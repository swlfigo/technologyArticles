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