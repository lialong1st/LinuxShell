# 第一个shell脚本

新建一个文本文件，扩展名为sh（sh表示shell），扩展名不会影响脚本执行，见名知意即可。

第一个简单的脚本如下，保存为hello.sh：

```bash
#!/bin/bash
echo "Hello World!"
```

“#!”是一个约定的标记，它告诉系统这个脚本需要什么解释器来执行，即使用哪一种Shell。

echo表示向窗口输出文本。

## 变为可执行程序

```bash
# 给脚本添加可执行权限
chmod +x hello.sh
# 执行脚本
./hello.sh
```

"#"表示此行为注释，与“#!”不同。

执行脚本必须写成./hello.sh，如果直接写成hello.sh（类似于ls、cd等），Linux系统回去PATH路径中寻找有没有hello.sh，一般PATH路径中只有/bin、/sbin、/usr/bin、/usr/sbin等，此脚本的目录不在PATH路径中，系统会提示找不到此命令。用./hello.sh告诉系统在当前目录寻找。

通过“./”这种方式运行bash脚本，第一行要写对。

------

# Shell变量

在Bash Shell中，每一个变量的值都是字符串，无论给变量赋值时有没有使用引号，值都会以字符串的形式存储。

使用read命令从stdin获取输入并复制给变量，然后通过echo在stdout上输出。

```bash
#!/bin/bash

echo "How old are you?"
read age
echo "I'm ${age}."
```

运行此脚本：

```linux
$ ./read.sh 
How old are you?
45
I'm 45.
```

## 变量定义

1. 变量名由数字、字母和下划线组成；
2. 必须以字母或者下划线开头；
3. 不能使用shell里的关键字；

## 变量赋值

等号的前后不能有空格，如果value中不包括任何空白符（如空格、Tab等），那么可以不使用引号；如果value中包含了空白符，那么就必须使用引号包围起来。

Shell支持一下三种定义变量的方式：

```bash
#!/bin/bash

url=www.google.com
echo ${url}

name='AaronLee'
echo ${name}

country="CHINA"
echo ${country}
```

单双引号的区别后面会讲解。

## 变量使用

使用定义过的变量，只需在变量名前面加”$“即可。

变量名外面的大括号{}是可选的，加不加都行，加大括号是为了区分变量的边界，推荐给所有变量加上大括号{}。

```bash
#!/bin/bash

url=www.google.com
echo ${url}

name='AaronLee'
echo ${name}

country="CHINA"
echo ${country}

skill="Java"
# 不加大括号，系统就会把skillScript当成一个变量，其值为空
echo "I am good at $skillScript"
echo "I am good at ${skill}Script"
```

运行结果如下：

```Linu
$ ./test.sh 
www.google.com
AaronLee
CHINA
I am good at 
I am good at JavaScript
```

## 单、双引号的区别

以单引号包围起来的值，单引号里面是什么就输出什么，即使内容中有变量和命令（命令需要反引起来）。这种方式比较适合定义显示纯字符串的情况，即不希望解析变量和命令的场景。

以双引号包围起来的值，输出是会先解析里面的变量和命令，而不是把变量和命令原样输出。这种方式适合字符串中附带有变量和命令，并且想解析后再输出的。

建议：如果变量内容是数字，可以不加引号；如果想原样输出，就加单引号；没有特殊要求，就加双引号。

```bash
#!/bin/bash

url="www.google.com"

web='谷歌:${url}'
echo ${web}

web="谷歌:${url}"
echo ${web}
```

运行结果如下：

```Linux
$ ./test.sh   
谷歌:${url}
谷歌:www.google.com
```

## 将命令的结果赋值给变量

Shell中将命令的执行结果赋值为变量，有以下两种方式：

1. 把命令用反引号包围起来，反引号与单引号非常相似，不建议使用；
2. 把命令用$()包围起来，推荐此种方法。

```bash
#!/bin/bash

# 以下两种方法输出是相同的

var1=`pwd`
echo ${var1}

var2=$(pwd)
echo ${var2}
```

## 只读变量

使用readonly命令将变量定义为只读变量，只读变量的值不能改变。

修改只读变量，运行会报错，如下：

```bash
#!/bin/bash

url="www.google.com"
readonly url
url="www.baidu.com"
```

运行结果：

```Linux
$ ./test.sh 
./test.sh: 行 5: url: 只读变量
```

## 删除变量

使用unset命令可以删除变量，变量删除后不能再次使用，unset不能删除只读变量。

```bash
#!/bin/bash

url="www.google.com"
echo ${url}

# 无任何输出
unset url
echo ${url}

readonly web="www.cnblogs.com"
unset web
```

运行结果如下：

```Linux
$ ./test.sh 
www.google.com

./test.sh: 第 10 行: unset: web: 无法取消设定: 只读 variable
```

## 变量类型

**局部变量**

局部变量在脚本或命令中定义，仅在当前文件中有效，其他启动的程序不能访问。

**环境变量**

所有的程序，包括shell启动的程序，都能访问环境变量，有些程序需要环境变量来保证其正常运行。必要时shell脚本中可以定义环境变量。

**shell变量**

shell变量是由shell程序设置的特殊变量。shell变量中有一部分是环境变量，有一部分是局部变量，这些变量保证了shell的正常运行。

------

# shell特殊变量

变量名只能包含数字、字母和下划线，因为某些包含其他字符的变量有特殊意义，这样的变量被称为特殊变量。

<center>特殊变量列表</center>

