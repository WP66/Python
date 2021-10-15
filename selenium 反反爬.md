```写在前面：该篇文章是学习selenium反反爬的记录文章，会逐步完善，并不代表是selenium所有的反反爬方式，还请大佬纠正指点！```

# 1. 修改特征值
> 问题：
> 在我们使用自动化脚本工具抓取网页的时候，在网页控制台中执行这一段JS代码`
> window.navigator.webdriver`会返回true；而人工手动打开的网页则会返回false；这就是目标网站判断我们是不是自动化工具的手段之一。
> ![未修改之前.png](https://upload-images.jianshu.io/upload_images/13183156-8c7d06bff4f47aa6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

####解决方式：
#####已失效方法：
1. 修改特征值：
```
# 以Chromedriver举例
chrome = webdriver.ChromeOptions()
# 打开这个参数
chrome.add_experimental_option('excludeSwitches', ['enable-automation'])
# 设置driver
driver = webdriver.Chrome(options=chrome, executable_path=chrome_path)
```
但是有的朋友可能会发现，**该方法无效**，这是因为在谷歌79版本之后，修复了启用自动化时`window.navigator.webdriver`为未定义的问题。所以该方法失效！解决方式可以回退Chrome浏览器版本，有兴趣的朋友可以尝试一下~
失效之后我在网上查找了很多使用CDP（`Google `的`Chrome Devtools-Protocol`（Chrome 开发工具协议））修改特征值的方法，都未成功。
看看[CDP官网](https://chromedevtools.github.io/devtools-protocol/tot/Page/#method-addScriptToEvaluateOnNewDocument)有这么一段
![image.png](https://upload-images.jianshu.io/upload_images/13183156-a51233e2f19b0ee8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
意思就是在driver打开每一个页面之前（网站还未加载JS代码时），可以先执行我们自己的代码；但是我尝试了很多执行JS都无效，不能修改成功。
#####目前成功方法：
只需要添加`--disable-blink-features=AutomationControlled`参数即可；上边代码修改为
```
# 以Chromedriver举例
chrome = webdriver.ChromeOptions()
# 打开这个参数
chrome.add_experimental_option('excludeSwitches', ['enable-automation'])
# 将window.navigator.webdriver特征值去除
chrome.add_argument("--disable-blink-features=AutomationControlled")
# 设置driver
driver = webdriver.Chrome(options=chrome, executable_path=chrome_path)
```
```第一次的失效代码保留或删除都不影响结果```
看看执行结果：
![修改之后.png](https://upload-images.jianshu.io/upload_images/13183156-5abadec83e4e90ff.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
这里显示执行结果为undefined，即修改成功！
#2. UserAgent信息修改
在我们开发的时候，使用selenium最常用的还是需要无头模式（headless），但是有一个问题是，在使用无头模式的时候，UserAgent信息可能会与正常用户的UA不一致，导致访问失败；这也就是为什么有的朋友会遇到，**有头能正常访问，无头就出错**的问题。
#####解决方法：
只需要给driver手动添加一个UserAgent信息即可
```
ua = 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/90.0.4430.93 Safari/537.36'
chrome.add_argument(f"user-agent={ua}")
```
