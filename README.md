# blog
private blog
2018.5.2
使用virtualbox搭建laravel环境遇到的问题(virtualbox5.2.8+CentOS7)
一、virtualbox连接外网:
  1.virtualbox软件自身要做好网卡相关配置,具体配置在virtualbox管理器页面设置->网络中(注意一定要在虚拟机启动前修改,如果已经启动要shutdown now,
  然后再进行设置,再次启动后才会生效),网卡连接方式要设置为网络地址转换(NAT),然后点击高级->端口转发,设置端口转发规则主机端口设置为大于或等于10000
  (原因主要是系统程序会占用10000以前的端口所以不要轻易使用,除非已经在cmd窗口中查询过netstat -ano|findstr 端口号,没有被占用才能使用,事实上即使
  是10000以后的端口号最好也查询过,确认没有被占用才能使用),子系统端口设置为22,设置端口转发规则主要是为xshell连接服务.
  这样设置可以连接外网,但是本地windows无法ping通虚拟机,这也意味着xshell依旧无法连接.ping通虚拟机要设置网卡2,连接方式为仅主机(Host-Only)网络即可.
  2.接下来运行虚拟机,cd /etc/sysconfig/network-scripts/,然后会发现有至少两个ifcfg-XXX文件(ol和enXXX),运行ip addr命令,查看虚拟机网卡设置可以
  看到网卡的名称与XXX一致,vi ifcfg-enXXX文件,BOOTPROTO=dhcp(动态ip),ONBOOT=yes(开机自动启动网卡),:wq退出,systemctl restart network.service
  重启network服务,再次查询ip addr可以看到XXX网卡已经启动,ping baidu.com,能ping通表示设置成功.cp一份ifcfg-enXXX文件,修改HWADDR为host-only中
  设置好的MAC地址,修改BOOTPROTO为static,添加IPADDR为以cmd中ipconfig/all查询到的virtualbox host-only network的ip为网关的IP地址,即修改ip地址
  最后8位,注释掉UUID,NAME和DEVICE修改一致可以命名为ethX,保存退出后,修改文件名称为ifcfg-ethX,再次systemctl restart network.service,运行ip addr
  查看ethX是否启动.然后在windows的cmd窗口ping网卡设置的ip.如果发现重启network服务的时候有错误,可能是由于virtualbox没有启用网络连接,在设备->网络
  中可以启用.
2018.5.3
一、virtualbox突然无法启动昨天已经安装好的Centos7,报错内容为Error relaunching VirtualBox VM Process: 5,google了一下马上就找到解决方法,
是杀毒软件的问题,只要关闭杀毒软件即可.原文地址:http://letusexplain.blogspot.com/2015/08/solved-error-relaunching-virtualbox-vm.html