| 变量 | 含义                                                         |
| ---- | ------------------------------------------------------------ |
| $0   | 当前脚本的文件名                                             |
| $n   | 传递给脚本的参数。n是一个数字，表示第几个参数。例如：第2个参数是$2 |
| $#   | 传递给脚本的参数个数                                         |
| $*   | 传递给脚本的所有参数                                         |
| $@   | 传递给脚本的所有参数，被双引号包含时，与$*稍有不同，下面讲解 |
| $?   | 上个命令的退出状态，或函数的返回值                           |
| $$   | 当前shell的进程ID。对于shell脚本，就是当前脚本所在的进程ID   |

## 命令行参数

运行脚本时传递给脚本的参数称为命令行参数。命令行参数用$n表示，第一个就是$1，第二个就是$2，以此类推。

```bash
#!/bin/bash

echo "File Name: $0"
echo "First Parameter: $1"
echo "Second Parameter: $2"
echo "All Paremeter: $@"
echo "All Paremeter: $*"
echo "The Mumber of Parameter: $#"
```

运行结果如下：

```Linux
$ ./test.sh ab cd
File Name: ./test.sh
First Parameter: ab
Second Parameter: cd
All Paremeter: ab cd
All Paremeter: ab cd
The Mumber of Parameter: 2
```

## $*和$@的区别

不加引号时，二者都是返回传入的参数，但加了引号后,，$*把参数作为一个字符串整体(单字符串)返回，$@把每个参数作为一个字符串返回。

```bash
#!/bin/bash

echo "------------"
echo '$* =' $*
echo '"$*" =' "$*"

echo "------------"
echo '$@ =' $@
echo '"$@" =' "$@"

echo "------------"
for num in $*
do
	echo ${num}
done

echo "------------"
for num in $@
do
	echo ${num}
done

echo "------------"
for num in "$*"
do
	echo ${num}
done

echo "------------"
for num in "$@"
do
	echo ${num}
done
echo "------------"
```

运行结果如下：

```Linux
$ ./test.sh 12 34 56
------------
$* = 12 34 56
"$*" = 12 34 56
------------
$@ = 12 34 56
"$@" = 12 34 56
------------
12
34
56
------------
12
34
56
------------
12 34 56
------------
12
34
56
------------
```

## 退出状态

$?可以获取上一个命令的退出状态。所谓退出状态，就是上一个命令执行后的返回结果。

退出状态是一个数字，一般情况下，大部分命令执行成功后会返回0，失败返回1。

也有一些命令返回其他值，表示不同类型的错误。

$?也可以表示函数的返回值，后续讲解。

# shell替换

如果表达式中包含特殊字符，shell将会进行替换。

在双引号中使用变量就是一种替换，转义字符也是一种替换。

```bash
#!/bin/bash

a=10
# -e表示对转义字符进行替换，不用-e，将会原样输出
echo "Value of a is ${a}.\n"
echo "-----------------"
echo -e "Value of a is ${a}.\n"
echo "-----------------"
```

运行结果如下：

```Linux
$ ./test.sh   
Value of a is 10.\n
-----------------
Value of a is 10.

-----------------
```

下面的转义字符都可以用在echo中：

| 转义字符 | 含义                           |
| -------- | ------------------------------ |
| \\\      | 反斜杠                         |
| \a       | 警报，响铃                     |
| \b       | 退格（删除键）                 |
| \f       | 换页，将当前位置移动到下页开头 |
| \n       | 换行                           |
| \r       | 回车                           |
| \t       | 水平制表符（tab键）            |
| \v       | 垂直制表符                     |

可以使用echo命令的-E选项禁止转义，默认也是不转义的；使用-n选项可以禁止插入换行符。

## 命令替换

命令替换是指shell先执行命令，将输出结果暂时保存，在适当的地方输出。

命令替换可以用反引号或者$()。

```bash
#!/bin/bash

echo `date`

time=`date`
echo ${time}

echo $(pwd)

path=`pwd`
echo ${path}
```

执行结果如下：

```Linux
$ ./test.sh   
2018年 10月 11日 星期四 14:06:12 CST
2018年 10月 11日 星期四 14:06:12 CST
/home/aaron/bash
/home/aaron/bash
```

## 变量替换

变量替换可以根据变量的状态（是否为空、是否定义等）来改变它的值

<center>可以替换的形式</center>

| 形式            | 说明                                                         |
| --------------- | ------------------------------------------------------------ |
| ${var}          | 变量本来的值                                                 |
| ${var:-word}    | 如果变量var为空或者已被删除(unset)，那么返回word，但不改变var的值 |
| ${var:=word}    | 如果变量var为空或者已被删除(unset)，那么返回word，并将var的值设置为word |
| ${var:?message} | 如果变量var为空或者已被删除(unset)，那么将消息message送到标准错误输出，可以用来检测变量var是否可以正常被赋值。<br>若此替换出现在shell脚本中，那么脚本将停止运行。 |
| ${var:+word}    | 如果变量var被定义，那么返回word，但不改变var的值。           |

举例

```bash
#!/bin/bash

echo ${var:-"ab"}
echo "1:var=${var}"

echo ${var:="cd"}
echo "2:var=${var}"

unset var

echo ${var:+"12"}
echo "3:var=${var}"

var="34"

echo ${var:+"56"}
echo "4:var=${var}"

echo ${var:?"78"}
echo "5:var=${var}"
```

运行结果如下：

```Linu
$ ./test.sh 
ab
1:var=
cd
2:var=cd

3:var=
56
4:var=34
34
5:var=34
```

