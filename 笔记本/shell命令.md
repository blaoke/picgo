---
title: shell 命令
tags: 
notebook: 工作
---

# 1. 基本命令：
- `#!/bin/bash`:其中 `#!`告诉系统使用哪种shell
- `echo`: 向窗口输出文本
- `#`:开头的行就是注释
- `^`:一行的开头
- `$`:一行的结尾
## 1.1变量：
- 变量定义时，`=`的左右 不能随意加空格
- **使用**定义过的变量：在变量名前面加`$`,可以用 `{ }`包住变量
- 设置为 只读变量：`readonly 变量名`
- 删除变量: `unset 变量名` （unset 命令不能删除只读变量）
- 提取变量长度：在变量名前 使用 `#`
  - eg:`${#string}`:提取字符串长度
- 变量赋值时，前面不能添加`$`,只有在使用时才加

## 1.2数组
- 定义： 用括号来表示数组，数组元素用"空格"符号分割开: `数组名=(值1 值2 ... 值n)`
- 读取： `${数组名[下标]}` , `${array_name[@]}`： **@**符号获取数组中的所有元素
- 获取长度：`${#array_name[@]}` ： `#`用于获取长度

## 1.3参数
- 脚本中获取参数：`$n`: n代表数字
  - $0：执行的脚本名
  - $#: 传递的参数个数
  - $1：第一个参数，......类推
  - ${10}: 第10个参数（当n>=10时，获取参数使用 `${n}`）
  - $*:返回所有参数
  - $@：返回每一个参数(这两个命令效果类似，结果不同)

## 1.4运算符
- `[ expr ]`(Notice:方括号两边和expr中变量两边都必须有空格) 与 `test expr` 作用相同，其中的比较运算符 ==和！= 只能用于字符串比较，-eq，-gt等用于整数比较
- `[]`与 `[[]]`区别：
  - `[]`逻辑测试时使用 `-a,-o，！`，而 `[[]]`可以使用 `&&，||，！`
  - `[]`不能用于数学运算，`[[]]`可以使用 `+,-,*,/`
