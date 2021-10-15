# 场景描述：
> 1. 在爬虫开发中，可能有小伙伴会遇到浏览器正常访问，但是代码始终无法获取，返回403等；
> 2. 在返回的源码中，我们能很清晰的看到下图所示的字样
> 
![报错页面.png](https://upload-images.jianshu.io/upload_images/13183156-28e3e4e506acf683.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
# 问题解决
* 类似于这种需要等待的网站（一般等待5S，所以也称为5s盾），80%可以判定为使用了5s盾反爬。
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
`还有一个库（cfscrape）和cloudscraper用法一模一样，但是经过测试，该库失败`

# 参考文献
https://github.com/VeNoMouS/cloudscraper
