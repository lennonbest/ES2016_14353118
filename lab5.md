#ROS配置与Cartographer体验
##0.配置环境
###本次配置实验所用的环境为ubuntu14.04， 具体流程主要参照以下文章：
ROS配置</br>
    http://wiki.ros.org/jade/Installation/Ubuntu </br>
Cartographer配置 </br>
    https://google-cartographer-ros.readthedocs.io/en/latest/ http://www.cnblogs.com/hitcm/p/5939507.html
##1.ROS Installation
###1.1 Setup your sources.list
    sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/rosewater.list'
###1.2 Set up your keys
    sudo apt-key adv --keyserver hkp://ha.pool.sks-keyservers.net:80 --recv-key 0xB01FA116
###1.3 Installation
####Step 0.Make sure your Debian package index is up-to-date:
    sudo apt-get update
####Step 1.Desktop-Full Install:
    sudo apt-get install ros-jade-desktop-full
####Step 2.Desktop Install:
    sudo apt-get install ros-jade-desktop
####Step 3.ROS-Base:
    sudo apt-get install ros-jade-ros-base
####Step 4.Individual Package:
    sudo apt-get install ros-jade-PACKAGE sudo apt-get install ros-jade-slam-gmapping apt-cache search ros-jade
###1.4 Initialize rosdep
    sudo rosdep init rosdep update
###1.5 Environment setup

    echo "source /opt/ros/jade/setup.bash" >> ~/.bashrc source ~/.bashrc source /opt/ros/jade/setup.bash
###1.6 Getting rosinstall
    sudo apt-get install python-rosinstall
##2.Tips for ROS Installation
至此， ROS的配置就算完成了， 跑完最后一条指令， 没什么特别明显的结果显示， 没有出现error就表明配置成功了。 在跑前面几条指令的时候可能你会看到有warning,或者“This likely means the installation is broken"之类的， 只要程序不停， 就继
续跑下一条， 停了的话就重跑前一条， 不要怕。
##3.Cartographer配置1.0
###3.1Cartographer ROS Integration
####Step 0.前面提到本次配置参考了两篇文章， 第一篇的方法主要是用来做前期准备， 跑Installation的时候往往是不行的， 所以
需要第二篇文章的方法来填坑
####Step 1.Install wstool and rosdep
    sudo apt-get update
	sudo apt-get install -y python-wstool python-rosdep ninja-build
####Step 2.Create a new workspace in 'catkin_ws'
    mkdir catkinws cd catkinws wstool init src
####Step 3.Merge the cartographer_ros.rosinstall file and fetch code for dependencies
    wstool merge -t src https://raw.githubusercontent.com/googlecartographer/cartographerros/master/cartographerros.rosinstall wstool update -t
    src
####Step 4.Install deb dependencies
    rosdep init rosdep update rosdep install --from-paths src --ignore-src --rosdistro=${ROS_DISTRO} -y
####Step 5.Build and install
    catkinmakeisolated --install --use-ninja source install_isolated/setup.bash
####Step 6.注意， 跑到上一步的时候就会报错， 没法继续了， 第一篇文章的作用就到此为止了， 把刚刚用到的catkinws文件夹删
除， 然后新建一个catkinws文件夹，
    rm -rf catkinws mkdir catkinws
##4.Cartographer配置2.0
###4.1安装所有依赖项
    sudo apt-get install -y google-mock libboost-all-dev libeigen3-dev libgflags-dev libgoogle-glog-dev liblua5.2-dev libprotobuf-dev
    libsuitesparse-dev libwebp-dev ninja-build protobuf-compiler python-sphinx ros-indigo-tf2-eigen libatlas-base-dev libsuitesparse-dev
    liblapack-dev
###4.2安装所有依赖项
创建ceres-solver-1.11.0/build文件夹
    git clone https://github.com/hitcm/ceres-solver-1.11.0.git cd ceres-solver-1.11.0/build 
	cmake .. 
	make –j
make-j这一步我是执行不了的， 博主提出了解决办法， 不跑make -j命令 换成make
    make sudo make install
###4.3安装Cartographer
    mkdir cartographer cd cartographer mkdir build git clone https://github.com/hitcm/cartographer.git cd cartographer/build cmake .. -G Ninja
    ninja ninja test sudo ninja install
###4.4安装cartographer_ros
    cd catkinws mkdir src git clone https://github.com/hitcm/cartographerros.git cd .. catkin_make
###4.5测试
####Step 1.下载2D测试数据包
    wget -P ~/Downloads https://storage.googleapis.com/cartographer-public-data/bags/backpack2d/cartographerpaperdeutschesmuseum.bag
####Step 2.处理ROS的catkin_ws配置问题
Note 1：</br>
    rospack profile echo "source ~/catkin_ws/devel/setup.bash" >> ~/.bashrc
</br>Note 2：</br>
    source ~/catkin_ws/devel/setup.bash rospack profile
</br>Note 3：</br>
	`gedit ~/.bashrc` 
</br>在该文件末尾添加以下代码</br>
    export ROSPACKAGEPATH=$ROSPACKAGEPATH:/home/siat/ccny/ccnyvision:/home/siat/catkinws/src export ROSHOSTNAME=localhost
	export ROSMASTER_URI=http://localhost:11311
####Step 3.测试
    roslaunch cartographerros demobackpack2d.launch bagfilename:=${HOME}/Downloads/cartographerpaperdeutsches_museum.bag
###4.6测试结果