### 算数运算
- 使用 `expr`计算工具，\` `expr 运算表达式` \`
- 变量与运算符之间要有空格，表达式要放在 反引号 中
- eg: \` `expr $a + $b` \`
- 条件表达式 要放在方括号之中，同样要有空格将 运算符与变量分开
- eg: `[ $a -eq $b ]`

### 关系运算符（只支持数字）
> shell 中没有< >= 等符号，都用 `-字母代号` 形式代替
- `-eq`:两个数字是否相等
- `-ne`:两个数字是否不相等
- `-gt(e)`:左大于(等于)右，`-lt(e)`:左小于(等于)右
- eg：`if [[ $# -gt 0 ]]`:如果传递的参数大于0

### 布尔运算符
- `!`:非
- `-o`:或
- `-a`:与

### 字符串运算符
`-z`字符串长度是否为0
`-n`字符串长度是否不为 0
`$`字符串是否不为空 eg: [$`abc`]->返回 true。

### 文件运算符
|操作符名称|说明|
|-|-|
|-e|文件是否存在|
|-d|文件是否为目录|
|-f|文件是否为常规文件(既不是目录，也不是设备文件)|
|-p|文件是否是有名管道
|-s|文件是否不为空
|-r(w)(x)|文件是否可读(可写)(可执行)
|file1 -n(o)t file2| file1是否比file2新(旧)
### 示例：
- if利用read传参判断
```cpp
read -p "please input a score:" score 
if  [ "$score" -ge "0" ] #注意空格
then
    command
```

## 1.5 other
- `read`:将从终端读取的一行输入 赋值给shell中变量
  - eg: `read -p "input name" name`:将输入赋值给 变量name, 其中 `-p`：显示提示符    
- `test`:检查 某个表达式(数字，字符，文件) 是否成立
- `wc -l file_name`:获取文件的行数
- `chmod +x file_name.sh` : 对shell脚本赋可执行权限（如果.sh文件不能运行，需要这一步操作）
  - + 表示增加权限、- 表示取消权限、= 表示唯一设定权限。
  - r 表示可读取，w 表示可写入，x 表示可执行
### 1.5.1 单引号 与 多引号 区别：
- 单引号 `' '`包含什么就输出什么，不会对包含的内容进行分析
- 双引号 `" "`输出时会先解析包含的变量和命令，而不是把双引号中的变量名和命令原样输出，适合字符串中包含变量和命令，需要解析后再输出的情况
```shell
url="abc"
website1='${url}' #单引号
website2="${url}" #双引号（可以根据颜色判断是否为变量）
echo $website1
echo $website2
> ${url} #输出
> abc
```
### 1.5.2 将命令执行的结果赋值给变量
1. 变量名= /`命令/`
2. 变量名=$(命令)
- eg: `log=$(cat log.txt)`:读取文件内容，并将内容保存到log变量


## 1.6 条件语句
> continue：跳出当前循环， break:跳出所有循环
- `if else`
```sh
if  condition 
then
    command1 
else
    command2
fi
```

- `if else-if else `
```sh
if condition1
then
    command1
elif condition2 
then 
    command2
else
    command3
fi
```

- `for`
```sh
for var in item1 item2 ... itemN
do
    command1
done
```

- `while`
```sh
while condition
do
    command
done
```

## 1.7 函数
- 调用函数后函数的返回值通过`$?`获得
```sh
funWithReturn(){
    read aNum  #读取输入并赋值给aNum变量
    read anotherNum  
    echo "两个数字分别为 $aNum 和 $anotherNum !"
    return $(($aNum+$anotherNum))
}
funWithReturn  #调用定义的函数
echo "输入的两个数字之和为 $? !" # $? 输出为return的内容
```
# 2. 输入/输出 重定向
## 2.1 输出重定向（将输出的内容存到文件）
- command > file_name：输出重定向到 file文件中（覆盖文件原内容）
- command >> file_name:输出追加到文件末尾
- command > /dev/null :将输出

- 如果希望执行某个命令，但又不希望在屏幕上显示输出结果，可以将输出重定向到 `/dev/null`(这是一个特殊的文件，写入到其中的内容都会被丢弃，相当于清空的垃圾桶，无法从其中读取任何内容): `command > /dev/null`

## 2.2 输入重定向（本需要从键盘获取输入的命令会转移到文件读取内容）
- command1 < file1 ：输入重定向为从file1文件读取

# shell脚本中 添加包含的文件
- `. filename`   #注意点号(.)和文件名中间有一空格
- `source filename`  #作用与上面写法相同x


# 3. ssh命令参数
## ssh-keygen (生成ssh秘钥，包含id_rsa私钥和id_rsa.pub公钥)
- `-f` :指定秘钥存放位置的文件名，
- `-t` :指定密钥类型，默认是 rsa ，可以省略
- `-C` :设置注释文字
- `-R` ：hostname

# 4. sshpass：远程操作终端
- 本地远程连接主机,并在远程终端执行命令：`sshpass -p ‘user_password’ ssh root@192.168.11.11 "命令内容"`
  - 可选参数：
  - `-o StrictHostChecking=no` ：登录免输入yes
  - `-f filename` : 从文件中读取登录密码
  - `-o UserKnownHostsFile=/dev/null`：默认情况下，ssh会把访问过计算机的公钥(public key)都记录在~/.ssh/known_hosts中，这条命令表示不存公钥

# 5. sed：修改文件中内容
- sed修改与grep搜索及less查看配合使用，实现用shell脚本修改文件，配合sshpass远程修改
## `sed [option] '起始行，终止行function'`:
- option 说明

option|描述
---|---
-i|直接修改文件内容
-e|使一条命令行可以执行多个sed操作（只有一项操作时可以省略）

- function说明

|function|描述|
|---|---|
|a:|新增， a的后面接字串,添加内容在 **下一行**|
|c:|取代，将原行内容取代为指定内容，可以指定替换的始 终 行|
|d:|删除|
|i:|插入，插入内容在 **上一行**|
|s:|替换，类似正则表达式查找替换，默认只替换每一行匹配到的第一个，末尾带 `/g`:将每一行匹配到的所有都替换

### 示例:
- `sed -i '1,20s/原字符串/新字符串/g file_name'`,修改1-20行内所有原字符串为新字符串
- `sed -e 3a\new xxx`:在xxx文件的第三行末尾添加 new,*字符前面用反斜线作区分*
- `sed -e /YYY/a\new xxx`:在xxx文件中的以YYY开头的行末尾添加 new(Notice:-e 操作不会改变原文件内容)
- [more example](https://blog.csdn.net/wdz306ling/article/details/80087889)

```shell
#!/bin/bash
echo 'shs,sdf' > a.txt
echo 'shaaa' >> a.txt
sed -i '1,2s/sh/ss/g' a.txt > a
source a.txt
```

# 6. grep： 搜索命令
>一种支持 正则表达式 的对文本进行搜索的工具,将搜索的结果显示在屏幕上
##  `grep [option] '搜索字符串' filename`
option操作|说明
---|---    
-a|将binary文件以text文件的方式搜寻数据
-c|计算找到'搜寻字符串'的次数
-i|忽略大小写搜索
-n|输出搜索的字符串并 输出行号
-v|反向选择，即输出没有'搜索字符串'的内容
--color=auto|可以将找到的关键词部分加上颜色显示
-A行数|显示匹配到的那行，及 后面的x行
-B行数|显示匹配到的那行，及 前面的x行
-C行数|显示匹配那行前后的x行

- 一般grep与cat,less等一起使用，配合正则表达式
- eg：`cat file_name | grep -n ‘xxx’` :读取文件内容，并搜索xxx关键字
- eg:`less file_name | grep -n 'a[bd]c'`:在文件中搜索 abc与adc这两个关键词


# 7. awk： 文件操作命令
> awk主要是处理文本文件的工具，依次处理文件的每一行，并读取每一个字段
## 7.1 基本用法： 
- **awk '动作' 文件名**
- awk会根据分隔符（默认为空格）和制表符，将每一行分成若干字段，$1、$2、$3分别代表第一个字段、第二个字段、第三个字段，$0表示每一行的所有字段
### 示例
- `awk -F ':' '{print $0}' demo.txt`:将demo.txt文件中的每一行都显示出来，其中`-F`:指定分隔符为冒号，`{ }`:内部是动作内容，`print`:打印，`$0`:每一行

## 7.2 变量：
- `NF`:当前行有多少个字段, `$NF`:表示每一行最后一个字段
- `NR`:当前处理的行数
- `FILENAME`：当前文件名
- `FS`：字段分隔符，默认是空格
- `RS`：行分隔符，默认是换行符。

- eg:`awk -F ':' '{print NR ") " $1}' demo.txt`:以冒号为字段分隔符，打印每行行号）每行第一个字段

## 7.3 函数
- `toupper()`:字符转为大写
- `tolower()`：字符转为小写。
- `length()`：返回字符串长度。
- `substr()`：返回子字符串。
- eg：`awk -F ':' '{ print toupper($1) }' demo.txt`：将第一个字段转换为大写并打印输出 

## 7.4 条件
-  **awk '条件 动作' 文件名**
> 条件可以为 正则表达式命令
### 示例
- `awk -F ':' 'NR % 2 == 1 {print $1}' demo.txt`:输出奇数行的第一个字段
- `awk -F ':' 'NR >3 {print $1}' demo.txt`: 输出第三行之后的每行的第一个字段