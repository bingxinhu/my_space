##############
Vagrant
##############

************************************
Vagrant 插件
************************************

* `科大Rubygems源使用 <http://mirrors.ustc.edu.cn/help/rubygems.html>`_

.. code-block:: sh

    #  查看 插件
    $ vagrant plugin list

    $ vagrant plugin install vagrant-share --plugin-clean-source --plugin-source https://gems.hashicorp.com

    $ yum install libvirt-devel 
    $ vagrant plugin install vagrant-libvirt --plugin-clean-source --plugin-source  https://mirrors.ustc.edu.cn/rubygems/
    $ vagrant plugin install vagrant-mutate --plugin-clean-source --plugin-source  https://mirrors.ustc.edu.cn/rubygems/
    $ vagrant plugin install vagrant-rekey-ssh --plugin-clean-source --plugin-source  https://mirrors.ustc.edu.cn/rubygems/

************************************
Vagrant-libvirt 
************************************

* `Install vagrant-libvirt <https://github.com/vagrant-libvirt/vagrant-libvirt/blob/master/README.md#installation>`_

* `使用Vagrant部署kvm虚拟化 <https://huataihuang.gitbooks.io/cloud-atlas/virtual/vagrant/vagrant_libvirt_kvm.html>`_

 
初始化
============
 
.. code-block:: sh

    vagrant init centos/7 

更名存储池
============
 
Vagrant会尝试使用一个名为default的存储池，
如果这个default存储池不存在就会尝试在/var/lib/libvirt/images上创建这个defualt存储池。
CentOS7默认安装libvirt环境，已经在/var/lib/libvirt/images目录上创建了名为images的存储池，
所以需要修改Vagrantfile配置文件中定义provider。

.. code:: 

    Vagrant.configure("2") do |config|
      config.vm.provider "libvirt" do |libvirt|
        libvirt.storage_pool_name = "images"
      end
    end

启动
==============

为告知Vagrant主机使用的provider是vagrant-libvirt, 而不是默认的virtualbox，
设置环境变量（这样vagrant up命令就不需要加上--provider libvirt参数）

.. code-block:: sh

    vagrant up --provider libvirt
    # or 
    export VAGRANT_DEFAULT_PROVIDER=libvirt ; vagrant up


FAQ
==============

* https://www.jianshu.com/p/44a48ae9db08
* https://github.com/AJNOURI/COA/issues/68

***********************
Vagrant box Init  
***********************

.. code-block:: sh

    vagrant init ubuntu/trusty64
    vagrant init my-box https://boxes.company.com/my.box
    vagrant init my-box ../mybox_storage/my.box

    # eg :
    $ vagrant init  bionic-server-cloudimg-amd64     https://mirrors.shu.edu.cn/ubuntu-cloud-images/bionic/20180802/bionic-server-cloudimg-amd64-vagrant.box
    $ vagrant init  CentOS-7-x86_64-Vagrant-1805_01  https://mirrors.ustc.edu.cn/centos-cloud/centos/7/vagrant/x86_64/images/CentOS-7-x86_64-Vagrant-1805_01.VirtualBox.box
    $ vagrant init  Fedora-Cloud-Base-Vagrant-28-1.1 http://mirrors.163.com/fedora/releases/28/Cloud/x86_64/images/Fedora-Cloud-Base-Vagrant-28-1.1.x86_64.vagrant-virtualbox.box

************************************
Vagrant box Add
************************************

Vagrant会将所下载的box,保存到 `~/.vagrant.d/boxes` 目录下

