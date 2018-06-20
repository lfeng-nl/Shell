> 记录日常使用到的，但又相对不容易知道的命令

## 1.系统管理相关

>1.[Linux的启动流程](http://www.ruanyifeng.com/blog/2013/08/linux_boot_process.html)；
>
>2.service xxx start，用于控制`/etc/init.d`目录下的脚本文件名；

###1.`systemd`相关命令，关于systemd更多请了解 [阮一峰](http://www.ruanyifeng.com/blog/2016/03/systemd-tutorial-commands.html)

- Systemd 可以管理所有的系统资源，不同的资源统称为Unit；

- Unit一共有12种：

  - Service unit，系统服务
  - Device unit，硬件设备
  - ...

- `systemctl status`命令用于查看系统状态和单个 Unit 的状态。 

- `systemctl status xxx`查看单个unit的状态；

  - ```bash
    # 可用于脚本判断
    #显示某个 Unit 是否正在运行
    $ systemctl is-active application.service
    
    # 显示某个 Unit 是否处于启动失败状态
    $ systemctl is-failed application.service
    ```

- `systemctl start/stop/restart xxx`：启动，停止，重启某个 Unit ；

- `systemctl list-dependencies  xxx`：列出一个 Unit 的依赖；

- Unit配置文件：Unit 都有一个配置文件，告诉 Systemd 怎么启动这个 Unit ；默认从`/etc/systemd/system/`读取（都是符号链接，指向`/usr/lib/systemd/system/`(真正配置文件)）；[配置文件解析](http://www.siguoya.name/pc/home/article/46)

  - `systemctl enable xxx`用于建立上面所述的文件的链接；等同于：`ln -s /usr/.. /etc/systemd/system/multi-user.target.wants/..`
  - 如果文件里设置的开机启动，`systemctl enable xxx`命令相当于激活了开机启动；
  - `WantedBy `指定Unit所在的 Target 

- `systemctl cat`命令可以查看配置文件的内容。 

- `Target`：就是一个 Unit 组，启动Target时，就会启动里面所有的 Unit ；

  - `systemctl get-default`：查看启动时的默认Target；一般指向 `system/multi-user`
  - `systemctl list-dependencies multi-user.target`：查看 target 包含的 Unit

### 2.进程相关

- ps [选项]

  - 选项：

    ```shell
    -e   显示所有进程。
    -f    全格式。
    -h   不显示标题。
    -l    长格式。
    -w  宽输出。
    a    显示终端上的所有进程，包括其他用户的进程。
    r    只显示正在运行的进程。
    u 　以用户为主的格式来显示程序状况。
    x     显示所有程序，不以终端机来区分。
    ```

  - 常用：`ps aux|less ..., ps aux|grep ...` 

## 2.磁盘和文件处理

### 1.文件查看

- tail ：输出文件末尾部分
  - `-f`：当文件增长时，输出后续添加的内容；
  - `-s`：与`-f`配合，表示在每次反复的间隔休眠s秒；
  - `-n`：输出最后N行，（不指定默认输出10行）；
- `less`：查看整个文件，通`more`类似，但也可向前向后查看文件；
  - `-N`：显示行号；

### 2.du和df

- du：查看文件所占空间；`du -h file`
- df：显示磁盘空间，`df -h`

### 3.fdisk :查看，创建，维护分区表

- 选项：
  - `-l`： 列出所有分区；

## 3.文件传输

### 1.sz，rz:

>服务器一般都是通过ssh客户端进行远程登录和管理的，可以使用sz，和rz，在本地和服务器之间进行文件交换；
>
>sz：将选定的文件发送（send）到本地机器    rz：运行该命令会弹出一个文件选择窗口，从本地选择文件上传到服务器(receive)  ；

- 安装：`yum install lrzsz -y `；
- 上传：`rz`，上传也也已直接拖动文件到ssh客户端窗口，选择zmodem；
- 下载：`sz`
- 缺点：传输速度低

### 2.scp ：secure copy

> 基于ssh登录

- 从本地复制到远端：`scp local_file remote_username@remote_ip:remote_folder `
- 从远端复制到本地：`scp remote_username@remote_ip:remote_folder local_file `
- 可选参数：
  - `-v`：显示进度；
  - `-P`：选择端口；

### 3.sftp：secure file transfer program

> 交互式文件传输，

- 登录：`sftp -P port user@host`
- 登录后的常用操作：
  - `help`：查看可用命令；
  - `pwd` ： 查看远程服务器当前目录；
  - `lpwd`：  查看本地系统的当前目录。
  - `cd <dir>` ：  将远程服务器的当前目录更改为<dir>；
  - `lcd <dir> `： 将本地系统的当前目录更改为<dir>。
  - `ls` ：显示远程服务器上当前目录的文件名；
  - `lls`： 显示本地系统上当前目录的文件名；
  - `get <file>` ：下载指定文件<file>；
  - `put <file>`： 上传指定文件<file>；

4.

