```shell
#!/bin/bash
```

- 第一行，告诉系统用什么解释器执行

# 变量

- 作用域

	- 环境变量（全局）：子进程只继承环境变量

		```shell
		export A
		```

	- 局部变量：默认

- 变量内容：

	```shell
	$ 变量
	```

- 变量赋值

	- 右侧有空格，\t，\n时需要引号
	- 默认为空字符串

- 输入输出

	```shell
	read num
	echo num
	```

- 取消

	```shell
	unset A
	```

- `${}`

	```shell
	${ str=expr } #若 $str 没设定，返回expr，同时赋值 $str
	${ str:=expr } #若没设定或为NULL
	```

- 内置变量

	|   变量   |                  意义                  |
	| :------: | :------------------------------------: |
	|   `$!`   |    shell进程最近启动的后台进程的PID    |
	|   `$$`   |             shell自己的PID             |
	|   `$?`   |       最后一次执行的命令的返回码       |
	|   `$#`   | 命令行参数的个数(不包括脚本文件的名字) |
	|   `$0`   |             脚本文件的名字             |
	| `$1, $2` |            第x个命令行参数             |
	|   `$@`   |         all命令行参数，一个个          |
	|   `$*`   |      all命令行参数组织成一个整体       |

# 程序设计

## {} & ()

- {}
- ()：在子进程中

## 条件语句

```shell
read num
if [ $num -lt 60 ]
then
    echo '不及格'
elif [ $num -lt 90 ]
then
    echo '及格'
else
    echo '优秀'
fi
```

- 条件：true == 0, false == 1
- `test` & `[...]`：
	- 对数值的测试
		- eq：等于
		- ne：不等于
		- gt：大于
		- ge：大于等于
		- lt：小于
		- le：小于等于
	- 对字符串：
		- =
		- !=
		- -z：长度==0
		- -n：长度!=0
	- 对文件的测试：
		- -e：存在
		- -s：长度非零
		- 文件类型：
			- -d：目录文件
			- -f：普通文件
			- -c：字符型特殊文件
			- -b：块特殊文件
		- 权限：
			- -r：可读
			- -w：可写
			- -x：可执行

## 跳转语句

```shell
case x in
    1) echo 0;;
    2) echo 1;;
    *)  echo -1;;
esac
```

## 循环

- `while`

	```shell
	while 条件
	do
	    命令
	done
	```

- `until`

	```shell
	until 条件
	do
	    命令
	done
	```

- `for`

	```shell
	for x in list
	do
	    命令
	done
	```

