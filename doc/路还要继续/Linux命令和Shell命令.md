## Shell命令和Linux命令
### Shell
shell是c语言编写的程序，是用户使用linux的桥梁。即是一种命令语言，也是一种程序设计语言。

shell是一种应用程序，这个程序提供一个界面，用户通过这个界面访问操作系统内核的服务。
#### shell脚本
shell script，是一种为shell编写的脚本程序。

.sh 文件

运行:

1. chmod 777 xx.sh ./xx.sh
2. sh xx.sh


#### shell变量
##### 定义变量
###### 规则
- 命名只能使用英文字母，数字和下划线，首个字符不能以数字开头。
- 中间不能有空格，可以使用下划线（_）。
- 不能使用标点符号。
- 不能使用bash里的关键字（可用help命令查看保留关键字）。

###### 显式赋值
	my_dir = "/home/dev"
	_var
    RUNOOB
###### 隐式赋值
	for file in 'ls /etc'
##### 使用变量
###### 使用变量的方式
	${my_dir}
    $my_dir
大括号表示变量名的边界，推荐加上大括号，养成良好的编程习惯

##### 只读变量
使用readonly命令可以将变量定义为只读，只读变量不能被改变。

	readonly my_dir
##### 删除变量
使用unset命令可以将变量删除，删除之后的变量不能再次使用，不能删除只读变量

	unset var
##### 变量类型
###### 局部变量
###### shell变量
###### 环境变量
##### shell字符串
###### 单引号
	str='this is a string'
    
规则：
- 单引号里的任何字符都会原样输出，单引号字符串中的变量是无效的；
- 单引号字串中不能出现单引号（对单引号使用转义符后也不行）。
###### 双引号
	your_name='dou sparky'
    str="hello, i know you are \"${your_name}\"! \n"
    
规则：
- 双引号里可以使用引用变量
- 双引号可以使用转义字符

###### 拼接字符串
	your_name='dou sparky'
    greeting="hello, $your_name !"
    greeting_2="hello, "$your_name" !"
    echo $greeting $greeting_2 # hello, dou sparky ! hello, dou sparky ！输出结果相同
    
###### 提取字符串
格式为: ${STR:start:end}
	
    string='runoob is great sit'
    echo ${string:1:4} # 输出unoo
