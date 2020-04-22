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