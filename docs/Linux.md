- [Linux常用命令](#linux常用命令)

- [JDK配置](#jdk配置)

- [Tomcat配置](#tomcat配置)

- [MySQL配置](#mysql配置)

  

# Linux

**Linux根据原生程度，分为两种：**

1. **内核版本：** Linux不是一个操作系统，严格来讲，Linux只是一个操作系统中的内核。内核是什么？内核建立了计算机软件与硬件之间通讯的平台，内核提供系统服务，比如文件管理、虚拟内存、设备I/O等；

2. **发行版本：** 一些组织或公司在内核版基础上进行二次开发而重新发行的版本。Linux发行版本有很多种（ubuntu和CentOS用的都很多，初学建议选择CentOS），如下图所示：

   ![img](imgs/1645efa7048fd018)

## Linux常用命令

- `ifconfig`：打印网卡信息(IP)
- `reboot`：重启
- `cd`：切换目录
- `pwd`：当前所在位置
- `ls(ll)`：查看目录信息(详细信息)
- `mkdir 目录名称`：创建文件夹
- `rm [-rf] 目录`：删除
- `cp [-r] 目录名称 目录拷贝的目标位置`：拷贝文件(夹) -r代表递归拷贝
- `mv 目录名称 目录的新位置`：重命名(剪切)
- `tar -zcvf`：打包并压缩文件
  - z：调用gzip压缩命令进行压缩；c：打包文件；v：显示运行过程；f：指定文件名
- `vi(vim) `：创建或编辑文件(高级编辑)
  - 按Insert或I键进入编辑状态
  - 按Esc退出编辑状态
  - `:wq` 保存退出
  - `:q!` 退出不保存
- `mkdir`：创建文件夹
- `ps aux|grep tomcat(其他)`： 查看当前软件进程
- `kill -9 1111(进程数)`：杀死进程
- `vim /etc/sysconfig/iptables`：编辑防火墙端口
- `service iptables restart` ：重启iptables服务
- `chmod a+x startup.sh`：给startup.sh赋予全部权限



## JDK配置

- 在/usr/local下创建tmp文件夹

- 使用FileZilla上传压缩包到Linux中/usr/local/tmp下

- `tar zxvf jdk1.8` --解压jdk

- `cp -r jdk1.8 /usr/local/jdk` --复制jdk1.8到/usr/local/jdk下

- `vim /etc/profile` --修改此文件配置环境变量

  ```shell
  export JAVA_HOME=/usr/local/jdk
  export PATH=$JAVA_HOME/bin:$PATH
  ```

- `source /etc/profile` 让环境变量配置立即生效

- `java -version` 查看jdk是否配置生效



## Tomcat配置

- 环境配置

  `vim /etc/profile`添加

  ```shell
  export TOMCAT_HOME=/usr/local/solr/tomcat
  export CATALINA_HOME=/usr/local/solr/tomcat
  ```

  tomcat集群时需要把配置写在startup.sh和shutdown.sh文件里

- 防火墙放行8080端口

- `./startup.sh & tailf ../logs/catalina.out` 带日志启动tomcat



## MySQL配置

- 解压mysql压缩包并复制到 /usr/local/mysql

- **创建用户组和用户**

  添加用户组，命名为mysql ： `groupadd mysql`

  创建用户mysql，并指定所属群组为mysql ：`useradd -r -g mysql mysql`

- **赋权**，让用户组和用户具有操作权限

  . 表示本级目录；一定要在/usr/local/mysql中操作

  给mysql用户组赋权 ：`chgrp -R mysql .`

  给mysql用户赋权 ：`chown -R mysql .`

- **初始化**

  保证在/usr/local/mysql下操作

  判断/etc/my.cnf是否存在，如果存在删除 ，不存在跳过

  - `ls /etc/my.cnf`

  - `rm /etc/my.cnf`

  初始化数据库

  `./scripts/mysql_install_db --user=mysql`

- **修改配置文件**

  复制my.cnf文件

  `cp support-files/my-default.cnf /etc/my.cnf`

  复制启动文件

  `cp support-files/mysql.server  /etc/rc.d/init.d/mysql`

- **mysql服务**

  启动 `service mysql start`

  关闭 `service mysql stop`

  重启 `service mysql restart`

- **操作数据库**

  添加软连接 `ln -s /usr/local/mysql/bin/mysql /usr/bin/mysql`

  mysql编辑模式 `mysql –u root –p`