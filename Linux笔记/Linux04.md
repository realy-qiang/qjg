# Shell脚本编程与运维

通过Shell的各种命令，对服务器进行维护工作，但是这样的效率非常低，工作量非常大，为了能够对服务器批量执行操作，可以将需要执行的命令写到文件中，批量执行，这种文件就是Shell脚本，后缀名为.sh，也可以省略扩展名

## 基本的Shell脚本

每一个Shell脚本第一行都需要通过注释来指明执行脚本的程序，常见的形式有：

> #!/bin/bash
>
> #!/bin/sh
>
> #!/user/bin/env bash

Python脚本的第一句一般是：`#!/user/bin/env python`

Shell脚本的创建过程：

1、创建一个.sh的文件

2、通过vim编辑这个文件

```shell
#!/bin/bash 

echo "hello"                                                              
 echo "I am qjg"
echo "The CPU in my has `cat /proc/cpuinfo | grep -c processor` croes"

 exit 0
```

3、给这个文件修改权限

`chmod a+x 文件名`

4、执行脚本`./文件名`

```bash
qjg@python:~/桌面$ ./cpu-count.sh 
hello
I am qjg
The CPU in my has 4 croes
```

5、文件的最后一行`exit 0`表示文件的状态，0为正常状态，其他为异常状态

可以通过`echo $?`来查看文件的退出状态

## Shell脚本中变量

1、定义

变量名=变量值，注意等号两边不能有空格

```shell
a=123
b=xyz
```

2、使用

调用变量的方式是：`$变量名`

```shell
echo '变量a的值为：$a'
echo '变量a的值为：$b'
```

3、shell脚本中的单引号、双引号和反引号

- 单引号、双引号用于用户把带有空格的字符串赋值给变量的分界符，如果没有单引号或双引号，shell会把空格后的字符串解释为命令
- 单引号和双引号的区别
  - 单引号告诉shell忽略所有特殊字符，而双引号忽略大多数，但不包括(美元符号$、反引号``、反斜杠\\)，这3种特殊字符将不被忽略。不忽略美元符号意味着shell在双引号内部可进行变量名替换。
- 反引号：反引号起着命令替换的作用。命令替换是指shell能够将一个命令的标准输出插在一个命令行中任何位置，将反引号中的字符串做为命令来执行，并且反引号中的命令优先执行

4、定义当前shell的全局变量

- 定义：export 变量名=变量值
- 定义完之后通过source加载` source 文件路径`

5、常见的系统环境变量

- $PATH：可执行文件目录

```bash
qjg@python:~$ echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
```

- $PWD：当前目录

```bash
qjg@python:~$ echo $PWD
/home/qjg
```

- $HOME：家目录

```bash
qjg@python:~$ echo $HOME
/home/qjg
```

- $USER：当前用户

```bash
qjg@python:~$ echo $USER
qjg
```

- $UID：当前用户的UID

```bash
qjg@python:~$ echo $UID
1000
qjg@python:~$
```

## 分支语句

完整格式：

```shell
if 判断条件1
then:
	条件1为真执行的代码块
elif 判断条件2：
	条件2为真执行的代码块
else:
	条件1,2都为假时执行的代码块
fi
	
```

1、`if`语句后的命令的状态码：0位true，其他值为false

```shell
if ls /xxx
then
 echo 'exist xxx'
else
 echo 'not exist xxx'
fi
```

2、条件测试命令[...]

+ shell提供了一种专做条件测试的语句[...]

+ 这一对方括号本质上是一个命令，里面的条件是其参数，所以括号里面的内容和括号个隔一个空格

+ 有三种比较

  + 数值比较

  + 字符串比较

  + 文件比较

  + 语法格式

    ```shell
    if [ 条件 ]
    then
    	代码块
    fi
    ```

3、条件列表

+ 数值比较

| 条件      | 说明                 |
| --------- | -------------------- |
| n1 -eq n2 | 判断n1是否等于n2     |
| n1 -ge n2 | 判断n1是否大于等于n2 |
| n1 -gt n2 | 判断n1是否大于n2     |
| n1 -le n2 | 判断n1是否小于等于n2 |
| n1 -lt n2 | 判断n1是否小于n2     |
| n1 -ne n2 | 判断n1是否不等于n2   |

+ 字符串比较

| 条件         | 说明                   |
| ------------ | ---------------------- |
| str1 = str2  | 判断str1和str2是否相同 |
| str1 != str2 | 判断str1和tr2是否不同  |
| str1 = str2  | 判断str1是否比str2小   |
| str1 = str2  | 判断str1是否比str2大   |
| -n str1      | 判断str1的长度是否非0  |
| -z str1      | 判断str1的长度是否为0  |

+ 文件比较

| 条件            | 说明                                     |
| --------------- | ---------------------------------------- |
| -d file         | 判断file是否存在并是一个目录             |
| -e file         | 判断file是否存在                         |
| -f file         | 判断file是否存在并是一个文件             |
| -r file         | 判断file是否存在并且可读                 |
| -w file         | 判断file是否存在并且可写                 |
| -x file         | 判断file是否存在并且可执行               |
| -s file         | 判断file是否存在并且非空                 |
| -O file         | 判断file是否存在并属于当前用户所有       |
| -G file         | 判断file是否存在并且默认组与当前用户相同 |
| file1 -nt file2 | 判断file是否比file2新                    |
| file -ot file2  | 判断file是否比file2旧                    |

## 循环语句

shell中有三种循环语句

语法格式：

```
for 变量 in 序列 do
	代码块
done
```

练习：打印1到10中的奇偶数



代码解释：

+ `seq 起始数 终止数`生成一个数字序列，注意是闭区间
+ $[ num1 + num2 ]：用来进行基本的数学运算
+ [[ ... ]]：用来更方便的进行比较

C语言风格的for语句

```shell
for ((i=0; i<10; i++))
do 	
	echo "num is $i"
done
```

## 函数

1、函数的定义

关键字`function`，是必须有的，不能省略

```
function fun(){
	echo "hello $1"
	echo "hi"
	echo "告辞"
}
```

2、函数的调用

+ 在终端中直接写上函数的名字，不需要小括号
+ 参数的传递，将参数放到函数的后面，多个参数以空格隔开，向正常命令那样使用

```
fun 参数1 参数2 ...
```

3、参数的接收

+ `$`符号后跟数字表示是第几个参数
+ `$#`：统计参数的数量
+ `$@`：接收全部参数
+ `$*`：接收全部参数

## 用户的输入

语法格式：

`read -p "请输入一个数字 num"` 

+ `read`：读取用户输入的内容
+ -p：后面跟提示信息
+ num：变量，用来接受用户的输入内容