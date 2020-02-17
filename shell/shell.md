https://www.runoob.com/linux/linux-shell-passing-arguments.html

https://www.cnblogs.com/qlqwjy/p/8684630.html

* `#!/bin/bash`：约定的标记，告诉系统这个脚本需要什么解释器来执行，即使用哪一种 Shell，例如bash
* `/bin/sh test.sh`：指定解释器执行脚本，`#!/bin/bash`就没用了
* `echo`：向窗口输出文本
* 别忘了加权限：`chmod a=x ./test.sh`(./表示当前目录，不加会被人为是PATH中的命令)

#### Shell变量
##### 变量
* `your_name="runoob.com"`
* 变量名和等号之间不能有空格
* 命名只能使用英文字母，数字和下划线，首个字符不能以数字开头
* 中间不能有空格，可以使用下划线
* 不能使用标点符号
* 不能使用bash里的关键字（可用help命令查看保留关键字）
* 除了显式地直接赋值，还可以用语句给变量赋值：`for file in $(ls /etc)`
* 使用一个定义过的变量，只要在变量名前面加美元符号即可
* 变量名外面的花括号是可选的，加不加都行，加花括号是为了帮助解释器识别变量的边界：`echo ${your_name}`
* 只读变量：`readonly myUrl="http://www.google.com`
* 删除变量：`unset variable_name`

##### 字符串
* shell字符串：单引号里的任何字符都会原样输出，单引号字符串中的变量是无效的
* shell字符串：双引号里可以有变量，双引号里可以出现转义字符
* 获取字符串长度：`${#string}`
* 提取子串：`${string:1:4}`
* 查找子字符串：`index "$string" io`

##### 数组
* 用括号来表示数组，数组元素用"空格"符号分割开：`array_name=(value0 value1 value2 value3)`
* array_name[0]=value0
* 读取数组：valuen=${array_name[n]}
* 获取所有元素：echo ${array_name[@]}
* 获取数组长度：length=${#array_name[@]}

### Shell传递参数
* 脚本传入参数：`$n`
* 

