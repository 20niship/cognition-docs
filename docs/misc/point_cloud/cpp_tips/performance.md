# 高速化に関するあれこれ

```cpp
#pragma omp parallel for
```

## Magi::Vectorについて
- VectorN : 遅い
    - おそらく代入に時間がかかっている
- コンストラクタが遅いということがわかった
    - 可変長テンプレートを使っているコンストラクタは遅いが、_Vec({e1, e2, e3}),のようにinitializer_list経由だと速い
    - でも一番早いのはループ外部にconstの変数として定義して、それを使うやり方だった
- forで各要素代入を回しているので、オーバーヘッドがかかっている？特殊化する必要がありそう
    - そんなことなかった。-O3コンパイルオプションで試すと自動で展開される？
    - 生配列、Vector3（ USE_CUSTOM_VECTOR3はON）両方で試したが同じくらいの速度

```cpp
// 現状コード
template<typename T, unsigned int LEN>
class _Vec {
public:
	T value[LEN];
	_Vec() { reset(); }
	_Vec(std::initializer_list<T> init) { MY_ASSERT(init.size() >= LEN);int idx=0; for(auto i=init.begin(); i<init.end(); i++){ value[idx] = *i; idx++; } }

    template<class... Args>
    _Vec(Args... args){
        constexpr std::size_t size = sizeof...(Args);
        static_assert(size == LEN);
        std::vector<T> tt{ args... };
        for(int i=0; i<LEN; i++){value[i] = tt[i];}
    }
```

### 改善
```cpp
float vec1[3] = {10, 20, 30};
float vec2[3] = {1, 2, 3};
#define N 150000000
for(int i=0; i<N; i++){
    vec1[0] = vec1[0] + vec2[0]/i + vec2[0];
    vec1[1] = vec1[1] + vec2[1]/i + vec2[0];
    vec1[2] = vec1[2] + vec2[2]/i + vec2[0];
}
```
200msくらい

```cpp
template<typename T>
struct Vector3 {
	// T x, y, z;
	T v[3];
    Vector3(){}
	Vector3(float x_, float y_, float z_) { v[0] = x_; v[1] = y_; v[2]= z_; }
	template<typename U> inline Vector3 operator+(const Vector3<U>& a)const{ return Vector3<T>(v[0] + a.v[0], v[1]+a.v[1], v[2]+a.v[2]  ); }
	template<typename U> inline Vector3 operator/(const Vector3<U>& a)const{ return Vector3<T>(v[0]/a.v[0], v[1]/a.v[1], v[2]/a.v[2]  ); }
	template<typename U> inline Vector3 operator+(const U a)const{ return Vector3<T>(v[0] + a, v[1] + a, v[2] + a); }
	template<typename U> inline Vector3 operator/(const U a)const{ return Vector3<T>(v[0]/a, v[1] / a, v[2] / a); }
};

Vector3<float> vec21, vec22;
const Vector3<float> v3 = {1, 2, 3};
for(int i=0; i<N; i++){
    vec21 = vec21+v3 + v3 + v3 + v3;
}
```

## 大きめの配列の確保
```cpp
std::vector<T> arr;
 // 動くが遅い
 // 感触的には T arr[N] の生配列の10倍くらい　−−このとき 最適化OFF`-O0`だったことが原因説大
 // サイズを変えると毎回コピーが発生するので更に遅い
 //  begin(), end()で取得したイテレーターからアクセスするとさらに遅かった


T arr[BIG_NUMBER];
// BIG_NUMBERが大きくなると（e.g. 786436) セグフォが起こる
// ので、そのような場合はmallocで動的確保する
```

## ムーブセマンティクスとか
こういう構造体があって
```cpp
struct A{
    std::string s;
    A() : s("test") {}
    A(const std::string & ss):s(ss) {}
    A(const A& o) : s(o.s) { std::cout << "move failed!\n"; }
    A(A&& o) : s(std::move(o.s)) { }
    A& operator=(const A& other){
         s = other.s;
         return *this;
    }
    A& operator=(A&& other){
         s = std::move(other.s);
         return *this;
    }
};
```
以下の3通り（普通にする、st::move使う、memcpyでコピー）だと処理時間は101 : 85 : 75くらいになった。（`-O3`で g++-11 ビルド）
```cpp
uiVector<A> hoge;
start = std::chrono::system_clock::now();
for(int i=0; i<N; i++){ hoge.push_back(A("aaaaaaaa")); }
```

VS

```cpp
uiVector<A> hoge;
for(int i=0; i<N; i++){ hoge.push_back(std::move(A("aaaaaaaa"))); }
```

VS

```cpp
uiVector<A> hoge;
const A a("AAAAA");
for(int i=0; i<N; i++){ hoge.push_back_ptr(&a); }
```

## GPU使う？
今回の場合は使わないほうが早い（データ移動にまあまあ時間かかる）という結論に達した。

`opencl_depth_calc.cpp`を実行するとわかる。640ｘ480のuint8_tとかのデータを移動するのに数msかかる



