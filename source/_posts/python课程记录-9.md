---
title: python课程记录-9
date: 2020-04-22 09:25:21
tags: python
category: 课程笔记
---

这一课是爬虫。

<!--more-->

1. 使用两个库：
   1.  Requests：获取网络数据
   2. BeautifulSoup：分析网络数据

### Requests

1. 获取某网址信息：`requests.get(url, params=None, **kwargs)`

   例如：

   ```python
   import requests
   html= requests.get("html://www.bnu.edu.cn")
   with open('bnu.html','wb') as f:
       f.write(html.content)		# 保存html的内容
   type(html)		# requests.models.Response
   html.url		# 'https://www.bnu.edu.cn/'
   html.content	# 二进制内容
   html.text		# 字符串（可能乱码）
   html.encoding	# 'ISO-8859-1'
   html.encoding='utf-8'  #修改编码
   html.request.headers	# 访问方式： {'User-Agent': 'python-requests/2.22.0', 'Accept-Encoding': 'gzip, deflate', 'Accept': '*/*', 'Connection': 'keep-alive'}
   ```

2. 把访问方式伪装成正常浏览器：

   1. 获取浏览器的访问方式：

      1. 在浏览器中打开链接；

      2. 按F12，找到Network；

      3. 随便选一个资源点一下（如果一个都没有，就刷新一下网页）；

      4. 在Request Headers里找到User-Agent并把整个内容复制出来。

         ![示意图](https://github.com/kun-bin/image/blob/master/0422.png?raw=true)

   2. 在代码中做伪装：

      ```python
      head = {'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/78.0.3904.108 Safari/537.36'}
      html = requests.get("http://www.bnu.edu.cn", headers=head)
      html.request.headers	# 访问方式变成User-Agent: Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/78.0.3904.108 Safari/537.36
      ```

### BeautifulSoup

1. 分析页面信息：

   ```python
   from bs4 import BeautifulSoup
   bsObj = BeautifulSoup(html.content, 'lxml')  #lxml HTML 解析器：速度快，文档容错能力强
   type(bsObj)		# bs4.BeautifulSoup
   bsObj.title     #获得html中的title标签: <title>北京师范大学</title>
   type(bsObj.title )	# bs4.element.Tag
   bsObj.img   #获得html中的第一个img标签: <img alt="" src="images/logo1.png"/>
   bsObj.a    #获得html中的第一个a标签: <a href="https://one.bnu.edu.cn" target="_blank">信息门户</a>
   print(type(bsObj.img), type(bsObj.a ))	# <class 'bs4.element.Tag'> <class 'bs4.element.Tag'>
   ```

2. 获取标签信息：

   ```python
   bsObj.a.get_text()	# '信息门户'
   bsObj.a.text		# '信息门户'
   bsObj.a.attrs		# {'href': 'https://one.bnu.edu.cn', 'target': '_blank'}
   bsObj.a['href']		# 获取a标签的href属性值：'https://one.bnu.edu.cn'
   ```

3. 找到特定标签：`find( name , attrs , recursive , text , **kwargs )`

   ```python
   alink=bsObj.find('a')   # 没有属性限定，找到第一个a标签
   print(alink)			# <a href="https://one.bnu.edu.cn" target="_blank">信息门户</a>
   blink=bsObj.find('a',text='学部院系')	# 限定了属性 text的值
   print(blink)			# <a href="xbyx/index.htm">学部院系</a>
   ```

   找到所有指定标签：`find_all( name , attrs , recursive , text , limit, **kwargs )`返回列表

   ```python
   alink=bsObj.find_all('a')
   print(alink)	# 所有a标签的列表
   blink=bsObj.find_all("a", limit=2)	# 只找前两个a标签
   print(blink)				# [<a href="https://one.bnu.edu.cn" target="_blank">信息门户</a>, <a href="http://xxgk.bnu.edu.cn/" target="_blank">信息公开</a>]
   clink=bsObj.find_all('span', class_="more01")	# class值符合要求的span标签
   print(clink)		# [<span class="more01"><img alt="" src="images/more3.png"/></span>, <span class="more01"><img alt="" src="images/more3.png"/></span>, <span class="more01"><img alt="" src="images/more3.png"/></span>, <span class="more01"><img alt="" src="images/more3.png"/></span>]
   
   import re
   imglist = bsObj.find_all('img', {"src":re.compile(".+\.jpg")})	# 符合正则表达的标签
   print(imglist)
   
   dlink=bsObj.find('div', class_="nav").find_all('a')	# find和findall结合使用
   print(dlink)
   ```

4. 相对url转绝对url：

   ```python
   from requests.compat import urljoin
   
   url='https://www.bnu.edu.cn/'
   blink = urljoin(url, blink['href'])
   print(blink)	# https://www.bnu.edu.cn/xbyx/index.htm
   ```

5. 图像相关（img标签）：

   ```python
   clink=bsObj.find('img')
   print(clink)			# <img alt="" src="images/logo1.png"/>
   print(clink['src'])		# images/logo1.png
   logo= requests.get(urljoin(url, clink['src']), headers=head) #获得图像
   
   # 显示图像
   from PIL import Image
   from io import BytesIO
   im=Image.open(BytesIO(logo.content))
   im.show()
   
   # 保存图像
   with open(r'D:\2020Python\Ch8\BNU-logo.png', 'wb') as f: 
       f.write(logo.content)
   with open(r'D:\2020Python\Ch8\BNU-logo.'+clink['src'][-3:], 'wb') as f: 
       f.write(logo.content)
   ```

6. `class`标签的访问：

   ```python
   dlink=bsObj.find('span', class="col-md-10")  #class 是关键词，不能这样使用
   # 解决方案1
   dlink=bsObj.find('div', class_="item-txt01")
   print(dlink)
   # 解决方案2
   dlink=bsObj.find('div', {'class':'item-txt01'})
   print(dlink.text)
   ```

7. 网页源码树状结构：

   ```python
   clink=bsObj.find('li')
   clink.parent  #父节点
   clink.children  #孩子节点
   for child in clink.children:
       print(child)	# 输出子节点
   for child in clink.descendants:    #子孙节点，后代节点
       print(child)
   for sibling in clink.next_siblings:  #兄弟节点
       print(sibling)
   clink.find_parent('div', class_='nav')  #满足class为‘nav’的父节点
   for child in clink.findChildren('a'):  #找到 a标签 孩子
       print(child)
   ```

### 爬取网页所有图片的示例

```python
#只爬取url网页上的图片，不爬next网页上的图片
import requests
from bs4 import BeautifulSoup
from requests.compat import urljoin
url = 'http://news.bnu.edu.cn/sj/gysd/index.htm'
head = {'User-Agent': 'Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/80.0.3987.132 Safari/537.36'}
html = requests.get(url, headers = head)
bsObj= BeautifulSoup(html.content, 'lxml')
allLinks = bsObj.find_all('div', class_="grid")
for i in allLinks:
    imglink = i.find('img')['src']	
    imglink = urljoin(url, imglink)	# 图片链接
    name = i.find('h3').text		# 图片标题
    print(imglink, name)
    img  = requests.get(imglink, headers = head)
    with open(r'D:\2020Python\Ch8\图片/'+name+imglink[-4:], 'wb') as f:
        f.write(img.content)		# 保存图片
```

```python
#爬取本网页以及下一页的图片
import requests
from requests.compat import urljoin
from bs4 import BeautifulSoup
url='http://news.bnu.edu.cn/sj/gysd/index.htm'
head = {'User-Agent': 'Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/80.0.3987.132 Safari/537.36'}
path='D:/2020Python/Ch8/光影师大/'
import os
if not os.path.exists(path):
    os.mkdir(path)
nexturl=url
while nexturl:
    html = requests.get(nexturl, headers=head)
    bsObj=BeautifulSoup(html.content, 'lxml')
    imgslink=bsObj.find_all('div', class_="grid")
    for i in imgslink:
        imglink=i.find('img')['src']
        imglink=urljoin(nexturl,imglink)
        name=i.find('h3').text
        name=''.join([i for i in name if i not in ['\\','/',':','*','?','"','<','>','|']])
        print(imglink, name)        
        img=requests.get(urljoin(nexturl,imglink), headers=head)
        with open(path+name+imglink[-4:], 'wb') as f:
            f.write(img.content)        
    temp=bsObj.find('a',class_="next")	# 获取next标签（下一页）
    if temp:
        nexturl=urljoin(nexturl,temp['href'])	# 获取下一页的url
    else:
        nexturl=None
```

### 课后作业

抓取北京师范大学中头条关注网站： http://news.bnu.edu.cn/zx/ttgz/index.htm 上迄今为止所 有头条关注报道的发表日期、标题、链接以及浏览次数，保存为 一个csv文件，要求使用函数封装。

思路：

1. 导入所需库

   ```python
   import requests
   import re
   import csv
   from bs4 import BeautifulSoup
   from requests.compat import urljoin
   ```

2. 分析这个网站，发现了很神奇的一点，该网站一共68页，应该只有第68页没有`next`标签，但实际上它第67页就没了。如果用保存图像那个例子里的方式访问，则无法访问到第68页，所以考虑写个函数来获取网站一共多少页，然后`for`循环读网页内容。

   经过仔细观察，可以发现网页下面有一个元素用来显示“现在是第几页/总共多少页”，源代码如下：

   ```html
   <div class="pages ">
   		   			            页数：<span>1/68</span>
             总数：<span>680</span>
   						
       <a href="index.htm" class="on_pages">1</a>
       <a href="index1.htm">2</a>
       <a href="index2.htm">3</a>
       <a href="index3.htm">4</a>
       <a href="index4.htm">5</a>
       <a href="index5.htm">6</a>
       <a href="index6.htm">7</a>
       <a href="index7.htm">8</a>
       <a href="index8.htm">9</a>
       <a href="index9.htm">10</a>
       <a href="index1.htm" class="next"><img src="../../images/pages04.jpg"></a>
       <a href="index10.htm" class="end"><img src="../../images/pages05.jpg"></a>
   </div>
   ```

   因此可以找到`class = pages` 的`div`标签，然后获取其中`span`标签的内容，并截取`/`符号后面的内容转为数字，即可得到网站页数，写成函数的代码如下：

   ```python
   def page(url, head):
       html = requests.get(url, headers=head)
       bsObj = BeautifulSoup(html.content, 'lxml')
       return int(bsObj.find(class_="pages").find('span').text.split('/')[1])
   ```

3. 接下来可以通过`for`循环访问每一页，第一页是`index.htm`，接下来是`index1.htm`等等，直到最后是`index67.htm`。

   1. 分析网页源码可知，在每一页中，我们需要提取的内容一定在`class = item-info01`的标签中，因此使用`findall`然后遍历得到的标签列表。

   2. 分析每个标签源码可知，文章标题出现在`h3`中，即`title = i.find('h3').text`。

   3. 文章链接是`a`标签的`href`属性，是相对链接，要转换为绝对链接，不过这里需要注意，有一些文章用了微信链接等站外网页作为内容，因此这些链接是绝对链接，不需要转换。所以要判断一下首字母是否为`h`。此外，在我们保存的时候，链接后面要加浏览次数，直接用逗号分隔会导致打开csv时链接和浏览次数连在一起，所以链接本身保存时在末尾加个空格。

   ```python
   link = i.find('a')['href']
   if link[0] != 'h':
   	link = urljoin(url, link)
   link += ' '
   ```

   4. 文章时间是`class = time`的`span`标签对应的内容，即`time = i.find('span', class_="time").text`。

   5. 最后是文章浏览次数，这个网站浏览次数是用script做的，所以要跳转到对应链接，然后从新网页里获取浏览次数。对应的相对链接是`script`标签中的`src`属性，因此要跳转的新网页是`urljoin(url, i.find('script')['src'])`，这个网页的内容只有`document.write('31')`这样的字符，所以获取网页内容后提取它17位开始、倒数第3位结束的内容即可得到浏览次数（类型为字符串）。

      ```python
      viewlink = urljoin(url, i.find('script')['src'])
      view = BeautifulSoup(requests.get(viewlink).text, 'lxml').text[16:-3]
      ```

   6. 最后把上述内容按顺序加到列表中，然后在整个循环结束后返回列表即可。完整代码如下：

      ```python
      def analy(page, url, head):
          lists = []
          nexturl = url
          for index in range(page):
              html = requests.get(nexturl, headers=head)
              bsObj = BeautifulSoup(html.content, 'lxml')
              allLink = bsObj.find_all(class_="item-info01")
              for i in allLink:
                  title = i.find('h3').text
                  link = i.find('a')['href']
                  if link[0] != 'h':
                      link = urljoin(url, link)
                  link += ' '
                  time = i.find('span', class_="time").text
                  viewlink = urljoin(url, i.find('script')['src'])
                  view = BeautifulSoup(requests.get(viewlink).text, 'lxml').text[16:-3]
                  lists.append([time, title, link, view])
              nexturl = urljoin(nexturl, 'index' + str(index + 1) + '.htm')
          return lists
      ```

4. 在得到列表后，根据要求输出csv文件，这部分内容是上一课讲过的，基本没有变化，注意编码要用`utf-8-sig`，如果用其他编码，保存的中文会乱码；如果只是`utf-8`没`sig`，则excel打开时中文会乱码。代码如下：

   ```python
   def writecsv(lists):
       with open("reportsInfo.csv", 'w', newline='', encoding='utf-8-sig') as f:
           f_csv = csv.writer(f)
           headers = ['日期', '标题', '链接', '浏览次数']
           f_csv.writerow(headers)
           for row in lists:
               f_csv.writerow(row)
   ```

5. 最后是主函数部分，如下：

   ```python
   head = {
       'user-Agent': 'Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/78.0.3904.108 Safari/537.36'}
   url = 'http://news.bnu.edu.cn/zx/ttgz/index.htm'
   page = page(url, head)
   lists = analy(page, url, head)
   writecsv(lists)
   print("FIN")
   ```
