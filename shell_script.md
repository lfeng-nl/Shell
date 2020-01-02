# 本文介绍shell脚本相关问题

## 1.基础

- `exit`：确保脚本程序能够返回一个有意义的退出码。 

## 2.变量

- shell 中的变量无须事先声明，默认情况下，所有变量都被认为是字符串类型；注意变量的赋值，`=`号前后无空格：

  ```sh
  test="Hi lfeng"
  ```

- 在变量名前加`$`可以获取变量内容；

- 单引号和双引号：单引号的`$`变量名不会读取变量内容，双引号会读取`$`后的变量内容；

  ```sh
  #!/bin/bash
  test="Hi lfeng"
  echo "$test"			
  #输出：Hi lfeng
  echo '$test'
  #输出：$test
  echo "\$test"
  #输出：$test
  ```

- 环境变量：系统已经初始化好的具有特殊意义的变量；
  - `$HOME`：当前用户的家目录；
  - `$PATH`：用冒号`:`分隔的用来搜索命令的目录列表；
  - `$PS1`：命令提示符号，通常是`$`
  - `$PS2`：二级提示符号，通常是`>`
  - `$IFS`：输入分隔符号；
  - `$0`：shell脚本的名字
  - `$1`：第一个参数；
  - `$#`：shell脚本参数的个数；
  - `$$`：shell脚本的进程号，（可以用它来生成唯一的临时文件）；

- 参数变量

  - `$1、$2`：脚本的参数；
  - `$*`：所有参数，以`$IFS`分隔；
  - `$@`：所有参数，以空格分隔，不依靠`$IFS`，比较可靠，推荐使用；

## 3.条件

shell中使用`test`或`[ ]`命令来进行判断，可以对==文件类型==和==值==（字符串和算数比较）进行比较；

- 字符串比较

  ```sh
  string1 = string2
  #相等为真
  string1 != string2
  #不等为真
  -n string
  #不为空为真
  -z string
  #空串为真
  ```

- 算数比较

  ```sh
  expression1 -eq expression2
  # 两个表达式相等为真
  expression1 -ne expression2
  # 两个表达式不相等为真
  expression1 -gt expression2
  # expression1 大于 expression2为真
  expression1 -ge expression2
  # expression1 大于等于 expression2为真
  expression1 -lt expression2
  # expression1 小于 expression2为真
  expression1 -le expression2
  # expression1 小于等于 expression2为真
  ！expression1
  ```

- 文件测试

  ```sh
  -f file
  # 文件是否存在
  ```

## 4.控制语句

  - if语句

    ```sh
    if [...]
    then
        ...
    elif [...]
    then
        ...
    else
        ...
    if
    ```

- for语句

  ```sh
  for variable in values
  do
  	...
  done
  ```

### 1.特殊控制语句

```sh
-e filename  如果filename存在，则为真
-d filename  如果filename为目录，则为真
-f filename  如果filename为常规文件，则为真
-r filename  如果filename可读，则为真
-w filename  如果filename可写，则为真
-x filename  如果filename可执行，则为真
```

## 5.命令串联

- `;`:  顺序执行, 即使其中某一条命令执行失败, 后面也会继续执行;
- `|`: 管道连接, 前面命令的输出, 作为后面命令的输入;
- `&&`: 前面命令执行成功后才继续执行后面的命令;
- `||`:  前面命令执行失败后才继续执行后面的命令;

## 6.函数

- 使用`function`定义函数

  ```sh
  function name{
    commands
  }
  ```

- 直接定义

  ```sh
  name(){
    commands
  }
  ```

## 7.重定向

- `command > file`: 将输出重定向到file
- `command < file`: 将输入重定向到file
- `command >> file`: 将输出追加到file
- `n > file`: 将文件描述符为n的文件重定向到file

- `n >& m`: 将输出到n的内容重定向到m
- 例: `1 > /dev/null`: 丢弃标准输出, 2 >& 1`: 将标准错误输出合并到标准输出;

## 8.调试

- `sh -n <script>`: 只检查语法, 不执行命令;
- `sh -v <script>`: 执行命令前回显;
- `sh -x <script>`: 在处理完命令之后回显;
- `sh -u <script>`: 如果使用了未定义变量, 给出出错消息;

## 9.特殊用法

### 1.mktemp

> 创建临时文件或者临时目录, 并打印其名称.

```sh
TEMPFILE=$(mktemp) || exit 1
```

- 参数:
  - `-d`: 创建目录;
  - `-p`: 指定临时文件所在的目录;
  - `-t`: 指定文件名称模板`tmpname.XXXXXX`, XXXX最终会被随机字符替代.

### 2.trap

> 响应系统信号
>
> trap [动作] [信息]

```sh
# HUP：编号1，脚本与所在的终端脱离联系。
# INT：编号2，用户按下 Ctrl + C，意图让脚本中止运行。
# QUIT：编号3，用户按下 Ctrl + 斜杠，意图退出脚本。
# KILL：编号9，该信号用于杀死进程。
# TERM：编号15，这是kill命令发出的默认信号。
# EXIT：编号0，这不是系统信号，而是 Bash 脚本特有的信号，不管什么情况，只要退出脚本就会产生。

trap 'rm -rf "$TEMPFILE"' EXIT
```