---
title: typora图床功能设置
date: 2020-07-07 08:33:38
tags: 教程
categories: 博客配置
---

终于不用自己上传github然后再复制链接到markdown了！

<!--more-->

1. 打开typora，文件—>偏好设置—>图像，然后按照下图设置：

   ![img](https://pic3.zhimg.com/80/v2-2f7c965687ee0689ced4a80a608599be_1440w.jpg)

2. 设置好以后点下载或更新，下载更新结束以后，浏览器打开[smms](https://sm.ms/home/apitoken)并注册账号和登录，注册的时候要填用户名和邮箱，登录的时候不是邮箱登录而是用户名登录；

3. 注册并登录以后打开[新链接](https://sm.ms/home/apitoken)，secret token那里是空的，点下面的generate secret token，会得到一串字符，如下图：

   ![img](https://pic2.zhimg.com/80/v2-f8e838a39e52ef233bca3c190af483f1_1440w.jpg)

4. 回到刚才的typora，还是图像那里，打开配置文件，如果要选应用程序就找记事本之类的编辑器；

5. 把下面的代码复制粘贴进去和保存：

   ```json
   {
     "picBed": {
       "uploader": "smms", // 代表当前的默认上传图床为 SM.MS,
       "smms": {
         "token": "这里面的token换成你上个页面的申请的token" //一定要换
       }
     },
     "picgoPlugins": {} // 为插件预留
   }
   ```

6. 到此为止就好了，还是在图像那里，点验证图片上传选项，可以验证一下有没有问题。



参考：[知乎链接](https://zhuanlan.zhihu.com/p/137310314)