###### 获取长度
格式为: ${#STR}

	string='my name is sparky'
    echo ${#string} # 输出17
###### 截取子串
格式为: `expr index "$STRING" REGEX`

	string='my name is xxx'
    echo `expr index "${string}" name`
#### shell传递参数
##### 添加参数
在运行shell脚本的输入的命令后可以加上参数传递给shell脚本，参数可以用空格隔开。

	sh xx.sh 参数1 参数2...
    ./xx.sh 参数1 参数2
    
##### 获取参数
输入的参数用$index接收，indenx表示第几个参数，$0表示执行的文件名，$n表示第n个参数
	
    echo "shell 传递参数实例！"
	echo "执行的文件名为 $0"
	echo "传递的第一个参数为 $0"
	echo "传递的第二个参数为 $1"
    
之外还有几个特殊命令:

- $# 表示参数的个数
- $* 表示所有的参数
- $@ 表示所有的参数
- $$ 当前进程id
- $! 后台运行的最后一个进程id
- $- shell当前选项
- $? 显示最后退出状态，0表示没有错误

#### shell数组
shell数组只支持一维数组，有下标，从0开始

shell数组用()表示，元素用空格隔开

	array_name=(value1 value2 ...)
    
##### 获取单个元素
获取单个元素值的方式为 value=array_name[index]可以用$引用
	
    my_array=(a b "c" D)
	value=my_array[0]
    echo "${my_array[1]}"
    
##### 获取数组所有元素
表达式为${array_name[*]}或者${array_name[@]}

	echo "${my_array[@]}"
    echo "${my_array[*]}"
##### 获取数组元素个数
表达式为${#array_name[@]}或者${array_name[*]}
	
    echo "${#my_array[@]}"
    echo "${#my_array[*]}"
#### shell运算符
shell同其它编程语言一样支持多种运算符
- 算数运算符
- 关系运算符
- 布尔运算符
- 字符串运算符
- 文件测试运算符

原生的bash不支持简单的数学运算，但是提供了其它命令来实现，比如expr,awk。expr是一款表达式计算工具，使用它能完成表达式的求职操作。

格式为 `expr m * n`。需要注意的是

- 表达式和运算符之间要有空格,2 + 2是对的， 2+2是错的。
- 完整的表达式要被``包含

	val=`expr 2 + 2`
   
##### 算数运算符
\+ - × / % = == !=

	a=10
    b=20
    val=`expr a \* b`
    echo $val
    
    if [ $a == $b ]
    then 
    	echo "a等于b"
    fi

> 注意

- 乘号(*)前边必须加反斜杠(\)才能实现乘法运算；
- if...then...fi 是条件语句，后续将会讲解。
- 在 MAC 中 shell 的 expr 语法是：$((表达式))，此处表达式中的 "*" 不需要转义符号 "\" 。

##### 关系运算符
- -eq 检测两个数是否相等,相等返回true [ $a -eq $b] 
- -ne 检测两个数是否不相等,不相等返回true [ $a -ne $b ]
- -gt 检测左边的数是否大于右边的数 [ $a -gt $b ]
- -lt 检测左边的数是否小于右边的数 [ $a -lt $b ]
- -ge 检测左边的数是否大于等于右边的数 [$a -ge $b ]
- -le 检测左边的数是否小于等于右边的数 [$a -le $b ]
##### 布尔运算符
- ! 非运算 [!$a]
- -o 或运算 [ $a -0 $b ]
- -a 与运算 [ $a -a $b ]
- && 双与(逻辑与) [[ $a && $b ]]
- || 双或(逻辑或) [[ $a || $b ]]

##### 字符串运算符
- = 检测两个字符串是否相等 [ $a = $b ]
- != 检测两个字符串是否不相等 [ $a != $b ]
- -z 检测字符串长度是否为0，如果为0返回true [ -z $a ]
- -n 检测字符串长度是否为0，如果不为0返回true [ -n $a ]
- str 检测字符串是否为空 [$a]
##### 文件操作运算符
- -b file 检测文件是否是块特殊文件 [ -b $file ]
- -c file 检测文件是否是字符特殊文件 [ -c $file ]
- -d file 检测文件是否是目录
- -f file 检测文件是否是单个文件
- -g file 检测文件是否设置了sgid位
- -k file 检测文件是否设置了黏着位
- -p file 检测文件是否有管道
- -u file 检测文件是否设置suid位
- -r file 检测是否可读
- -w file 检测是否可写
- -x file 检测文件是否可执行
- -s file 检测文件是否为空(size是否>0)
- -e file 检测文件或目录是否存在
    
#### shell基本命令
###### echo
命令格式

	echo string
    
1. 显示普通字符串 echo string
2. 显示转义字符 echo -e "\"\""
3. 显示变量 echo ${a}
4. 显示换行 echo -e "\n"
5. 显示不换行 echo -e "\c"
6. 显示原样字符串 echo '$a\n'
7. 显示结果重定向到文件 echo string > test.txt
8. 显示运算结果 echo `date`


###### printf
printf的命令语法：

	printf format-string [arguments...]
    # format-string为控制字符串
    # arguments为参数列表，用空格隔开

	printf "%-10s %-8s %-4s\n" 姓名 性别 体重kg  
	printf "%-10s %-8s %-4.2f\n" 郭靖 男 66.1234 
	printf "%-10s %-8s %-4.2f\n" 杨过 男 48.6543 
	printf "%-10s %-8s %-4.2f\n" 郭芙 女 47.9876 
    
printf的转移序列

- \a 警告字符，通常为ASCII的BEL字符
- \b 后退
- \c 不显示输出结果中任何结尾的换行字符
- \f 换页
- \n 换行
- \r 回车
- \t 水平制表符
- \v 垂直制表符
- \\ 反斜杠
- \ddd\ 表示1-3位数的八进制的字符，尽在格式字符串有效
- \0ddd 表示1-3位数的八进制字符
###### test
test命令用于检查某个条件是否成立，可以进行数值，字符串，文件三个方面的测试。

test命令格式为
	
    test 表达式

数值

- -eq
- -ne
- -gt
- -ge
- -lt
- -le

字符串

- =
- !=
- -z
- -n

文件测试

- -e
- -d
- -f
- -w
- -r
- -x
- -s
- -c
- -b

逻辑运算符

- -o
- -a
- !

#### shell流程控制
else里不能为空
##### if else
###### if else基本语法格式为

	if condition
    then 
    	command1
        command2
    else
    	command
    fi
    
###### if

	if condition
    then
    	command
    fi
    
    可以写成一行
    if condition;then command;fi;
    
###### if else-if else
	
    if condition1
    then
    	command1
    elif condition2
    then
    	command2
    else
    	command3
    fi
##### for
for循环的基本格式

	for var in item1 item2 ... itemN
    do
    	commands
    done

写成1行

	for var in item1 item2 ... itemN; do commands; done;
##### while
while循环的格式

	while condition
    do
    	commands
    done
    
无限循环的格式

	while :
    do
    	command
    done
    
    # 或者
    
    while true
    do
    	command
    done
    
    # 或者
    
    for (( ; ; ))
##### until循环
与while循环相反,格式为
	
    until condition
    do
    	command
    done
 
##### case循环
多选择语句，格式为

	case value in
    condition1)
    	commands
        ;;
    condition2)
    	commands
        ;;
    *)
    	commands
        ;;
    esac
#### shell函数
shell中函数定义格式为
	
    [function] funname [()]{
    	action;
        [return int;]
    }
    
说明:
- 可以带function定义，也可以不带，不带任何参数
- 参数返回可以加return 返回值；如果不加，将在最后一条命令运行结果。return 后跟数值n

##### 函数参数
与向脚本传递参数类似，方法的参数也可以通过类似的方法传递和获取

$?

#### shell输入输出重定向

- command > file 将输出重定向到file
- command < file 将输入重定向到file
- command >> file 将输出以追加的方式重定向到file
- n > file 将文件描述为n的输出重定向到file
- n >> file 将文件描述为n的输出以追加的方式重定向到file
- n >& m 将输出文件m和n合并
- n <& m 将输入文件m和n合并
- << tag 将开始标记和结束标记之间的内容作为输入

#### shell文件包含
使用.或者source命令添加其它脚本

	. ./xx.sh
    # 或者
    source ./xx.sh
   
> 注意:
> 1. 变量赋值的时候=两边不能有空格
> 	my_dir='/home/sparky'是对的
> 	my_dir = '/home/sparky'是错误的

### Linux
#### linux简介
#### linux安装
#### linu过程
#### linux系统目录结构
#### linux忘记密码
#### linux远程登录
#### linux文件基本属性
#### linux文件和目录管理
#### linux磁盘管理
#### linux vi/vim
#### linux yum命令