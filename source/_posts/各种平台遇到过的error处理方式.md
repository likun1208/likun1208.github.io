---
title: 各种平台遇到过的error处理方式
date: 2020-01-31 19:06:23
tags: error
categories: 无分类项
---

记录一下之前的一些问题。

<!--more-->

1. 平台：visiual studio
   error LNK2026: 模块对于 SAFESEH 映像是不安全的 
   解决方法：属性 - > 链接器 - > 附加选项输入 /SAFESEH:NO - > 应用
   
2. 平台：visiual studio
   error C4996: 'fopen': This function or variable may be unsafe. Consider using fopen_sinstead. To disable deprecation, use _CRT_SECURE_NO_WARNINGS. See online help   for details.
   1>f:\program files (x86)\microsoft visual studio 12.0\vc\include\stdio.h(211) : 参见“fopen”的声明
   解决方法：预处理器加入_CRT_SECURE_NO_WARNINGS
   
3. 平台：visiual studio
   error C2664: “int MessageBoxW(HWND,LPCWSTR,LPCWSTR,UINT)”: 无法将参数 2 从“const char [17]”转换为“LPCWSTR” 
   解决方法：项目菜单->属性->配置属性->常规->项目默认值->字符集从unicode改为未设置
   
4. 平台：visiual studio
   error LNK2019: 无法解析的外部符号 _main，该符号在函数 ___tmainCRTStartup 中被引用
   解决方法：右击项目，打开“属性”页，链接器->系统->子系统->/subsystem:windows。如果是刚开始默认的是/subsystem:windows则改为/subsytem:console
   
5. 平台：visiual studio
   error LNK2019: 无法解析的外部符号 __imp__timeGetTime@0，该符号在函数 “void __cdecl TimerInit(void)” (?TimerInit@@YAX 
   解决方法：将winmm.lib打入“附加依赖项”
   
6. 平台：visiual studio
   fatal error LNK1123: 转换到 COFF 期间失败: 文件无效或损坏 
   解决方法：右键->工程属性->配置属性-> 清单工具->输入和输出->嵌入清单，选择[否]
   
7. 突然发现github上的图片打不开，具体来说就是博客里插入了上传到github的图，但是加载不出，这时需要修改hosts文件，如下：

   打开路径C:\Windows\System32\drivers\etc下的hosts文件，在最后加上

   ```
   # GitHub Start 
   192.30.253.112    github.com 
   192.30.253.119    gist.github.com
   151.101.184.133    assets-cdn.github.com
   151.101.184.133    raw.githubusercontent.com
   151.101.184.133    gist.githubusercontent.com
   151.101.184.133    cloud.githubusercontent.com
   151.101.184.133    camo.githubusercontent.com
   151.101.184.133    avatars0.githubusercontent.com
   151.101.184.133    avatars1.githubusercontent.com
   151.101.184.133    avatars2.githubusercontent.com
   151.101.184.133    avatars3.githubusercontent.com
   151.101.184.133    avatars4.githubusercontent.com
   151.101.184.133    avatars5.githubusercontent.com
   151.101.184.133    avatars6.githubusercontent.com
   151.101.184.133    avatars7.githubusercontent.com
   151.101.184.133    avatars8.githubusercontent.com 
    # GitHub End
   ```

8. opencv读取图像释放资源时报错：

   `[ WARN:1] global C:\projects\opencv-python\opencv\modules\videoio\src\cap_msmf.cpp (674) SourceReaderCB::~SourceReaderCB terminating async callback`

   修改代码如下：`cap = cv2.VideoCapture(0, cv2.CAP_DSHOW)`

9. pycharm更新了一下，然后导入包的时候会有`unresolved reference issue`的提示，但是能正常运行，就只是看起来不好看。

   解决方案：~~[有一个](https://www.jianshu.com/p/9555310f1920)说`File` → `Settings` → `Editor` → `File Types` → `Ignore files and folders`，干掉框框中的：`__init__.py;`，然后：`OK`等待重新文件扫描。但是我打开看了以后发现我的框里并没有`__init__.py`。于是就`File` → `Invalidate Caches / Restart`，选择`Invalidate and Restart`，等待重新扫描库文件。这之后就好了。~~并没有解决问题，算了反正不影响运行，就这样吧。