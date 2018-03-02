# maoyan_movie
python+正则表达式爬取猫眼电影排行榜信息
### 主函数：

```
if __name__ == '__main__':
    for i in range(10):
        html=get_page('http://maoyan.com/board/4?offset='+str(i*10))
        for item in parse_one_page(html):
            print(item)
            write_to_file(item)
```
通过分析网址命名规则不难发现，每页的网址是‘http://maoyan.com/board/4?offset=’+0,10,20，。。。
### get_page函数：

```
def get_page(url):
    try:
        response=requests.get(url)
        if response.status_code==200:
            return response.text
        return None
    except RequestException:
        return None
```
### parse_one_page函数：

```
def parse_one_page(html):
    pattern=re.compile('<dd>.*?board-index.*?">(\d+)</i>.*?data-src="(.*?)".*?name"><a.*?>(.*?)'
                       +'</a>.*?star">\s+(.*?)\s+</p>.*?time">(.*?)</p>.*?integer">(.*?)</i>.*?fraction">(\d)</i>.*?</dd>',re.S)
    items=re.findall(pattern,html)
    for item in items:
        yield {
            'index':item[0],
            'image':item[1],
            'title':item[2],
            'actor':item[3],
            'time':item[4][5:],
            'score':item[5]+item[6]
        }
```
### write_to_file函数：

```
def write_to_file(content):
    with open('result.txt','a',encoding='utf-8') as f:
        f.write(json.dumps(content,ensure_ascii=False)+"\n")
        f.close()
```
json.dumps：将 Python 对象编码成 JSON 字符串（序列化）
python 原始类型向 json 类型的转化对照表：
![](http://img.blog.csdn.net/20180223002641790?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvRFJfZWFtTWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
在序列化时，中文汉字总是被转换为unicode码，在dumps函数中添加参数ensure_ascii=False即可解决
关于open函数的用法可以参见菜鸟教程：http://www.runoob.com/python/python-func-open.html
## 效果：
![猫眼爬虫项目](http://img.blog.csdn.net/20180223001334585?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvRFJfZWFtTWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

整个都很简单，代码一共也不到40行，但是对于初学者是个很好的练习项目。