.. code-block:: sh

    $ vagrant box add --name mybox ../mybox_storage/my.box
    $ vagrant box add        mybox http://someurl.com/mybox.box
    $ vagrant box add --name mybox http://someurl.com/mybox.box
    
    # eg :
    $ vagrant box add --name bionic-server-cloudimg-amd64     https://mirrors.shu.edu.cn/ubuntu-cloud-images/bionic/20180802/bionic-server-cloudimg-amd64-vagrant.box
    $ vagrant box add --name CentOS-7-x86_64-Vagrant-1805_01  https://mirrors.ustc.edu.cn/centos-cloud/centos/7/vagrant/x86_64/images/CentOS-7-x86_64-Vagrant-1805_01.VirtualBox.box
    $ vagrant box add --name Fedora-Cloud-Base-Vagrant-28-1.1 http://mirrors.163.com/fedora/releases/28/Cloud/x86_64/images/Fedora-Cloud-Base-Vagrant-28-1.1.x86_64.vagrant-virtualbox.box


Vagrantbox.ex
=================

 * http://www.vagrantbox.es/

 * `vagrant box cloud <https://app.vagrantup.com/boxes/search>`_

Base box download
==================

* `Ubuntu cloud <https://cloud-images.ubuntu.com/>`_
    
    .. code::

        https://mirrors.ustc.edu.cn/ubuntu-cloud-images/server/server/bionic/20180802/bionic-server-cloudimg-amd64-vagrant.box
        https://mirrors.shu.edu.cn/ubuntu-cloud-images/bionic/20180802/bionic-server-cloudimg-amd64-vagrant.box

* `Centos cloud <https://cloud.centos.org/centos/7/vagrant/x86_64/images/>`_

    .. code:: 
    
        https://mirrors.ustc.edu.cn/centos-cloud
        https://mirrors.ustc.edu.cn/centos-cloud/centos/7/vagrant/x86_64/images/CentOS-7-x86_64-Vagrant-1805_01.VirtualBox.box

* `Fedora cloud <https://alt.fedoraproject.org/cloud/>`_
    .. code::

        http://mirrors.163.com/fedora/releases/28/Cloud/x86_64/images/Fedora-Cloud-Base-Vagrant-28-1.1.x86_64.vagrant-virtualbox.box
        https://mirrors.ustc.edu.cn/fedora/releases/28/Cloud/x86_64/images/Fedora-Cloud-Base-Vagrant-28-1.1.x86_64.vagrant-virtualbox.box
        https://mirrors.tuna.tsinghua.edu.cn/fedora/releases/28/Cloud/x86_64/images/Fedora-Cloud-Base-Vagrant-28-1.1.x86_64.vagrant-virtualbox.box
        https://mirrors.aliyun.com/fedora/releases/28/Cloud/x86_64/images/Fedora-Cloud-Base-Vagrant-28-1.1.x86_64.vagrant-virtualbox.box
        https://mirrors.shu.edu.cn/fedora/releases/28/Cloud/x86_64/images/Fedora-Cloud-Base-Vagrant-28-1.1.x86_64.vagrant-virtualbox.box


************************************
基于Virtual box 创建Vagrant box
************************************

* `基于Virtual box 创建Vagrant box <http://ebarnouflant.com/posts/7-convert-a-virtualbox-ova-vm-into-a-vagrant-box>`_

.. code-block:: sh

    # virtualBox 导入虚拟机
    $ VBoxManage import ./UCS-Virtualbox-Demo-Image.ova --vsys 0 --eula accept                                                                                                                                   
    # 查看虚拟机 id
    $ vboxmanage lisb vms

    "UCS 4.1" {acef4c0a-35be-4640-a214-be135417f04d}
    You can now package that VM as a Vagrant box:

    # 基于虚拟机 id 生成 vagrant box
    $ vagrant package --base acef4c0a-35be-4640-a214-be135417f04d --output UCS.box   


************************************
打包  Vagrant box
************************************

.. code-block:: sh
    
    # 打包成box
    $ vagrant package  --output newBox.box          
    # 重新打包 box
    $ vagrant box repackage <name>          <provider> <version>
    $ vagrant box repackage ubuntu/trusty64 virtualbox 20180330.0.0

************
provison
************

**provison并不会每次都执行，只有在这三种情况下provision才会运行：**