------

# shell运算符

bash支持很多运算符，包括算数运算符、关系运算符、布尔运算符、字符串运算符和文件测试运算符。

原生bash不支持简单的数学运算，但可以通过其他命令来实现，例如：awk、expr等。

expr是一款表达式计算工具，使用它能完成表达式的求值计算。

```bash
echo "5:var=${var}"
#!/bin/bash

val=`expr 3 + 5`
echo "3 + 5 = ${val}"
```

运行结果如下：

```Linux
$ ./test.sh 
3 + 5 = 8
```

- 表达式和运算符之间要有空格；
- 完整的表达式要被反引号包含，在Esc下面。

## 算数运算符

```bash
#!/bin/bash

a=10
b=20

val=`expr ${a} + ${b}`
echo "a + b = ${val}"

val=`expr ${a} - ${b}`
echo "a - b = ${val}"

val=`expr ${a} \* ${b}`
echo "a * b = ${val}"

val=`expr ${b} / ${a}`
echo "b / a = ${val}"

val=`expr ${b} % ${a}`
echo "b % a = ${val}"

if [ ${a} == ${b} ]
then
	echo "a = b"
fi

if [ ${a} != ${b} ]
then
	echo "a != b"
fi
```

运行结果如下：

```Linux
a + b = 30
a - b = -10
a * b = 200
b / a = 2
b % a = 0
a != b
```

**注意：乘号（*）前边必须加反斜杠（\）才能实现乘法运算**

<center>算数运算符列表</center>

| 运算符 | 说明               |
| ------ | ------------------ |
| +      | 加法               |
| -      | 减法               |
| *      | 乘法               |
| /      | 除法               |
| %      | 取余               |
| =      | 赋值               |
| ==     | 相等，则返回true   |
| !=     | 不相等，则返回true |

**注意：条件表达式要放在中括号之间，并且要有空格，必须写成[ $a == $b ]。**

## 关系运算符

关系运算符只支持数字，不支持字符串，除非字符串的值是数字。

<center>关系运算符列表</center>

| 运算符 | 说明                       |
| ------ | -------------------------- |
| -eq    | 左右相等，返回true         |
| -ne    | 左右不相等，返回true       |
| -gt    | 左边大于右边，返回true     |
| -lt    | 左边小于右边，返回true     |
| -ge    | 左边大于等于右边，返回true |
| -le    | 左边小于等于右边，返回true |

关系运算符举例：

```bash
#!/bin/bash

a=10
b=20

if [ ${a} -eq ${b} ]
then
	echo "${a} -eq ${b} : a = b"
else
	echo "${a} -eq ${b} : a != b"
fi

if [ ${a} -ne ${b} ]
then
	echo "${a} -ne ${b} : a != b"
else
	echo "${a} -ne ${b} : a = b"
fi

if [ ${a} -gt ${b} ]
then
	echo "${a} -gt ${b} : a > b"
else
	echo "${a} -gt ${b} : a <= b"
fi

if [ ${a} -lt ${b} ]
then
	echo "${a} -lt ${b} : a < b"
else
	echo "${a} -lt ${b} : a <= b"
fi

if [ ${a} -ge ${b} ]
then
	echo "${a} -ge ${b} : a >= b"
else
	echo "${a} -ge ${b} : a < b"
fi

if [ ${a} -le ${b} ]
then
	echo "${a} -le ${b} : a <= b"
else
	echo "${a} -le ${b} : a > b"
fi
```

运行结果如下：

```Linu
10 -eq 20 : a != b
10 -ne 20 : a != b
10 -gt 20 : a <= b
10 -lt 20 : a < b
10 -ge 20 : a < b
10 -le 20 : a <= b
```

## 布尔运算符

<center>布尔运算符列表</center>

| 运算符 | 说明                                 |
| ------ | ------------------------------------ |
| !      | 非运算，表达式为false，返回true      |
| -o     | 或运算，有一个表达式为true，返回true |
| -a     | 与运算，两个表达式都为true，返回true |

布尔运算符举例：

```bash
#!/bin/bash

a=10
b=20

if [ ${a} != ${b} ]
then
	echo "${a} != ${b} : true"
else
	echo "${a} != ${b} : false"
fi

if [ ${a} -lt ${b} -a ${b} -gt 30 ]
then
	echo "${a} -lt ${b} -a ${b} -gt 30 : true"
else
	echo "${a} -lt ${b} -a ${b} -gt 30 : false"
fi

if [ ${a} -gt ${b} -o ${b} -lt 30 ]
then
	echo "${a} -gt ${b} -o ${b} -lt 30 : true"
else
	echo "${a} -gt ${b} -o ${b} -lt 30 : false"
fi
```

运行结果如下：

```Linux
10 != 20 : true
10 -lt 20 -a 20 -gt 30 : false
10 -gt 20 -o 20 -lt 30 : true
```

## 字符串运算符

<center>字符串运算符列表</center>

| 运算符 | 说明                         |
| ------ | ---------------------------- |
| =      | 左右字符串是否相等，返回true |
| !=     | 左右字符串不相等，返回true   |
| -z     | 字符串长度为0，返回true      |
| -n     | 字符串长度不为0，返回true    |
| str    | 字符串不为空，返回true       |

字符运算符举例：

