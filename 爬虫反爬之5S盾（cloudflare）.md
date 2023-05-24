# 场景描述：
> 1. 在爬虫开发中，可能有小伙伴会遇到浏览器正常访问，但是代码始终无法获取，返回403等；
> 2. 在返回的源码中，我们能很清晰的看到下图所示的字样
> 
![报错页面.png](https://upload-images.jianshu.io/upload_images/13183156-28e3e4e506acf683.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
# 问题解决
### 方法一
* 在python里，有可以绕过这个等待的库 `cloudscraper`
* 使用：

> 安装：
```
pip install cloudscraper
# 更新最新版本
pip install cloudscraper -U
```
> 普通使用
```
# 创建实例
scraper = cloudscraper.create_scraper()
# 请求url
res = scraper.get(url)
# 打印结果
print(res.text)
```
> 在scrapy中使用中间件

middlewares.py
```
class CloudScraperMiddleware:
    def process_response(self, request, response, spider):
        if response.status == 403:
            url = request.url
            req = spider.scraper.get(url, headers={'referer': url})
            return HtmlResponse(url=url, body=req.text, encoding="utf-8", request=request)
        return response
```
spider.py
```
import cloudscraper

# 启用中间件
custom_settings = {
        "DOWNLOADER_MIDDLEWARES": {
            'testspider.middlewares.CloudScraperMiddleware': 520,
        }
    }

def __init__(self, **kwargs):
    # 创建实例
    self.scraper = cloudscraper.create_scraper()
```
> 使用scraper请求url的时候，也可使用代理;
> 
> 源码查看支持的参数如下：

![image](https://user-images.githubusercontent.com/84300396/137427710-afd7bebf-9d19-48aa-bcab-b92905f4e8ef.png)

> 使用：
```
proxies = {"http": "http://localhost:8080", "https": "http://localhost:8080"}
spider.scraper.get(url, headers={'referer': url}, proxies=proxies)
```
`还有一个库（cfscrape）和cloudscraper用法一模一样，但是经过测试，该库失败`

### 方法二：抓取谷歌缓存
* 当谷歌抓取网络以索引网页时，它会创建一个它找到的数据的缓存。大多数受 Cloudflare 保护的网站都让 Google 抓取他们的网站，因此可以抓取此缓存。
> 使用：
> 我们只需要在url前加上`https://webcache.googleusercontent.com/search?q=cache:`即可，如：
```
import requests
url = 'https://webcache.googleusercontent.com/search?q=cache:https://www.xxx.com/'
response = requests.get(url)
```
> 这时我们就可以从响应中提取到我们想要的数据
### 方法三：undetected_chromedriver
* 还在使用selenium抓取网页被封吗？还在设置冗长的chromedriver参数吗？简单易上手的undetected_chromedriver出现了。
* 顾名思义，这也是一个自动化工具，不过他更简单，更不容易被封禁，甚至不用下载驱动。
> 安装：
```
pip3 install undetected-chromedriver
```
> 简单使用：
```
import undetected_chromedriver as uc

url = 'https://www.baidu.com/'
driver = uc.Chrome()
driver.get(url)
```
> 详细用法可以去看看：https://github.com/ultrafunkamsterdam/undetected-chromedriver
### 方法四：使用三方接口（付费）
* 目前已经有很多成熟的代理可供企业/个人使用，我这里找了一个，试用一下看看效果
```
url = "https://xxxx.com/"
# 这个密钥是用我自己的邮箱生成的
api_key = '一长串密钥'
proxy = f"http://{api_key}:@proxy.zenrows.com:8001"
proxies = {"http": proxy, "https": proxy}
response = requests.get(url, proxies=proxies, verify=False)
```
> 试用完全没问题（毕竟是收费的）


# 参考文献
* https://github.com/VeNoMouS/cloudscraper
* https://github.com/ultrafunkamsterdam/undetected-chromedriver
* https://scrapeops.io/web-scraping-playbook/how-to-bypass-cloudflare/#option-2-scrape-google-cache-version