.. code-block:: sh

   # 1. 首次执行vagrant up
   $  vagrant up

   # 2. 执行
   $ vagrant provision

   # 3. 执行 
   $ vagrant reload --provision


************************
Vagrant Snapshot
************************


.. code-block:: sh

   $ vagrant snapshot --help

     
   $ vagrant snapshot list    "snapshot_name"
   $ vagrant snapshot save    "snapshot_name"  # 创建快照
   $ vagrant snapshot delete  "snapshot_name"  # 删除快照
   $ vagrant snapshot pop     "snapshot_name"
   $ vagrant snapshot push    "snapshot_name"
   $ vagrant snapshot restore "snapshot_name"  # 从快照还原

     

*************
Vagrantfile  
*************

* `vagrantfile examble  <https://github.com/hugsy/modern.ie-vagrant/blob/master/Vagrantfile>`_
* `vagrantfile examble2 <https://github.com/patrickdlee/vagrant-examples/blob/master/example6/Vagrantfile>`_

.. code:: 
    
    config.vm.box = "mc_termian_test"

    # The url from where the 'config.vm.box' box will be fetched if it
    # doesn't already exist on the user's system.

    config.vm.box_url = "../boxs/mc_termianl.box"
    config.ssh.username = 'root'
    config.ssh.password = 'rootroot'

    # 挂在目录
    config.vm.synced_folder "../data", "/vagrant_data"

    config.vm.provider "virtualbox" do |vb|
      # Display the VirtualBox GUI when booting the machine
      vb.gui = true

      vb.name = "vagrent_ubuntu14"

      # Customize the amount of memory on the VM:
      vb.memory = "1024"
      vb.cpus = 4

      # enable hardware virtualization technology
      vb.customize ["modifyvm", :id, "--pae",      "on"]
      vb.customize ["modifyvm", :id, "--hwvirtex", "on"]  
      vb.customize ["modifyvm", :id, "--vtxvpid",  "on"]
      vb.customize ["modifyvm", :id, "--vtxux",    "on"]

      ## Remote display (VRDP support)
      # vb.customize ["modifyvm", :id, "--vrde", "on"]
      # vb.customize ["modifyvm", :id, "--vrdeport", "3940"] # change here to a free port,fefault :3389

    end

    # 网络
    # config.vm.network "public_network", ip: "192.168.2.176" , bridge: "en0"

************
set proxy   
************

.. code-block:: sh

    # Install proxyconf:
    $ vagrant plugin install vagrant-proxyconf

.. code-block:: sh

    #Configure your Vagrantfile:
    config.proxy.http     = "http://yourproxy:8080"
    config.proxy.https    = "http://yourproxy:8080"
    config.proxy.no_proxy = "localhost,127.0.0.1"


********************
about mc_termianl   
********************

.. code-block:: sh

    # Install VirtualBox Guest Additions
    wget -O /etc/apt/sources.list  http://mirrors.163.com/.help/sources.list.trusty
    apt-get install -y gcc make perl
    apt-get clean
    mount /dev/cdrom /media/cdrom
    cd /media/cdrom
    ./VBoxLinuxAdditions.run 


*******
Docs   
*******

* `vagrant docs <https://www.vagrantup.com/docs/index.html>`_
* `gitbook vagrant  <https://ninghao.gitbooks.io/vagrant/content/>`_
* `Ansible中文权威指南 <http://www.ansible.com.cn/index.html>`_
    

**********
常见问题  
**********

* `vagrant 启动时报错failed to create the raw output file <https://my.oschina.net/chan17/blog/1785293>`_




参考
====

* `vagrant with guis and windows <https://www.phparch.com/2015/01/vagrant-with-guis-and-windows/>`_
* `Vagrant 入门 <https://www.cnblogs.com/davenkin/p/vagrant-virtualbox.html>`_

* http://blog.csdn.net/hel12he/article/details/51069269

----

* https://coderwall.com/p/ozhfva/run-graphical-programs-within-vagrantboxes


