**sed在指定行插入新行**

1.在指定行前插入一行

```shell
sed '2 ittt' -i a.txt 
```

在第2行前插入ttt，并且将结果更新到a.txt（如果不想将插入真正更新到文件，去掉后面的-i选项就可以）

代码中的 'i' 指的是在第2行前,必须要写

2.在指定行后插入一行

```shell
sed '2 attt' -i a.txt 
```

在第2行后插入ttt,并且将结果更新到a.txt（如果不想更新原文件，去掉-i）

代码中的 'a' 指的是在第2行后,必须要写

**'a' = after   ;  'i' = infont**





例如将test.txt文件内的以1开头的行，替换为"aa/bb"
使用命令

```shell
sed -i ".bak" "s/1.*/aa\/bb/" test.txt
```


其中：

-i ".bak"是直接操作文件并添加.bak作为备份文件名称，如果不需要备份文件，则使用-i ""
s/代表’“substitue”,即替换
1.* 代表以1开头的所有字符串，.*在正则表达式中表示所有字符
aa\/bb使用了转义字符表示aa/bb。
注意最后还需要一个分界符/，否则会提示unterminated substitute in regular expression
分界符也可使用|，那么特殊符号就不需要转义字符了，命令就变成：
sed -i ".bak" "s|1.*|aa/bb|" test.txt
如果需要在文件中替换多个相同的字符串，需要添加global参数g，即:
sed -i ".bak" "s/1.*/aa\/bb/g" test.txt
或者
sed -i ".bak" "s|1.*|aa/bb|g" test.txt