```bash
#!/bin/bash

str1="adc"
str2="xyz"

if [ ${str1} = ${str2} ]
then
	echo "${str1} = ${str2} : true"
else
	echo "${str1} = ${str2} : false"
fi

if [ ${str1} != ${str2} ]
then
	echo "${str1} != ${str2} : true"
else
	echo "${str1} != ${str2} : false"
fi

if [ -z ${str1} ]
then
	echo "-z ${str1} : true"
else
	echo "-z ${str1} : false"
fi

if [ -n ${str1} ]
then
	echo "-n ${str1} : true"
else
	echo "-n ${str1} : false"
fi

if [ ${str1} ]
then
	echo "${str1} : true"
else
	echo "${str1} : false"
fi
```

运行结果如下：

```Linux
adc = xyz : false
adc != xyz : true
-z adc : false
-n adc : true
adc : true
```

## 文件测试运算符

文件测试运算符用于检测Unix文件的各种属性。

<center>文件测试运算符列表</center>

| 操作符  | 说明                                                   |
| :------ | :----------------------------------------------------- |
| -b file | 文件是块设备文件，返回true                             |
| -c file | 文件是字符设备文件，返回true                           |
| -d file | 目录，返回true                                         |
| -f file | 文件是普通文件（既不是目录，也不是设备文件），返回true |
| -g file | 文件设置了SGID位，返回true                             |
| -k file | 文件设置了粘着位，返回true                             |
| -p file | 文件是具名管道，返回true                               |
| -u file | 文件设置了SUID位，返回true                             |
| -r file | 文件可读，返回true                                     |
| -w file | 文件可写，返回true                                     |
| -x file | 文件可执行，返回true                                   |
| -s file | 文件不为空，返回true                                   |
| -e file | 文件存在，返回true                                     |

下面代码，用于检测该文件的各种属性：

```Linux
$ pwd
/home/aaron/bash
$ ls -l guide.txt 
-rw-rw-r-- 1 hzs hzs 163 10月  9 17:28 guide.txt
```

```bash
#!/bin/bash

path="/home/aaron/bash"
file="/home/aaron/bash/guide.txt"

if [ -d ${path} ]
then
	echo "${path} 是目录"
else
	echo "${path} 是文件"
fi

if [ -f ${file} ]
then
	echo "${file} 是通文件"
else
	echo "${file} 不是普通文件"
fi

if [ -r ${file} ]
then
	echo "${file} 文件可读"
else
	echo "${file} 文件不可读"
fi

if [ -w ${file} ]
then
	echo "${file} 文件可写"
else
	echo "${file} 文件不可写"
fi

if [ -x ${file} ]
then
	echo "${file} 文件可执行"
else
	echo "${file} 文件不可执行"
fi

if [ -s ${file} ]
then
	echo "${file} 文件不为空"
else
	echo "${file} 是空文件"
fi

if [ -e ${file} ]
then
	echo "${file} 文件存在"
else
	echo "${file} 文件不存在"
```

执行结果如下：

```Linux
/home/aaron/bash 是目录
/home/aaron/bash/guide.txt 是通文件
/home/aaron/bash/guide.txt 文件可读
/home/aaron/bash/guide.txt 文件可写
/home/aaron/bash/guide.txt 文件不可执行
/home/aaron/bash/guide.txt 文件不为空
/home/aaron/bash/guide.txt 文件存在
```

# Shell注释

以“#“开头的行就是注释，会被解释器忽略。

sh里面没有多行注释，只能每行加一个#号。

如果在开发过程中，遇到大段的代码需要临时注释起来，过一会儿又取消注释，怎么办呢？每一行加个#符号太费力了，可以把这一段要注释的代码用一对花括号括起来，定义成一个函数，没有地方调用这个函数，这块代码就不会执行，达到了和注释一样的效果。

# Shell字符串

字符串是shell编程中最常用的数据类型，字符串可以用单引号，也可以用双引号，也可以不用引号。

## 单引号

```bash
str='Hello World!'
```

- 单引号里的任何字符都会原样输出，单引号字符串中的变量是无效的；
- 单引号字符串中不能出现单引号（对单引号使用转移符后也不行）。

## 双引号

```bash
name="AaronLee"
str="Hello, I know your name \"${name}\"!\n"
```

- 双引号里可以有变量；
- 双引号里面可以出现转义字符。

## 拼接字符串

```bash
#!/bin/bash

str="World"
str1="Hello "${str}"!"
echo ${str1}
str2="Hello ${str}!"
echo ${str2}
```

## 获取字符串长度

```bash
#!/bin/bash

str="abc"
# 字符串长度为3，输出3
echo ${#str}
```

## 提取字符串

```bash
#!/bin/bash

string="alibaba is a great company"
# 提取1-4位字符串，输出liba
echo ${string:1:4}
```

## 查找子字符串

```bash
#!/bin/bash

str="abc"
expr index $str "a"  # 1
expr index $str "b"  # 2
expr index $str "x"  # 0
expr index $str ""   # 0
```

运行结果入下：

```Linux
1
2
0
0
```

------

# shell数组

bash支持一维数组，不支持多位数组，没有限定数组的大小。数组元素的下标由0开始编号，获取数组中的元素要利用下标，下标可以是整数或算术表达式，其值应大于或等于0。

## 定义数组

在shell中，用括号来表示数组，数组元素用“空格”分开。

定义数组的一般形式为：

```bash
# 第一种形式
arr=(val0 val1 val2)
# 第二种形式
arr=(
val0
val1
val2
)
# 第三种形式，分别定义
arr[0]=val0
arr[1]=val1
arr[2]=val2
```

可以不使用连续的下标，而且下标的范围没有限制。

