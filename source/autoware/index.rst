Autoware
===========

* MIC-7700
    * ubuntu18.04 LTS
    * 32G RAM
    * GPU 1050Ti

一 环境搭建
------------

* `Wiki <https://gitlab.com/autowarefoundation/autoware.ai/autoware/-/wikis/home>`_


1 安装 ROS melodic
````````````````````

1.1 更新 ROS源地址
:::::::::::::::::::

.. code-block:: sh

        # # 更换阿里源, 网速快; 缺点, 当碰巧,阿里源正在和官方源同步的时段，会无法安装
        # sed -i 's/cn.archive.ubuntu.com/mirrors.aliyun.com/' /etc/apt/sources.list # X86 中文
        # sed -i 's/archive.ubuntu.com/mirrors.aliyun.com/' /etc/apt/sources.list    # X86 英文
        # sed -i 's/ports.ubuntu.com/mirrors.aliyun.com/' /etc/apt/sources.list      # arm 

        #  添加 科大ROS源
        sudo sh -c '. /etc/lsb-release && echo "deb http://mirrors.ustc.edu.cn/ros/ubuntu/ $DISTRIB_CODENAME main" > /etc/apt/sources.list.d/ros-latest.list'

        sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys F42ED6FBAB17C654
        sudo apt-get update

1.2 安装 ROS
:::::::::::::::::::

* 执行安装脚本

.. code-block:: sh
    
    sudo apt-get install curl --yes --allow-unauthenticated
    
    # 按照提示输入,当前用户密码
    curl -sSL https://raw.githubusercontent.com/my-rds-store/my_space/master/source/autoware/src/ros_instal.sh | bash


* 安装脚本的源码如下:

  .. literalinclude:: ./src/ros_instal.sh
     :language: bash



2 安装 CUDA 10.0
```````````````````

* Step 1 : revmoe nvidia

    .. code-block:: sh

        sudo apt-get remove --purge nvidia*

* Step 2 : Install cuda 10.0

    `下载链接: cuda-repo-ubuntu1804_10.0.130-1_amd64.deb <https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/cuda-repo-ubuntu1804_10.1.105-1_amd64.deb>`_

    .. code-block:: sh

        ## Install cuda
        ## https://developer.nvidia.com/cuda-toolkit-archive
        sudo dpkg -i cuda-repo-ubuntu1804_10.0.130-1_amd64.deb
        sudo apt-key adv --fetch-keys http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/7fa2af80.pub
        sudo apt-get update
        sudo apt-get install cuda-10-0

* Step 3 :  Install cuDNN 


    `先下载 cuDNN v7.5.0 (Feb 21, 2019), for CUDA 10.0 <https://developer.nvidia.com/rdp/cudnn-archive>`_ ;
    需要注册账号登录才能下载.

    .. code-block:: sh

        ## https://developer.nvidia.com/rdp/cudnn-archive
        ## cuDNN v7.5.0 (Feb 21, 2019), for CUDA 10.0

        sudo dpkg -i libcudnn7_7.5.0.56-1+cuda10.0_amd64.deb


* Step 4 :  添加环境变量 

    在 ${HOME}/.bash_aliases 添加

    .. code:: 

        ##################################
        #  CUDA
        ##################################
        export CUDA_HOME=/usr/local/cuda-10.0
        export PATH=$PATH:$CUDA_HOME/bin
        export LD_LIBRARY_PATH=${CUDA_HOME}/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}


    .. code-block:: sh

        source ${HOME}/.bash_aliases
        # 查看 CUDA 版本
        nvcc -V


* Step 5 : 重启系统 

    .. code-block:: sh

        sudo shutdown -r now



3 源码编译 Autoware
````````````````````````````````

* step 1 : Install Eigen

.. code-block:: sh

    wget http://bitbucket.org/eigen/eigen/get/3.3.7.tar.gz #Download Eigen

    mkdir eigen && tar --strip-components=1 -xzvf 3.3.7.tar.gz -C eigen #Decompress

    cd eigen && mkdir build && cd build && cmake .. && make && make install #Build and install

    cd && rm -rf 3.3.7.tar.gz && rm -rf eigen #Remove downloaded and temporary files

* step 2 : Build Autoware

**注意**

    .. code::

        NVIDIA Jetson AGX Xavier 
            需要 将libopencv-dev 版本 
            由 4.1.1-2-gd5a58aa75 降为 3.2.0+dfsg-4ubuntu0.1

            sudo apt-get install libopencv-dev=3.2.0+dfsg-4ubuntu0.1
                                      

