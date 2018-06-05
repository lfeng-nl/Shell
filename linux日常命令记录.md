> 记录日常使用到的，但又相对不容易知道的命令

## 1.系统管理相关

>1.[Linux的启动流程](http://www.ruanyifeng.com/blog/2013/08/linux_boot_process.html)；
>
>2.service xxx start，用于控制`/etc/init.d`目录下的脚本文件名；

- `systemd`相关命令，关于systemd更多请了解 [阮一峰](http://www.ruanyifeng.com/blog/2016/03/systemd-tutorial-commands.html)

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

## 2.文件处理相关



- tail ：输出文件末尾部分
  - `-f`：当文件增长时，输出后续添加的内容；
  - `-s`：与`-f`配合，表示在每次反复的间隔休眠s秒；
  - `-n`：输出最后N行，（不指定默认输出10行）；