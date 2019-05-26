- [单机版安装步骤](#单机版安装步骤)
- [集群安装步骤](#集群安装步骤)

# Redis安装

## 单机版安装步骤

**Redis 支持单机版和集群,下面的步骤是单机版安装步骤**

1. `yum install -y gcc-c++`

  由于是 c 语言编写,所以需要安装支持组件

2. 把压缩包上传到 linux 服务器上

  示例位置: /usr/local/tmp/ 下

3. `cd /usr/local/tmp`

   `tar zxvf redis-3.0.6.tar.gz`

   进入到/usr/local/tmp 下运行解压命令

4. `make`

  进入到解压后的目录编译

5. `make install PREFIX=/usr/local/redis`

  安装，设置安装路径为/usr/local/redis 下

  进入到 src 下安装

6. `./redis-server`

  前端启动，安装后不能进行其他操作

  Ctrl+c 退出

  命令要在 bin 目录下执行

7. `cp /usr/local/tmp/redis-3.0.0/redis.conf /usr/local/redis/bin`

  把解压目录下配置文件拷贝到安装目录的 bin 下

8. `vi redis.conf`

   修改 bin 下 redis.conf

   把 daemonize 由 no 修改成 yes,守护进程启动

9. `ps aux|grep redis`

   查看 redis 启动情况

10. `./redis-server redis.conf`

    启动 redis 服务

11. `./redis-cli shutdown`

    如果希望关闭,运行上面命令,不关闭不运行即可

12. `./redis-cli`

    进入到自带客户端工具,测试 redis 是否可用

13. `set name 'smallming' ` 

    添加一个 string ，key 为 name，value 为 smallming

14. `get name`

    取出 name 中内容



## 集群安装步骤

1. `yum install ruby -y`

   后面需要用到 ruby 脚本

2. `yum install rubygems -y`

   安装 ruby 包管理器

3. `gem install redis-3.0.0.gem`
   脚本需要 ruby 其他包,所以安装这个 redis.gem

4. `mkdir reids-cluster`

   在/usr/local 中新建 redis-cluster 文件夹

5. `cp -r bin ../redis-cluster/redis01`

   把之前安装好的 redis/bin 复制到 redis-cluster 中并起名为 redis01

6. `rm -rf dump.rdb`

   删除掉 redis01 中 dump.rdb 数据库文件

7. `vi redis.conf`

   修改redis01中端口号为 7001, 找到 port 后面修改为 7001

   去掉 `cluster-enabled yes` 前面的注释

   如果之前设置过密码,注释掉密码。如果没有设置过略过这步骤

8. `cp -r redis01 redis02`

   `cp -r redis01 redis03`

   `cp -r redis01 redis04`

   `cp -r redis01 redis05`

   `cp -r redis01 redis06`

   把 redis01 文件夹在复制 5 份,分别起名为 redis02,redis03,redis04,redis05,redis06

9. `vi redis02/redis.conf`

   此命令需要在 redis-cluster 下执行

   把其他5个文件夹中redis.conf中port修改成不同的值 , 分别为7002,7003,7004,7005,7006

10. `cp *.rb /usr/local/redis-cluster/`

    去 redis 解压目录中 src 下执行此命令

    把 redis-trib.rb 复制到 reids-cluster 中。

11. `vi startall.sh`

    创建一个批量启动文件

    把下面内容粘贴到文件中

    ```sh
    cd redis01
    ./redis-server redis.conf
    cd .. 
    cd redis02
    ./redis-server redis.conf
    cd .. 
    cd redis03
    ./redis-server redis.conf
    cd .. 
    cd redis04
    ./redis-server redis.conf
    cd ..
    cd redis05
    ./redis-server redis.conf
    cd .. 
    cd redis06
    ./redis-server redis.conf
    cd ..
    ```

12. `chmod +x startall.sh`

    给脚本设置一个可启动权限

13. `./startall.sh`

    执行脚本,启动所有 redis 服务

14. `ps aux|grep redis`

    查看所有服务是否启动成功

15. `./redis-trib.rb create --replicas 1 192.168.192.130:7001 192.168.192.130:7002 192.168.192.130:7003 192.168.192.130:7004 192.168.192.130:7005 192.168.192.130:7006`

    创建集群

    在执行时按照提示输入’yes’ 

16. `./redis01/redis-cli -h 192.168.10.128 -p 7001 -c`

    进入任意节点测试

17. `redis01/redis-cli -p 7001 shutdown`

    关闭其中一个 redis

18. `vi shutdown.sh`

    在 redis-cluster 中创建文件,并添加下面内容

    ```shell
    ./redis01/redis-cli -p 7001 shutdown
    ./redis02/redis-cli -p 7002 shutdown
    ./redis03/redis-cli -p 7003 shutdown
    ./redis04/redis-cli -p 7004 shutdown
    ./redis05/redis-cli -p 7005 shutdown
    ./redis06/redis-cli -p 7006 shutdown
    ```

    