.. code-block:: sh

    mkdir -p autoware.ai/src
    cd autoware.ai

    # Download
    wget -O autoware.ai.repos "https://gitlab.com/autowarefoundation/autoware.ai/autoware/raw/master/autoware.ai.repos?inline=false"

    vcs import src < autoware.ai.repos

    ## Install dependencies using rosdep.
    rosdep update
    rosdep install -y --from-paths src --ignore-src --rosdistro $ROS_DISTRO

    # With CUDA support
    AUTOWARE_COMPILE_WITH_CUDA=1 colcon build --cmake-args -DCMAKE_BUILD_TYPE=Release

    # Without CUDA Support
    # colcon build --cmake-args -DCMAKE_BUILD_TYPE=Release

* step 2 : Run Autoware

.. code-block:: sh

    cd autoware.ai
    source install/setup.bash
    roslaunch runtime_manager runtime_manager.launch


4. Docker 安装Autoware(整理中....)
`````````````````````````````````````

* 需要 在 autoware 用户下操作. 新建 autoware 用户

.. code::

    #/etc/sudoers 添加
    autoware      ALL=NOPASSWD:ALL
 

.. code-block:: sh

     git clone https://gitlab.com/autowarefoundation/autoware.ai/docker.git

     cd docker/generic

     mkdir ~/Autoware
    ./run.sh --ros-distro melodic 
    ./run.sh --ros-distro melodic --cuda off # 无cuda


   
* `问题: No protocol specified  <https://blog.csdn.net/Niction69/article/details/78480675>`_

.. code-block:: sh
    
    #　root 用户下
    xhost +


5. 学习资料
`````````````

* `autoware入门教程 <https://www.ncnynl.com/archives/201910/3402.html>`_

`二 LGSVL <https://www.lgsvlsimulator.com/docs/>`_
-----------------------------------------------------

.. code-block:: sh
 
    # ubuntu18.04 Install lgsvls imulator
    sudo apt install libcanberra-gtk-module libcanberra-gtk3-module # Failed to load module "canberra-gtk-module"
    sudo apt-get install libgtk2.0-0:i386 libglib2.0-0:i386 libgdk-pixbuf2.0-0:i386 # 待验证
    sudo apt-get install vulkan-utils # 解决: No supported renderes found, exiting 


.. code::

    Windows LGSVL地图及配置文件，下载保存路径为

     用户\AppData\Locallow\LG Silicon Valley Lab\LGSVL Simulator\


.. mdinclude:: ./md/autoware-json-example.md