## 读取数组

读取数组的一般形式：

```
val=${arr[index]}
```

举例：

```bash
#!/bin/bash

person=("HanMei" "25" "male")
echo "name = ${person[0]}"
echo "age = ${person[1]}"

# 使用@或*，可以输出数组中的所有元素
echo "all para: ${person[@]}"
echo "all para: ${person[*]}"
```

运行结果如下：

```Linux
name = HanMei
age = 25
all para: HanMei 25 male
all para: HanMei 25 male
```

## 获取数组的长度

获取数组长度的方法与获取字符串长度的方法相同。

```bash
# 获取数组元素的个数
len=${#arr[@]
# 或者
len=${#arr[*]}
# 获取数组单个元素的长度
len=${#arr[n]}
```

举例：

```bash
#!/bin/bash

person=("HanMei" "25" "male")

echo "arr length: ${#person[@]}"
echo "arr length: ${#person[*]}"
echo "name length: ${#person[0]}"
```

运行结果如下：

```Linux
arr length: 3
arr length: 3
name length: 6
```

------

# Shell echo命令

echo用于在屏幕上打印出指定的字符串。

## 显示转义字符

```bash
# 输出：It is a test
echo "\"It is a test\""
# 双引号可以省略
echo \"It is a test\"
```

## 显示变量

```bash
name="aaron"
# 变量name在此时输出可以省略大括号，但是建议养成习惯一直使用
echo "My name is ${name}"
year=2018
# 输出变量与其他字符相连，一定要使用大括号
echo "${year}-10-16"
```

## 显示换行

```bash
# \n为换行符，不加也默认换行
echo "Hello\n"
echo "World!"
```

## 显示不换行

```bash
# 输出:HelloWorld!
# -e表示开启转义，-c表示不换行
echo -e "Hello\c"
echo "World!"
```

## 显示结果重定向至文件

```bash
# 如果file文件不存在，会创建名为file的文件，并将Hello World！写入file
# 如果file文件存在，会清除file中的内容，再将Hello World！写入file
echo "Hello World!" > file
```

## 原样输出字符串

原样输出字符串，不进行转义，需要使用单引号。

```bash
echo '$name\"'
```

## 显示命令的执行结果

```bash
# 小括号和反引号都能执行命令
echo $(date)
echo `date
```

# Shell printf 命令

printf命令用于格式化输出，是echo命令过的增强版，它是C语言printf库函数的一个有限的变形，并且语法上有些不同。

printf有POSIX标准所定义，移植性比echo好。

如echo命令一样，printf命令也可以输出简单的字符串。

```bash
# printf换行需要手动添加换行符\n
printf "Hello World!\n"
```

printf命令语法格式：

```bash
# format-string为格式控制字符串
# arguments为参数列表
printf format-string [arguments...]
```

- printf命令不用加小括号
- format-string可以没有引号，但是加上最好，单双引号都可以
- 参数多余格式控制符时，format-string可以重用，将所有参数都转换
- arguments使用空格分隔，不用逗号

举例：

```bash
#!/bin/bash

# 格式添加单、双引号都可以
printf "%d %s\n" "123" "abc"
printf '%d %s\n' "123" "abc"
# 格式可以不加引号
printf %s "abc"
printf "\n"
# 参数个数大于格式个数时，格式会被重用
printf %s "abc" "xyz"
printf "\n"
# 格式重用时，会以格式的个数换行显示
printf "%s %s\n" "abc" "def" "ghi" "jkl" "mn"
# 如果没有参数，那么%s用NULL代替，%d用0代替
printf "%s and %d\n"
# 以%d格式输出字符串，会出现提示无效，此时默认输出0
printf "%d\n" "abc"
```

执行结果如下：

```Linux
123 abc
123 abc
abc
abcxyz
abc def
ghi jkl
mn 
 and 0
./test.sh: line 17: printf: abc: invalid number
0
```

注意：根据POSIX标准，浮点格式%e、%E、%f、%g和%G是“不需要被支持的”。因为awk支持浮点运算，且有他自己的printf语句，这样shell程序中需要将浮点数值进行格式化的打印时，可使用小型的awk程序实现。然而，内建与bash、ksh93和zsh中的printf命令都支持浮点格式。

------

# Shell if ... else 命令

if 语句通过关系运算符判断表达式的真假来决定执行哪个分支。

Shell有三种if...else语句：

- if...fi 语句；
- if...else...fi 语句；
- if...elif...else...fi 语句。

## if...fi 语句

举例：

```bash
#!/bin/bash

a=10
b=20

if [ ${a} == ${b} ]
then
	printf "a等于b\n"
fi

if [ ${a} != ${b} ]
then
	printf "a不等于b\n"
fi
```

运行结果入下：

```Linux
a不等于b
```

## if...else...fi 语句

举例：

```bash
#!/bin/bash

a=10
b=20

if [ ${a} -gt ${b} ]
then
	printf "a大于b\n"
else
	printf "a小于等于b\n"
fi
```

运行结果如下：

```Linux
a小于等于b
```

## if...elif...else...fi 语句

举例：

```bash
#!/bin/bash

a=10
b=20

if [ ${a} == ${b} ]
then
	printf "a等于b\n"
elif [ ${a} -gt ${b} ]
then
	printf "c大于b\n"
else
	printf "a小于b\n"
