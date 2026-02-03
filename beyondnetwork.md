# RouterOS系统使用比扬云

以下所有操作均在routeros 7.21.2 Stable版本测试通过，其它版本基本方法一样，部分地方可能略有不同，参考并适当修改即可

## 一、开启container

在System-Packages中查看是否已安装container，如果没有安装，选中container点右边的Enable，重启ros安装

![img1](https://github.com/lhbill/myfile/blob/main/01.jpg)

使用New Terminal（以下简称Term），运行命令：system/device-mode/print

如果其中显示了：container: yes，直接进行步骤二

否则，执行命令：/system/device-mode/update container=yes

如果你是ros硬路由，5分钟之内拔插电源重启设备，注意必须拔电源，不能直接重启；

如果你是软路由，以PVE系统为例，先停止该虚拟机（注意不能用关机），再开机启动

再运行system/device-mode/print，会显示container: yes

![img2](https://github.com/lhbill/myfile/blob/main/02.jpg)

## 二、创建一个veth

在Interfaces VETH中，创建一个veth1，配置一个ip，并添加到网桥中

![img3](https://github.com/lhbill/myfile/blob/main/03.jpg)

![img4](https://github.com/lhbill/myfile/blob/main/04.jpg)

也可以在Term中使用命令：

/interface/veth/add name=veth1 address=192.168.2.5/24 gateway=192.168.2.1
/interface/bridge/port add bridge=bridge1 interface=veth1

注意其中的gateway设为ROS的ip，veth1设置一个内网空余的ip，bridge改为自己的内网网桥名称

## 三、加载并运行比扬云容器

在Term中使用命令：/container/add remote-image=beyondnetwork-registry.cn-shenzhen.cr.aliyuncs.com/beyondnetwork/edge interface=veth1 envlist=beyondnetwork start-on-boot=yes

等待容器加载完成

![img5](https://github.com/lhbill/myfile/blob/main/05.jpg)

![img6](https://github.com/lhbill/myfile/blob/main/06.jpg)

在container的Envs中增加一条环境变量，list设为与上面envlist名称一致，Key设为EDGE_KEY，Valve设为站点标识

![img7](https://github.com/lhbill/myfile/blob/main/07.jpg)

Term命令方式如下，把123替换为站点标识即可：/container/envs/add list=beyondnetwork key=EDGE_KEY value=123

然后运行镜像就可以了
/container/print 命令确保 container 已经添加，可以查看运行状态
