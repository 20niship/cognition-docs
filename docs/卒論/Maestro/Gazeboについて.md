
Gazeboとは
----------

*   公式サイト: [http://gazebosim.org](http://gazebosim.org)
*   オープンソース(Apacheライセンス)の3Dロボットシミュレータ
*   リアルタイム性を重視
*   OGREを利用した高品質なグラフィック
*   複数の物理エンジンを切り替え可能(ODE,Bullet,Simbody,DART)
*   カメラ、レーザレンジファインダなどのセンサシミュレーション



ODEとの違い(?)
------------

*   モデルが豊富
    *   universal robot, turtle bot, shadow hand, spheroなどのモデルがすでにあり、誰でも利用可能
    *   Gazeboは精密な接触判定がうまくできないので、２足歩行ロボットなどのシミュレーションには向かない



Gazeboの使い方
------------

*   Ubuntuのバージョンにあったものを入れる
*   Gazeboの公式サイトのチュートリアル([http://gazebosim.org/tutorials](http://gazebosim.org/tutorials)）を参照
*   ODEの出村先生のページにもROS+gazeboのページがある([http://demura.net/lecture/13049.html](http://demura.net/lecture/13049.html)）



### ROSとの連帯


#### まずROSをインストールする
*   インストール方法(例：ubuntu16.04,ros:kinetic [http://wiki.ros.org/kinetic/Installation/Ubuntu](http://wiki.ros.org/kinetic/Installation/Ubuntu) )
    *   ROSのインストール設定
        *   sources.listを設定する→鍵の設定→パッケージを最新のものにアップデート→ROS のフルセット版をインストール
        *   sudo sh -c 'echo "deb [http://packages.ros.org/ros/ubuntu](http://packages.ros.org/ros/ubuntu) $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'
        *   sudo apt-key adv --keyserver hkp://ha.pool.sks-keyservers.net:80 --recv-key 421C365BD9FF1F717815A3895523BAEEB01FA116
        *   sudo apt-get update
        *   sudo apt-get install ros-kinetic-desktop-full
    *   rosdepの初期化
        *   sudo rosdep init
        *   rosdep update
    *   rosinstallの準備
        *   sudo apt-get install python-rosinstall
    *   ROS環境設定：環境変数の設定
        *   echo "source /opt/ros/kinetic/setup.bash" >> ~/.bashrc
        *   source ~/.bashrc
*   Ubuntuのバージョンによって入れるROSのバージョンも異なるので確認してから
*   使いたいロボットのモデルが特定のROSのバージョンにしか対応していない場合があるのでその確認も必要



#### ROSサンプルプログラムのディレクトリを用意してみる
*   mkdir -p ~/catkin_ws/src
*   cd ~/catkin_ws/src
*   catkin_create_pkg beginner_tutorials std_msgs geometry_msgs rospy roscpp message_generation message_runtime
*   cd ..
*   catkin_make
*   source ~/catkin_ws/devel/setup.bash
*   /.bashrcにsource ~/catkin_ws/devel/setup.bashを追記しておく
    



#### Gazeboをインストールする
*   Gazeboの公式サイトのConnect to ROS([http://gazebosim.org/tutorials?cat=connect_ros](http://gazebosim.org/tutorials?cat=connect_ros)）参照
    *   日本語もある([https://cir-kit.github.io/blog/2015/02/14/gazebo-ros-overview/](https://cir-kit.github.io/blog/2015/02/14/gazebo-ros-overview/)）
*   terminalにgazeboと打ち込んでGazeboが立ち上がるのを確認する
*   gazebo_ros_pkgsのインストール
    *   sudo apt-get install ros-kinetic-gazebo-ros-pkgs ros-kinetic-gazebo-ros-control



#### 自分に必要なパッケージのインストール方法(例：[https://github.com/ros-simulation/gazebo_ros_demos](https://github.com/ros-simulation/gazebo_ros_demos) )
```
cd ~/catkin_ws/src/
git clone [https://github.com/ros-simulation/gazebo_ros_demos.git](https://github.com/ros-simulation/gazebo_ros_demos.git)
cd ..
catkin_make
```


#### 実際に使うとき
*   roscoreしてからlaunchファイルをroslaunchする



### pygazeboを使用（非推奨）
*   ROSを介さずに直接pythonで通信を行って操作
*   シミュレーションのリセットができなかったり、使えないセンサ値があったりする
*   ソース自体にバグがあり、修正しないと動かない