fi
```

运行结果如下：

```Linux
a小于b
```

# Shell test 命令

test 命令用于检查某个条件是否成立，与中括号[]类似。

```bash
if test ${a} -lt ${b};then printf "a小于b\n";fi;
```

## 数值测试

| 参数 | 说明           |
| ---- | -------------- |
| -eq  | 等于则为真     |
| -ne  | 不等于则为真   |
| -gt  | 大于则为真     |
| -ge  | 大于等于则为真 |
| -lt  | 小于则为真     |
| -le  | 小于等于则为真 |

举例：

```bash
#!/bin/bash

num1=100
num2=100
if test $[num1] -eq $[num2]
then
	echo 'The two numbers are equal!'
else
	echo 'The two numbers are not equal!'
fi
```

## 字符串测试

| 参数      | 说明                  |
| --------- | --------------------- |
| =         | 等于则为真            |
| !=        | 不相等则为真          |
| -z 字符串 | 字符串长度为0则为真   |
| -n 字符串 | 字符串长度不位0则为真 |

举例：

```bash
#!/bin/bash

num1=100
num2=100
if test num1=num2
then
	echo 'The two strings are equal!'
else
	echo 'The two strings are not equal!'
fi
```

## 文件测试

| 参数      | 说明                                 |
| --------- | ------------------------------------ |
| -e 文件名 | 如果文件存在则为真                   |
| -r 文件名 | 如果文件存在且可读则为真             |
| -w 文件名 | 如果文件存在且可写则为真             |
| -x 文件名 | 如果文件存在且可执行则为真           |
| -s 文件名 | 如果文件存在且至少有一个字符则为真   |
| -d 文件名 | 如果文件存在且为目录则为真           |
| -f 文件名 | 如果文件存在且为普通文件则为真       |
| -c 文件名 | 如果文件存在且为字符型特殊文件则为真 |
| -b 文件名 | 如果文件存在且为块特殊文件则为真     |

举例：

```bash
#!/bin/bash

cd /bin
if test -e ./bash
then
	echo 'The file already exists!'
else
	echo 'The file does not exists!'
fi
```

Shell还提供了与(-a)、或(-o)、非(!)三个逻辑操作符用于将测试条件连接起来，优先级：

(!) > (-a) > (-o)

------

# Shell case 命令

case ... esac 与其他语言中 switch ... case 语句类似，是一种多分支选择结构。

case 语句匹配一个值或者一个模式，如果匹配成功，执行相关命令。

case 语句格式如下：

```bash
case value in
mode1)
	command1
	command2
	;;
mode2)
	command1
	command2
	;;
*)
	command1
	command2
	;;
esac
```

case工作方式如上所示。取值后面必须为关键字 in，每一模式必须以右括号结束。取值可以为变量或常数。匹配发现取值符合某一模式后，其间所有命令开始执行直至 ;;。;; 与其他语言中的 break 类似，意思是跳到整个 case 语句的最后。

取值将检测匹配的每一个模式。一旦模式匹配，则执行完匹配模式相应命令后不再继续其他模式。如果无一匹配模式，使用星号 * 捕获该值，再执行后面的命令。  

举例：

```bash
#!/bin/bash

echo "Input a number between 1 to 4"
echo -e "Your number is \c"
read num
case ${num} in
1)
	echo "input 1"
	;;
2)
	echo "input 2"
	;;
3)
	echo "input 3"
	;;
4)
	echo "input 4"
	;;
*)
	echo "input error"
	;;
esac
```

# Shell for 循环

for 循环格式：

```bash
for 变量 in 列表
do
	command1
	command2
	...
done
```

列表是一组值（数组、字符串等）组成的序列，每个值通过空格分隔，每循环一次，就将列表中的下一个值赋给变量。

in 列表是可选的，如果不用它，for 循环使用命令行的位置参数。

例如，顺序输出当前列表的数字：

```bash
#!/bin/bash

for loop in 1 2 3 4 5
do
	echo "The value is ${loop}"
done
```

运行结果如下：

```bash
The value is 1
The value is 2
The value is 3
The value is 4
The value is 5
```

顺序输出字符串中的字符：

```bash
#!/bin/bash

for str in "This is a string"
do
	echo ${str}
done
```

显示当前脚本所在目录下后缀为.sh的文件：

```bash
#!/bin/bash

for FILE in *.sh
do 
	echo ${FILE}
done
```

------

# Shell while 循环

while循环用于不断执行一系列命令，也用于从输入文件中读取数据；命令通常为测试条件。其格式为：

```bash
while condition
do
	command1
	command2
done
```

命令执行完毕，控制返回循环顶部，从头开始直至测试条件为假。

举例：

```bash
#!/bin/bash

num=0
# num小于5则输出，大于等于5结束
while [ ${num} -lt 5 ]
do
	num=`expr ${num} + 1`
	echo "num = ${num}"
done
```

------

# Shell until 循环

until 循环执行一系列命令直至条件为 true 时停止。until 循环与 while 循环在处理方式上刚好相反。一般 while 循环优于 until 循环，但在极少数情况下，until 循环更加有用。

until 循环格式为：

```bash
# 如果condition返回值为false，则继续执行循环体内的语句，否则跳出循环。
until condition
do
	command1
	command2
done
```

举例：

```bash
#！/bin/bash

a=0
until [ ${a} -ge 10 ]
do
	printf "%d " ${a}
	a=`expr ${a} + 1`
done

printf "\n"
```

运行结果：

```Linux
0 1 2 3 4 5 6 7 8 9 
```

------

# Shell break 和 continue  命令

在循环过程中，有时候需要在未达到循环结束条件时强制跳出循环，Shell可以使用break和continue来跳出循环。

## break 命令

break 命令允许跳出所有循环（终止执行后面所有的循环）。

举例：

```bash
#！/bin/bash