.. code-block:: sh

    cp -rvf  ./src/autoware/simulation/lgsvl_simulator_bridge/*  \
             ./install/lgsvl_simulator_bridge/share/lgsvl_simulator_bridge/
    source install/setup.bash
    roslaunch runtime_manager runtime_manager.launch
    
    # start
    roslaunch rosbridge_server rosbridge_websocket.launch
   
--------

* `LGSVL Simulator python API 整理总结 ------ (待验证) <https://www.jianshu.com/p/9585cb18f0a6>`_
* `罗技 G29 方向盘 ------ (待验证) <https://www.jianshu.com/p/d314f70b26ba>`_

--------


三 问题整理
------------

（ 空 )

四 学习笔记
------------


.. code-block:: sh

    rosrun runtime_manager runtime_manager_dialog.py


使用GNSS进行定位
`````````````````

gpsd
::::::

gpsd是一个GPS的守护进程，用以侦听来自GPS接收器的位置信息，并将这些位置信息转换成一种简化的格式。这样就可以使用其他程序对这些数据进行分析并制作图表等。该软件包中有一个客户端，用以显示当前可见GPS卫星（如果有的话）的位置和速度。它也可以使用差分全球定位系统/ IP协议。

.. code-block:: sh

    sudo apt-get install gpsd gpsd-clients

 
* `Python gpsd bindings <https://www.perrygeo.com/python-gpsd-bindings.html>`_

----

* `How to use Android phone as GPS sensor in Linux <https://miloserdov.org/?p=3762>`_

  .. code-block:: sh
    
    systemctl stop    gpsd
    systemctl disable gpsd
    sudo shutdown -r now   # 需要关机重启，启动 启动 gpad -N .... 会报错。


    sudo apt-get install adb

    ###########
    cgps
    gpsmon

* `Warwalking With Linux and Android <https://pentasticweb.wordpress.com/2016/05/27/warwalking-with-linux-and-android/>`_
    * https://www.jillybunch.com/sharegps/nmea-usb-linux.html


gpsfake
:::::::::::::::

* 使用gpsfake模拟GPS数据

    .. code:: 

        1. 将假的gps数据存到文件中，命名为test.log.

               nc localhost 20175  >> test.log
               或者
               curl <phone ip>:port >> test.log

        2. ls /dev/pts,查看现在有什么设备。我的有三个，分别是0，1，ptmx。

        3. gpsfake -c 0.2 test.log  #  0.2秒 发送一条数据

        4. ls /dev/pts再次查看。这时候有四个了，分别是0,1,2,ptmx.

        5. cat /dev/pts/2. 就可以看到假的gps数据了。

        6. gpsd -F -D3 -N /dev/pts/2

        7 cgps 或者 gpsmon


    * `gpsd_client-Tutorials <http://wiki.ros.org/gpsd_client/Tutorials/Getting%20Started%20with%20gpsd_client>`_

    .. code-block:: sh 

        # 8. 
        rosrun gpsd_client gpsd_client _host:=localhost _port:=2947

        #9. 
        rostopic echo /fix
        
 `nmea_navsat_driver <https://wiki.ros.org/nmea_navsat_driver>`_
    * `run nmea_serial_driver <https://autoware.readthedocs.io/en/feature-documentation_rtd/DevelopersGuide/PackagesAPI/sensing/scripts.html>`_

    .. code-block:: sh 

       gpsfake -c 0.2 test.log  #  0.2秒 发送一条数据

       rosrun nmea_navsat_driver nmea_serial_driver _port:=/dev/pts/7 _baud:=4800

       rostopic list
       rostopic echo /fix
       rostopic echo /vel 
       rostopic echo /time_reference

gnss_localizer 
:::::::::::::::

https://github.com/autowarefoundation/autoware/issues/492


.. code-block:: sh

    find . -name "*.py" -or -name "*.yaml"| xargs grep -in plane
    find . -name "*.c*" -or -name "*.h*" -or -name "*.launch" -or -name "*.py" | xargs grep -in set_plane

    vim ./autoware/utilities/runtime_manager/scripts/computing.yaml +1281
    vim ./autoware/utilities/autoware_launcher/plugins/refs/nmea2tfpose.yaml +11

    vim ./autoware/core_perception/gnss_localizer/launch/fix2tfpose.launch +4
    vim ./autoware/core_perception/gnss_localizer/nodes/nmea2tfpose/nmea2tfpose_core.cpp +46

    vim ./autoware/common/gnss/src/geo_pos_conv.cpp +52


fix2tfpose
'''''''''''''''

.. code-block:: cpp

  pose_publisher = nh.advertise<geometry_msgs::PoseStamped>("gnss_pose", 1000);
  stat_publisher = nh.advertise<std_msgs::Bool>("/gnss_stat", 1000);
  ros::Subscriber gnss_pose_subscriber = nh.subscribe("fix", 100, GNSSCallback);


`路径跟踪基本配置 <https://qiita.com/hakuturu583/items/297adfd8ad0fa54d1a24>`_
````````````````````````````````````````````````````````````````````````````````

录制rosbag包
::::::::::::::::

.. code-block:: cpp

    rosbag record -O name.bag /points_raw

rosbag建图
::::::::::::::::

**Runtime Manager** 

* Setup  

.. code::

    TF -  x: 1.2, y: 0, z: 2 ;  这是 LIDAR 传感器在车身坐标系中的位置。 
                                设置 transform 是为了建立 LIDAR 坐标系
                                与车身坐标系的转换关系。
    Vehicle Model

* Computing 

.. code::

    ndt_mapping : 借助 NDT 算法实现 SLAM。
    ndt_mapping[app] : ref设定保存pcd文件的路径, 建图结束后 ,点击 `PCD OUTPUT` 保存pcd。


rviz，配置文件 Autoware/ros/src/.config/rviz/ndt_mapping.rviz。

建图不一定每次都成功，有时候 NDT 算法匹配的不好，地图可能很混乱。我们的经验是，在收集 LIDAR 数据的时候车辆*行驶慢一些*，如果建图不成功，就多尝试几次，每次都重新收集一次数据.


生成 Waypoint
::::::::::::::::

* Setup 

.. code::

    TF  -  x: 1.2, y: 0, z: 2
    Vehicle Model

* Map 

.. code::

  Point Cloud : 加载pcd
  TF : 加载 lgsvl-tf.launch

.. code-block:: xml

    <!-- lgsvl-tf.launch -->

    <launch>
    <node pkg="tf"  type="static_transform_publisher" name="world_to_map" args="0 0 0 0 0 0 /world /map 10" />
    <node pkg="tf"  type="static_transform_publisher" name="map_to_mobility" args="0 0 0 0 0 0 /map /mobility 10" />
    </launch>

* Sensing 

.. code::

    Point Downsampler -> voxel_grid_filter 

* Computing 

.. code::

    lidar_localizer -> ndt_matching : 注意，要在 app 中 initial pose，数值全为 0.

    autoware connector -> vel_pose_connect  这里是将 ndt 估计出的 pose 和 velocity 
                                              名字改为 current_pose, current_velocity，
                                              以便后续 pure-pursuit node 使用.

    waypoing_maker -> waypoint_saver : 设置好路径点文件的名字和保存路径。

航点导航
:::::::::

* Sensing 

.. code::

    Point Downsampler -> voxel_grid_filter 

* Computing 

.. code::

     lidar_localizer    -> ndt_matching : 注意，要在 app 中 initial pose，数值全为 0; 
                                              这是 NDT 点云匹配的初始位置
     autoware connector -> vel_pose_connect

* Mission Planning

.. code::

    * lane_planner -> lane_rule 
                   -> lane_stop 
                   -> lane_select

* Motion Planning

.. code::

    waypoing_maker -> waypoint_loader - 加载刚才生成的路径点文件
                   -> path_select

    waypoint_planner -> astar_void 
                     -> velocity_set

    waypoint_follower -> pure_pursuit 
                      -> twist_filter

    lattice_planner -> lattice_velocity_set  

使用YOLOv3进行检测
``````````````````

* `Running yolov3 detection in autoware <https://youtu.be/M5K2xc6ndtA>`_

.. raw:: html

    <iframe width="560" height="315" src="https://www.youtube.com/embed/M5K2xc6ndtA" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

Step 1: 安装Yolo3
::::::::::::::::::

* `安装Yolo  <https://www.ncnynl.com/archives/201911/3439.html>`_

Step 2: usb_cam
::::::::::::::::

.. code-block:: sh

    sudo apt install ros-melodic-cv-camera
    rosrun cv_camera cv_camera_node

    rostopic echo /cv_camera/image_raw

.. code-block:: bash

    mkdir -p usb_cam 
    cd usb_cam 

    #git clone https://github.com/bosch-ros-pkg/usb_cam src
    git clone https://github.com/ros-drivers/usb_cam.git src

    catkin_make 
    source devel/setup.bash 

    roscore  &
    source devel/setup.bash 
    roslaunch usb_cam usb_cam-test.launch

Step 3
::::::::::::::::

* Computing->Detection->vision_detector->vision_darknet_yolo3/[app]

.. image:: ./img/vision_darknet_yolo3/01.png
        :scale: 80%

.. image:: ./img/vision_darknet_yolo3/02.png
        :scale: 80%

* 打卡 Rviz

.. image:: ./img/vision_darknet_yolo3/03.png
        :scale: 80%

.. image:: ./img/vision_darknet_yolo3/04.png
        :scale: 100%

.. image:: ./img/vision_darknet_yolo3/6.png
        :scale: 60%

* How to use object detection package in Autoware 

.. raw:: html

  <iframe width="560" height="315" src="https://www.youtube.com/embed/rCSzirRForc" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

------------------

* `RoboSense-LiDAR <https://github.com/RoboSense-LiDAR/ros_rslidar.git>`_

.. code:: 

    $ git diff

    diff --git a/rslidar_pointcloud/launch/cloud_nodelet.launch b/rslidar_pointcloud/launch/cloud_nodelet.launch
    index 6f0869a..a3ef4e9 100644
    --- a/rslidar_pointcloud/launch/cloud_nodelet.launch
    +++ b/rslidar_pointcloud/launch/cloud_nodelet.launch
    @@ -15,6 +15,9 @@
         <param name="device_ip" value="$(arg device_ip)" />
         <param name="msop_port" value="$(arg msop_port)" />
         <param name="difop_port" value="$(arg difop_port)"/>
    +
    +    <!-- support autoware  -->
    +    <param name="frame_id" type="string" value="velodyne"/>
       </node>
     
       <node pkg="nodelet" type="nodelet" name="$(arg manager)_cloud"
    @@ -24,5 +27,7 @@
         <param name="angle_path" value="$(find rslidar_pointcloud)/data/rs_lidar_16/angle.csv" />
         <param name="channel_path" value="$(find rslidar_pointcloud)/data/rs_lidar_16/ChannelNum.csv" />
     
    +       <!-- support autoware  -->
    +       <remap from="rslidar_points" to="/points_raw"/>
       </node>
     </launch>

.. code-block:: sh

   rostopic echo /points_raw     | grep frame_id

--------------------

*  Jetson AGX Xavier

.. raw:: html
   :file: ./jetson_nvpmodel.html

.. code-block:: sh

    tegrastats

    sudo nvpmodel --query
    sudo nvpmodel -m 0  # 0 - MAXN ; 
                        # 1 - MODE_10W ;  默认
                        # 2 - MODE_15W ; 
                        # 3 - MODE_30W_ALL ; 
                        # 4 - MODE_30W_6CORE ; 
                        # 5 - MODE_30W_4CORE ; 
                        # 6 - MODE_30W_2CORE ; 


    sudo jetson_clocks --show
    
    suod -i  && echo 255 > /sys/devices/pwm-fan/target_pwm  # 风扇开到最大