# 本文介绍shel及shell脚本相关问题

## 1.shell基础

- exit：确保脚本程序能够返回一个有意义的退出码。 

## 2.变量

- shell 中的变量无须事先声明，默认情况下，所有变量都被认为是字符串类型；注意变量的赋值，`=`号前后无空格：

  ```shell
  test="Hi lfeng"
  ```

- 在变量名前加`$`可以获取变量内容；

- 单引号和双引号：单引号的`$`变量名不会读取变量内容，双引号会读取`$`后的变量内容；

  ```shell
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

  ```shell
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

  ```shell
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

  ```shell
  -f file
  # 文件是否存在
  ```

##4.控制语句

  - if语句

    ```shell
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

  ```shell
  for variable in values
  do
  	...
  done
  ```

### 1.特殊控制语句

```shell
-e filename  如果filename存在，则为真
-d filename  如果filename为目录，则为真
-f filename  如果filename为常规文件，则为真
-r filename  如果filename可读，则为真
-w filename  如果filename可写，则为真
-x filename  如果filename可执行，则为真
```

## 5.函数

- 使用`function`定义函数

  ```shell
  function name{
    commands
  }
  ```

- 直接定义

  ```shell
  name(){
    commands
  }
  ```

## 6.重定向

- `2>&1`: 将标准错误输出重定向到标准输出;
- `1>/dev/null`: 丢弃标准输出;
- `... > /dev/null 2>&1`: 丢弃标准输出和标准错误输出, 顺序不能反;



