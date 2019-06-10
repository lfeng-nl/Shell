> 记录日常使用到的，但又相对不容易知道的命令

## 1.系统管理相关

>1.[Linux的启动流程](http://www.ruanyifeng.com/blog/2013/08/linux_boot_process.html)；
>
>2.service xxx start，用于控制`/etc/init.d`目录下的脚本文件名；

### 1.`systemd`相关命令

> 关于systemd更多请了解 [阮一峰](http://www.ruanyifeng.com/blog/2016/03/systemd-tutorial-commands.html)

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
    -e	显示所有进程。
    -f	全格式。
    -h	不显示标题。
    -l	长格式。
    -w  宽输出。
    a	显示终端上的所有进程，包括其他用户的进程。
    r	只显示正在运行的进程。
    u	以用户为主的格式来显示程序状况。
    x	显示所有程序，不以终端机来区分。
    ```

  - 常用：`ps aux|less ..., ps aux|grep ...` 

### 3.lsof 一切皆文件

> 查看当前系统文件, linux环境下,任何事物都是以文件的形式存在, 包括网络套接字等;

- 选项:
  - `-a`: 列出打开文件存在的进程;
  - `-u`: 列出某个用户打开的文件信息;
  - `-c`: 列出某个进程名所打开的文件信息;
  - `-p`: 列出某个进程所打开的文件信息, 以进程号输入;
  - `-i`: 列出网络连接;

### 4.用户和用户组

> 用户组：有时需要让多个用户具有相同的权限, 比如查看,修改某一文件或执行某个命令;

- 用户组: 可以通过`/etc/groups`文件查看所有用户组
  - `groupadd test`: 添加用户组;
  - `groupmod -n  test2 test`: 修改用户组信息,`-n`: 修改名称;
  - `groupdel test2`: 删除用户组;
  - `groups`: 查看当前用户所属的用户组有哪些;

- 用户: 可以通过`/etc/passwd`查看所有用户列表;
  - `useradd -g test -m u_test`: 添加用户, `-g`: 添加到用户组, `-m`: 创建用户目录, `-M`: 不创建用户目录;
  - `passwd u_test`: 修改用户密码;
  - `usermod xx`: 修改用户;
  - `gpasswd -a u_test test`: 将用户添加到test用户组, `-a`: 添加. `-d`:移除;`
  - `userdel u_test`: 删除用户, `-r`: 同时删除工作目录;

- 切换用户:
  - `su` : 更改用户ID, 默认更改为root用户;
  - `exit`: 退出当前登录用户到上一个用户;

## 2.磁盘和文件

### 1.文件查看

- tail ：输出文件末尾部分
  - `-f`：当文件增长时，输出后续添加的内容；
  - `-s`：与`-f`配合，表示在每次反复的间隔休眠s秒；
  - `-n`：输出最后N行，（不指定默认输出10行）；
- `less`：查看整个文件，通`more`类似，但也可向前向后查看文件；
  - `-N`：显示行号；
- `grep`：`grep [options] "text" file`
  - `-r`：遍历文件夹；
  - `-n`：显示行号；
  - `-E`：扩展正则，可以用于匹配多个关键字；

- `find`: 查找目录及文件`find ./ -name "xxxx"
- `locate xxx`: 查找文件系统索引, 速度快, 但不是实时;

### 2.文本处理

- `xargs`: 将输入数据转化为特定命令行参数;
  - 将多行输出转化为单行输出, `-d` 定义定界符;

### 2.du和df

- du：查看文件所占空间；`du -h file`
  - `-d`: 深度;
- df：显示磁盘空间，`df -h`
  - `-h`: 以方便人类阅读的方式呈现;

### 3.fdisk :查看，创建，维护分区表

- 选项：
  - `-l`： 列出所有分区；

### 4.通配符

> 通配符早于正则, 可以看做原始正则表达式,功能相对没有那么灵活强大, 但是胜在简单方便; 

- `?`: 单个字符,
- `*`: 任意数量的字符,
- `[xxx]`: 匹配括号中的任意一个字符,
- `[^xxx], [!xxx]`: 表示匹配不在方括号里面的字符,

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

## 4.脚本相关

### 1.set 修改shell环境的运行参数

- `set -u`：遇到不存在的变量就会报错，并停止执行；
- `set -x`：运行结果之前，先输出执行的那一行命令；
- `set -e`：根据返回值判断命令是否执行失败，失败则脚本停止执行；
  - `set -o pipefail`：针对管道，当管道中只要有一个命令执行失败，脚本就会终止执行；

## 5.网络相关

### 1.netstat  查看网络信息

- 参数
  - -a (all)显示所有选项，默认不显示LISTEN相关
  - -t (tcp)仅显示tcp相关选项
  - -u (udp)仅显示udp相关选项
  - -n 拒绝显示别名，能显示数字的全部转化成数字(如端口号,不显示对应服务)。
  - -l 仅列出有在 Listen (监听) 的服務状态
  - -p 显示建立相关链接的程序名
  - -r 显示路由信息，路由表
  - -c 持续输出
- 如查询端口占用`netstat -anp|less,  netstat -anp|grep 9090`

### 2.路由相关

> 路由表的左右: 指定下一级网关,匹配规则有: 1.优先级匹配, 2.最长匹配;

- `route`: 查看路由;

- `route [add|del][-net|-host] target [netmask Nm][gw Gw] [[dev] If]`: 添加或删除路由;
  - 例如`route add -net 10.20.30.48 netmask 255.255.255.248 gw 10.20.30.41 #添加10.20.30.48的网络`;

### 3.traceroute

### 4.curl 网络文件传输

- `-i`: 显示头部和网页信息;
- `-X`: 设置请求方式, 默认为GET;   
- `--referer`: 设置referer信息;
- `--user-agent`: 设置User Agent信息;
- `--cookie`: 设置cookie信息, `--cookie "name=xxx" [url]`;
- `-c cookies_file [url]`: 保存信息到cookie_file;
- `-H, --header`:  设置头部信息, `-H "Content-Type:application/json" [url]`;
- `-d --data`: 向服务器传输数据; `content type`为 `application/x-www-form-urlencoded`;

### 5.ssh

- 使用`pam_abl`模块防止暴力破解, [参考](<http://www.hexten.net/assets/pam_abl_doc/index.html>)；
  - `pam_abl --okhost=HOST`: 不阻止指定地址;

## 6.文本处理

### 1.awk

- `awk '{pattern + action}' {filenames}`：pattern 表示在数据中查找的内容，action是在匹配到查找内容时所执行的命令；
  - 如：`awk '{print $0}' /etc/passwd`，将`/etc/passwd`作为输入文件，每一行作为参数，指向指令；
  - `-F":"` ：指定分隔符；

## 7.性能

### 1.free 查看内存

- free -h

## 8.其他

### 1.OpenSSL

> 安全套接字层密码库,  囊括主要的密码算法, 常用的密钥和证书封装管理功能及SSL协议;
>
> 包括: `openssl`多用途命令行工具,  `libcrypto`加密算法库, `libssl`: 加密模块应用库, 实现了ssl及tls;
>
> 这里主要介绍 `opensll`命令行工具;

- `openssl genrsa`: 主要用来生成私钥, 选择使用的算法, 对称加密密码和私钥长度来生成私钥;
  - `openssl genrsa -out foobar.key 2048`
- `openssl req`: 生成证书请求和生成自签名证书
  - `openssl req -new -key foobar.key -out foobar.csr`
- `openssl x509`: 证书处理工具, 可用来显示证书内容, 转换格式,等:
- `openssl ca`: 生成RSA参数: