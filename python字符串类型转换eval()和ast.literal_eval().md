# 在Python中，如果要将字符串类型的列表、元祖、字典等转换成对应的类型，可以使用以下方法
> eval()和ast.literal_eval()

例如：
```
# 形如列表的a字符串
a = '[1,2,3]'
b = eval(a)
c = ast.literal_eval(a)

# 输出结果都是列表形式
[1, 2, 3]
```
#### 那么这两者之间有没有什么区别？
> eval()还有一个功能就是可以进行运算，比如：
```
eval('1 + 1')
# 输出int型的2
2
```
> 不仅如此，还可以进行系统命令的执行，如：

![listdir.png](https://upload-images.jianshu.io/upload_images/13183156-ff26bd88d0850b17.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> 这样就存在安全隐患，万一用户错误执行了一个危险的系统命令，比如：删除某些重要文件 `_ _import_ _('os').system('rm -rf /etc/*')`，或者格式化磁盘等命令；这样带来的后果是不堪设想的。
#### 所以ast.literal_eval()出现了

> 我们先来执行一下正常运算：
```
ast.literal_eval('1+1')
# 输出也是int型的2
2
```
> 再执行一个系统指令：

![listdir2.png](https://upload-images.jianshu.io/upload_images/13183156-bed32b882abaacec.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> 结果执行报错：不合法的字符串

> 原因：ast模块是帮助Python应用来处理抽象的语法解析的。而该模块下的literal_eval()函数：则会判断需要计算的内容计算后是不是合法的python类型，如果是则进行运算，否则就不进行运算。

##所以建议大家，在对字符串进行类型转换的时候，优先使用ast.literal_eval()
