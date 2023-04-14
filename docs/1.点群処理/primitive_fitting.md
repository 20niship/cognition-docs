# 点群の Geometric Primitiveへのフィッティング

## 直線

## 平面

## 

## 事前に平面がセグメンテーションされているとき
- PCA（主成分分析）によるアルゴリズム
- C++によるコーディング例

```cpp
#include <iostream>
#include <Eigen/Dense>
#include <Eigen/Eigenvalues>

using namespace Eigen;

int main(){
    // Define the 3D point cloud
    MatrixXd point_cloud(3, 5);
    point_cloud << 1, 2, 3, 4, 5,
                   2, 3, 4, 5, 6,
                   1, 1, 1, 1, 1;

    // Calculate the centroid of the point cloud
    Vector3d centroid = point_cloud.rowwise().mean();

    // Calculate the covariance matrix of the point cloud
    Matrix3d covariance = (point_cloud.colwise() - centroid) * (point_cloud.colwise() - centroid).transpose();

    // Calculate the eigenvalues and eigenvectors of the covariance matrix
    EigenSolver<Matrix3d> es(covariance);
    Vector3d eigenvalues = es.eigenvalues().real();
    Matrix3d eigenvectors = es.eigenvectors().real();

    // Extract the normal vector of the fitted plane from the eigenvector corresponding to the smallest eigenvalue
    Vector3d normal = eigenvectors.col(0);

    // Calculate the distance from the origin to the plane
    double d = -centroid.dot(normal);

    // Print the equation of the fitted plane
    std::cout << "Equation of the fitted plane: " << normal(0) << "x + " << normal(1) << "y + " << normal(2) << "z + " << d << " = 0" << std::endl;

    return 0;
}
```


## 球体
- 事前にセグメンテーションされているとき
  - https://programming-surgeon.com/script/sphere-fit/


## 円
- aaaaaa
- EKFがいいよ
  - https://www.kalmanfilter.net/JP/default_jp.aspx

## 円柱

### RANSACによるフィッティング

以下の処理を繰り返します
```
1. 点群の法線ベクトルは事前に計算されているものとする
2. 点群からランダムに２点選択する
3. 選択した２点が円柱の表面にあるとみなして、円柱のパラメータを計算する
4. 各点のうち、算出した円柱との距離が閾値以下のものを選択する。
5. 処理を 2 から繰り返し、最も含まれる点の数が多かったパラメータを選択する
```
- [円柱のRANSACアルゴリズムについて](http://visitlab.jp/pdf/CylinderSegPCL.pdf)

実装はこれが読みやすい → https://github.com/leomariga/pyRANSAC-3D/blob/master/pyransac3d/cylinder.py

### その他１（ロバストらしい）
- [ROBUST CYLINDER FITTING IN THREE-DIMENSIONAL POINT CLOUD DATA](https://isprs-archives.copernicus.org/articles/XLII-1-W1/63/2017/isprs-archives-XLII-1-W1-63-2017.pdf )

### その他２（高速な手法、RC22のラゴリ検出）

```
- 1. 事前に法線は算出されているものとする
- 2. 事前にオブジェクト（円柱）ごとにセグメンテーションされているものとする

```

## 配管
- https://stackoverflow.com/questions/64911820/fit-curve-spline-to-3d-point-cloud
  -  ![](https://i.stack.imgur.com/WiwdV.png)

## 平面


## see also:
- convex hull
- 


# 参考文献

- https://core.ac.uk/download/pdf/304641014.pdf