a=0
b=0

while [ ${a} -lt 10 ]
do
	echo "a = ${a} b = ${b}"

	if [ ${b} -gt 8 ]
	then
		break;
	fi

	a=`expr ${a} + 1`
	b=`expr ${b} + 2`
done
```

运行结果如下：

```Linux
a = 0 b = 0
a = 1 b = 2
a = 2 b = 4
a = 3 b = 6
a = 4 b = 8
a = 5 b = 10
```

在嵌套循环中，break命令后面还可以跟一个整数，表示跳出第几层循环。

举例：var1=2，var2=0，就跳出所有循环。

```bash
#!/bin/bash

for var1 in 1 2 3
do
	for var2 in 0 5
	do
		if [ ${var1} -eq 2 -a ${var2} -eq 0 ]
		then
			break 2
		else
			echo "var1 = ${var1} var2 = ${var2}"
		fi
	done
done
```

break 2表示直接跳出外层循环，运行结果如下：

```Linux
var1 = 1 var2 = 0
var1 = 1 var2 = 5
```

## continue 命令

continue 是跳出本次循环，进行下一次循环，与 break 和 break 1 不同。

continue 结束此次执行，进行下一次循环。

```bash
#!/bin/bash
for var in 1 2 3 4 5
do
	if [ ${var} -eq 3 ]
	then
		continue
	else
		echo "var = ${var}"
	fi
done
```

运行结果入下：

```Linux
var = 1
var = 2
var = 4
var = 5
```

break 会跳出循环，举例：

```bash
#!/bin/bash
for var in 1 2 3 4 5
do
	if [ ${var} -eq 3 ]
	then
	# break 与 break 1 相同
		break 
	else
		echo "var = ${var}"
	fi
done
```

运行结果如下：

```Linux
var = 1
var = 2
```

同样，continue 后面也可以跟一个数字，表示跳出第几层循环。

```bash
#!/bin/bash

for var1 in 1 2 3
do
	for var2 in 1 2 3
	do
		if [ ${var2} -eq 2 ]
		then
			# continue 与 continue 1 意义相同
			continue 1
		else
			echo "var1 = ${var1} var2 = ${var2}"
		fi
	done
done
```

运行结果如下：

```Linux
var1 = 1 var2 = 1
var1 = 1 var2 = 3
var1 = 2 var2 = 1
var1 = 2 var2 = 3
var1 = 3 var2 = 1
var1 = 3 var2 = 3
```

若将 continue 1 改为 continue 2，运行结果如下：

```Linux
var1 = 1 var2 = 1
var1 = 2 var2 = 1
var1 = 3 var2 = 1
```

------

# Shell函数

Shell 支持函数，但必须先定义后使用。

函数格式如下：

```bash
# 在函数名前加上关键字 function 也可以。
func () {
	list of commands
	[ return value ]
}
```

## 函数返回值

函数返回值，可以添加return语句，如果没有，会将最后一条命令运行结果作为返回值。

Shell 函数返回值只能是整数，一般用来表示执行是否成功，0表示成功，其他值表示失败。如果return其他数据，比如字符串，往往会提示错误：“numeric argument required”。

如果一定要让函数返回字符串，可以先定义一个变量，用来接收函数的计算结果，脚本在需要的时候访问这个变量来获得函数的返回值。

```bash
#!/bin/bash

add () {
	printf "Please input a number:"
	read num
	return `expr ${num} + 1`
}
```

## 函数调用

调用函数只需写出函数名，不需要加括号。

函数返回值在调用该函数后用 $? 来获取。

```bash
#!/bin/bash

add () {
	printf "Please input a number:"
	read num
	return `expr ${num} + 1`
}

main () {
	echo "Hello Function!"
	add
	echo "res = $?"
}

main
```

运行结果如下：

```Linux
Hello Function!
Please input a number:4
res = 5
```

## 删除函数

删除函数可以使用 unset 命令，但是要加上 .f 选项，删除后的函数不能再使用。

```bash
$unset .f func
```

## 在终端调用函数

如果想直接从终端调用函数，可以将函数定义在主目录下的 .profile 文件，这样每次登录后，在命令提示符后面直接输出函数名可以立即调用。

------

# Shell 函数参数

在Shell中，调用函数时可以向其传递参数。在函数内部，通过 $n (n<10) 或者 ${n} (n>=10)的形式来获取参数的值，例如，$2表示第二个参数，${10}表示第10个参数。

```bash
#!/bin/bash

main () {
	echo "第二个参数:$2"
	echo "第三个参数:${3}"
	# $10 表示 $1 和 0
	echo "第十个参数:$10"
	echo "第十个参数:${10}"
	echo "参数个数:$#"
	echo "所有参数:$*"
}

main a b c d e f g h i j k l m n
```

运行结果如下：

```bash
第二个参数:b
第三个参数:c
第十个参数:a0
第十个参数:j
参数个数:14
所有参数:a b c d e f g h i j k l m n
```

------

# Shell 输入输出重定向

Unix 命令默认从标准输入设备（stdin）输入，将结果输出到标准输出设备（stdout）显示。一般情况下，标准输入设备就是键盘，标准输出设备就是终端（显示器）。

## 输出重定向

命令的输出不仅仅可以是显示器，还可以输出到文件，这被称为输出重定向。

输出重定向格式为：

```bash
command > file
```

举例：

```bash
#!/bin/bash

