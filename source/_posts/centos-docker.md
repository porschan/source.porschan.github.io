---
title: '[转] CentOS6.5 安装Docker'
date: 2018/7/26 9:35:27 
desc: chanchfieng.com
tags: [CentOS,docker,[转]]
---

### 运行docker Linux内核版本需要在3.8以上,针对centos6.5 内核为2.6的系统需要先升级内核.不然会特别卡 ###

在yum的ELRepo源中,有mainline（4.5）、long-term（4.4）这2个内核最新版本，考虑到long-term更稳定，会长期更新，所以选择这个版本。(在 http://hkg.mirror.rackspace.com/elrepo/kernel/el7/x86_64/RPMS/ 能查看最新内核版本)

1. 查看当前版本

```

	[root@localhost ~]# more /etc/issue
	
	CentOS release 6.5 (Final)
	Kernel \r on an \m
	
	[root@localhost ~]# uname -a
	
	Linux localhost.localdomain 2.6.32-431.el6.x86_64 #1 SMP Fri Nov 22 03:15:09 UTC 2013 x86_64 x86_64 x86_64 GNU/Linux

```

2.  升级内核

```

	1.导入public key

	rpm --import http://www.elrepo.org/RPM-GPG-KEY-elrepo.org

	2.安装ELRepo到CentOS-6.5中

	rpm -ivh http://www.elrepo.org/elrepo-release-6-8.el6.elrepo.noarch.rpm

	3.安装kernel-lt（lt=long-term）

	yum --enablerepo=elrepo-kernel install kernel-lt -y

	或者安装kernel-ml（ml=mainline）

	yum --enablerepo=elrepo-kernel install kernel-ml -y

	4.编辑grub.conf文件，修改Grub引导顺序,主要该default=0

	vim /etc/grub.conf
	# grub.conf generated by anaconda
	#
	default=0
	timeout=5
	splashimage=(hd0,0)/grub/splash.xpm.gz
	hiddenmenu
	title CentOS (4.4.121-1.el6.elrepo.x86_64)
	        root (hd0,0)
	        kernel /vmlinuz-4.4.121-1.el6.elrepo.x86_64 ro root=UUID=9955fe5f-5015-4582-a9d5-60fedd85d282 rd_NO_LUKS rd_NO_LVM LANG=zh_CN.UTF-8 rd_NO_MD SYSFONT=latarcyrheb-sun16 crashkernel=auto  KEYBOARDTYPE=pc KEYTABLE=us rd_NO_DM rhgb quiet
	        initrd /initramfs-4.4.121-1.el6.elrepo.x86_64.img
	title CentOS (2.6.32-431.el6.x86_64)
	        root (hd0,0)
	        kernel /vmlinuz-2.6.32-431.el6.x86_64 ro root=UUID=9955fe5f-5015-4582-a9d5-60fedd85d282 rd_NO_LUKS rd_NO_LVM LANG=en_US.UTF-8 rd_NO_MD SYSFONT=latarcyrheb-sun16 crashkernel=auto  KEYBOARDTYPE=pc KEYTABLE=us rd_NO_DM rhgb quiet
	        initrd /initramfs-2.6.32-431.el6.x86_64.img

	确认刚安装好的内核在哪个位置，然后设置default值（从0开始），一般新安装的内核在第一个位置，所以设置default=0

	重启，查看内核版本号

	[root@localhost ~]# reboot
	[root@localhost ~]# uname -r
	4.4.121-1.el6.elrepo.x86_64

	至此，CentOS6.5的内核升级完成，下面就可以安装docker了.

```

### 安装docker ###

直接使用命令: yum install docker-io   是会报错的!!!  提示:No package docker-io available.

需要先安装yum 源.

```

	yum -y install http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm

```

再执行

```

	yum install docker-io

```

启动docker

```

	service docker start

```

查看docker版本

```

	[root@localhost ]# docker version
	Client version: 1.7.1
	Client API version: 1.19
	Go version (client): go1.4.2
	Git commit (client): 786b29d/1.7.1
	OS/Arch (client): linux/amd64
	Server version: 1.7.1
	Server API version: 1.19
	Go version (server): go1.4.2
	Git commit (server): 786b29d/1.7.1
	OS/Arch (server): linux/amd64

```

查看docker日志.

```

	cat /var/log/docker

```

 至此，docker安装完成！！！

<div class="tip">
  感谢我从放心疯的博客[CentOS6.5下安装Docker](https://blog.csdn.net/qq_42679773/article/details/81066053)学习到centOS6.5安装Docker的过程，以下为未使用到的过程。
</div>

```

	三.卸载docker
	列出你安装过的包
	
	[root@localhost ~]# yum list installed | grep docker
	docker-io.x86_64                     1.7.1-2.el6                        @epel  
	删除软件包
	
	yum -y remove docker-io.x86_64
	删除镜像/容器等
	rm -rf /var/lib/docker
	四.docker加速配置，配置国内源（docker1.7修改方法）
	vi /etc/sysconfig/docker
	
	加入下面两行
	other_args="--registry-mirror=https://wbtwgo1e.mirror.aliyuncs.com"  
	OPTIONS='--registry-mirror=https://wbtwgo1e.mirror.aliyuncs.com'

```