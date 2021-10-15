> 由于scrapy目前不支持payload格式的formdata请求，所以如果直接写scrapy.FormRequest()会出现401或400错误，看一个例子：
```
# payload参数
payload_query = {"query": {
                        "allOf": [{"allOf": [{"anyOf": [{"is": {"name": "xxxName", "value": "xxxvalue"}}]}]}]}, 
                         "page": '1',
                         "pageSize": '100',
                         "sorting": {"column": "xxxxxx", "order": "desc"}}
url = 'https://www.xxxx.com/'
# 请求头
headers = {
                'authority': 'api.xxx.xxxx.com',
                'method': 'POST',
                'path': '/xxxx/xxx/xxx',
                'scheme': 'https',
                'accept': '*/*',
                # 不必要的参数注释掉
                # 'accept-encoding': 'gzip, deflate, br',
                # 'accept-language': 'zh-CN,zh;q=0.9',
                # 'Connection': 'keep - alive',
                # 'content-length': '199',
                'content-type': 'application/json',
                'origin': 'https://xxx.xxxx.com',
                'referer': 'https://xxxxxxxx.com',
                'user-agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/78.0.3904.70 Safari/537.36',
}
```
> 如果将payload参数直接当做formdata访问，会出现401等错误
```
yield scrapy.FormRequest(url=url, formdata=payload_query, headers=headers)
```
> 结果如下

![image.png](https://upload-images.jianshu.io/upload_images/13183156-36fa83069a143950.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
>正确的做法：
```
yield scrapy.Request(url=url, body=json.dumps(payload_query), method='POST', headers=headers)
```
>运行结果

![image.png](https://upload-images.jianshu.io/upload_images/13183156-3ff6d84405094b72.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