# 输出 abc 到文件123.txt
echo abc > 123.txt
# 输出重定向会覆盖文件内容adb
echo mn > 123.txt
# 使用 >> 可以将输出追加到文件末尾
echo xyz >> 123.txt
```

运行结果如下：

```Linux
$ cat 123.txt 
mn
xyz
```

## 输入重定向

和输出重定向一样，Unix命令可以从文件获取输入。

输入重定向格式为：

```bash
command < file
```

举例：

```bash
#!/bin/bash

wc -l < 123.txt
echo "$?"
```

运行结果入下：

```bash
$ ./test.sh 
4
# 不明白0是哪里来的
0
```

## 重定向深入讲解

一般情况下，每个命令运行时都会打开三个文件：

- 标准输入文件（stdin）：stdin的文件描述符为0，默认从stdin中读取数据
- 标准输出文件（stdout）：stdout的文件描述符为1，默认向stdout输出数据
- 标准错误文件（stderr）：stderr的文件描述符为2，默认回向stderr写入错误信息

command > file 将 stdout 重定向到 file，command < file 将 stdin 重定向到 file。

```bash
# 2 表示 stderr 的文件描述符
# 将 stderr 重定向到 file
command 2 > file
# 将 stderr 重定向追加到 file 末尾
command 2 >> file
# 将 stdout 和 stderr 合并后重定向到 file
command > file 2>&1
# 或者
command >> file 2>&1
# 将 stdin 重定向到 file1，再将 stdout 重定向到 file2。
command < file1 > file2
```

<center>全部可用的重定向命令列表</center>

| 命令            | 说明                                             |
| --------------- | ------------------------------------------------ |
| command > file  | 将输出重定向到 file                              |
| command < file  | 将输入重定向到 file                              |
| command >> file | 将输出以追加的方式重定向到 file                  |
| n > file        | 将文件描述符为 n 的文件重定向到 file             |
| n >> file       | 将文件描述符为 n 的文件以追加的方式重定向到 file |
| n >& m          | 将输出文件 m 和 n 合并                           |
| n <& m          | 将输入文件 m 和 n 合并                           |
| << tag          | 将开始标记 tag 和结束标记 tag 之间的内容作为输入 |

## Here Document

Here Document 是在 Linux Shell 中的一种特殊的重定向方式，基本格式如下：

```bash
cmd << delimiter
	Here Document Content
delimiter
```

作用是将两个 delimiter 之间的内容传递给 cmd 作为输入参数。

注意：

- delimiter 只是一个标识而已，可以替换成任意的合法字符；
- 结尾的 delimiter 一定要顶格写，前面不能有任何字符，后面也不能有任何字符，包括空格和tab缩进；
- 开始的 delimiter 前后的空格会被忽略掉。

例如在终端输入 cat << EOF，系统会提示继续输入，当再次输入EOF时，中间的信息会显示在终端上。

```bash
# ">"是终端产生的提示输入信息的标识符
$ cat << EOF
> 123
> 456
> 789
> EOF
123
456
789
```

Here Document 不仅可以在终端上使用，也可以在Shell文件中使用。如下：

```bash
#!/bin/bash

# 创建新的脚本并写入内容
cat << EOF > output.sh
#!/bin/bash

echo "Hello"
echo "World"
EOF

# 为新脚本增加可执行权限
chmod +x output.sh
```

运行结果如下：

```Linux
$ ./test.sh 
$ ls
output.sh  test.sh
$ ./output.sh 
Hello
World
```

Here Document 的内容，不仅可以包括普通字符，还可以在里面使用变量。例如：

```bash
#!/bin/bash

cat << EOF > output.sh
#!/bin/bash

echo "Hello"
# $1 被展开为第一个参数
echo $1
EOF

chmod +x output.sh
```

运行结果如下：

```Linu
$ ./test.sh World
$ ./output.sh 
Hello
World
```

如若不想展开 $1，可以在 delimiter 上添加双引号来实现。如下：

```bash
#!/bin/bash

cat << "EOF" > output.sh
#!/bin/bash

echo "Hello"
echo $1
EOF

chmod +x output.sh
```

运行结果如下：

```Linu
$ ./test.sh World
$ ./output.sh 
Hello

$ cat output.sh 
#!/bin/bash

echo "Hello"
echo $1
```

## /dev/null

如果希望执行某个命令，但又不希望在屏幕上显示输出结果，则可以将输出重定向到/dev/null：

```bash
command > /dev/null
```

/dev/null 是一个特殊的文件，写入到它的内容会被丢弃，将命令的输出重定向到它，会起到“禁止输出”的效果。

```bash
command > /dev/null 2>&1
```

------

# Shell 文件包含

Shell 可以包含外部脚本，将外部脚本的内容合并到当前脚本。

Shell 中包含脚本格式如下：

```bash
# 第一种方式
. filename
# 第二种方式
source filename
```

一般使用点，但注意点与文件名之间有空格。

```bash
# android 系统编译前配置环境，下面两种效果一样
. build/envsetup.sh
source build/envsetup.sh
```

举例：

创建第一个脚本abc.sh，内容如下：

```bash
url="www.google.com"
```

创建第二个脚本xyz.sh，内容如下：

```bash
#!/bin/bash

. ./adb.sh
echo ${url}
```

执行结果如下：

```Linux
$ chmod +x xyz.sh 
$ ./xyz.sh
www.google.com
```

注意：被包含的脚本不需要可执行权限。