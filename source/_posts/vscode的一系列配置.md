---
title: vscode的一系列配置
date: 2020-06-06 09:51:33
tags: 教程
categories: 编辑器配置
---

前段时间忽然开始用vscode，今天配置好了C和C++，现在决定整理一下配置过程。

<!--more-->

## 安装和外观

1. [VSCode官网](https://code.visualstudio.com/)下载和安装，这部分应该不会有问题，注意最后添加到path的选项要勾选，不然就要自己去配置环境变量；

2. 中文设置：打开扩展（左边那一列按钮最下面的，默认快捷键是`ctrl+shift+x`，找一个叫Chinese (Simplified) Language Pack for Visual Studio Code的扩展，安装完成以后重启vscode，界面就变成中文了。如果没变，就按`ctrl+shift+p`，在出现的框里输入configure display language，然后下拉菜单里选zh-cn就可以了。

3. 主题和图标：在扩展里找喜欢的就可以，据推荐vscode-icons比较好看，我也没研究其他图标。

4. 背景：扩展里找background这个插件（名字就是这个单词），安装以后需要设置一下

   1. `文件—>首选项—>设置—>扩展—>Plugin background config.background插件配置`

   2. 打开这个界面以后，有一行字`在settings.json中编辑`，点一下可以打开settings.json（实际上在其他扩展里也能找到这行字，总之打开settings.json就行）

   3. 如果之前什么扩展也没装，也没搞别的配置，那这个文件打开以后应该是只有两行花括号(`{}`)的，如果之前装了别的，那花括号中间可能会有几行内容，接下来在花括号里面输入以下内容：

      ```
      	"background.enabled": true,
          "background.useDefault": false,
          "background.customImages":["file:///C:/Users/LiKun/Pictures/background.png"],
          "background.style":{
              "content":"''",
              "pointer-events":"none",
              "position":"absolute",//图片位置
              "width":"99%",
              "height":"99%",
              "z-index":"99999",
              "background.repeat":"no-repeat",
              "background-size":"25%,25%",//图片大小
              "opacity":0.3 //透明度
          },
      ```

      注意：每一行最后的逗号不要忘了，背景图的路径改成自己的图，我这个设置是背景图出现在右下角的位置，如果有其他需求自己改就行。

## latex

1. 安装texlive，因为我之前安装过texstudio所以就没再装了，这里要记得环境变量的问题，正常来说安装的时候会自动添加，如果没勾选那就要自己配置；

2. vscode的扩展里找latex workshop安装，据说安装好就能编译tex文件并生成pdf，不过我还搞了乱七八糟的设置才可以；

3. 设置过程还是打开setting.json，直接输入：

   ```
   	"latex-workshop.latex.recipes": [
         {
           "name": "xelatex -> bibtex -> xelatex*2",
           "tools": [
             "xelatex",
             "bibtex",
             "xelatex",
             "xelatex"
           ]
         }
       ],
       "latex-workshop.latex.tools": [
         {
           "name": "xelatex",
           "command": "xelatex",
           "args": [
             "-synctex=1",
             "-interaction=nonstopmode",
             "-file-line-error",
             "%DOC%"
           ]
         },
         {
           "name": "latexmk",
           "command": "latexmk",
           "args": [
             "-synctex=1",
             "-interaction=nonstopmode",
             "-file-line-error",
             "%DOC%"
           ]
         },
         {
           "name": "pdflatex",
           "command": "pdflatex",
           "args": [
             "-synctex=1",
             "-interaction=nonstopmode",
             "-file-line-error",
             "%DOC%"
           ]
         },
         {
           "name": "bibtex",
           "command": "bibtex",
           "args": [
             "%DOCFILE%"
           ]
         }
       ],
       "latex-workshop.view.pdf.viewer": "tab",
       "editor.wordWrap": "on",
       "latex-workshop.synctex.afterBuild.enabled": true,
       "latex-workshop.view.pdf.internal.synctex.keybinding": "double-click",
       "[latex]": {
         "editor.defaultFormatter": "nickfode.latex-formatter"
       },
       "latex-workshop.latex.autoClean.run": "onBuilt",
   ```

   注意：这个文件最外层就一组花括号，不管有多少要设置的东西都在里面加；latex的内容里有一些是在那个设置里可以直接选的，而不是通过这种让人看不懂的代码来设置，例如`"latex-workshop.view.pdf.internal.synctex.keybinding": "double-click"`这一句的作用是在pdf里双击可以跳转到latex源文件对应的位置，本来默认的是ctrl点击，但是我习惯双击，可以在设置里搜synctex:keybinding然后修改，改了会自动在settings.json 里出现那句话；`"editor.wordWrap": "on"`是和latex本身无关的设置，是让编辑器可以自动换行；其他各种设置可以自行百度。

## python

1. 扩展里搜python，安装跳出来的第一个就行；
2. 可能和我之前已经安装过python、pycharm等等东西有关，安装插件之后就能编译运行python了。

## C和C++

1. 安装mingw，如果之前装过就不必重装了，这里要自己添加环境变量，然后检查有没有装好；
2. vscode里安装C/C++扩展，可能装好要重启；
3. 打开要写代码的那个文件夹，比如C++文件夹，里面新建一个cpp文件随便写几行；
4. `Ctrl+Shift+P`输入C/C++，选择“Edit Configurations(UI)”进入配置，这里要选编译器：`C:/Program Files/mingw-w64/x86_64-8.1.0-posix-seh-rt_v6-rev0/mingw64/bin/g++.exe`，路径改成自己的，还要选IntelliSense 模式，要用gcc-x64；
5. 上一步做好以后，可以看到C++文件夹里多出一个.vscode文件夹，里面有c_cpp_properties.json文件，文件内容是写好的，可以看看，不过不需要修改；
6. 再按`Ctrl+Shift+P`输入tasks，选择“Tasks:Configure Default Build Task”，再选择“C/C++: g++.exe build active file”，然后.vscode文件夹里就会出现tasks.json，也是写好的，不需要改；
7. 接下来点运行—>启动调试，或者直接按F5，选择C++(GDB/LLDB)，会出现一个launch.json，或者左边那列扩展按钮上面的那个按钮里有个创建launch.json也可以；
8. 上一步的launch.json里可以自己根据需求添加一些配置，或者去网上复制别人的配置，总之我这里是什么也没改就能直接运行；
9. 然后再按F5，就可以运行文件了；
10. 在第4步之后，就可以在vscode里找到终端然后gcc编译运行文件；
11. 默认运行输出信息是在下面的终端，在launch.json里找到"externalConsole"然后把它的false改成true,就可以出现独立的命令行界面来显示信息了。

## KanBan

1. 是最近发现的很好用的一个项目管理方式；

2. 在扩展里搜KanBan，出来的第一个就是它，然后安装；

3. 在扩展说明那里能看到很多可以配置的，我的配置如下：

   ```html
   "kanban": 
       {
           <!--配置四个列的名称，因为原本的名称不符合我的需求，所以这么改了-->
           "columns": {
             "done": "Others",
             "inProgress": "In Progress",
             "testing": "Done",
             "todo": "TODOs"
           },
           "openOnStartup": true,   <!--每次打开vscode的时候，自动打开KanBan标签页-->
           "trackTime": true    <!--打开计时功能，如果在干活前后各点一下，就会记录干了多久-->
       },
   ```

4. 在搞好以后发现，KanBan文件是存放在项目文件夹的`.vscode`中，名称是`vscode-kanban.json`，打开可以看到具体内容。打开不同文件夹时，KanBan文件都是新的，如果想保持同步，就得把最新的文件复制到其他文件夹里，这有点麻烦，所以对着说明文档分析了一下，猜测可以改扩展的源代码里的文件。

5. 具体来说，是找到用户目录里的vscode文件夹中的KanBan文件夹，文件夹名字可能和版本号等有关，比如我的是`C:\Users\LiKun\.vscode\extensions\mkloubert.vscode-kanban-1.28.0\`，里面有一个`out`文件夹，打开会发现一些`js`文件，这些文件都可以按需求改，我需要改的是`workspace.js`。

6. 打开`workspace.js`，在一开始定义常量的地方有一个`const BOARD_FILENAME`，把它的值改成我打算固定存KanBan的绝对路径，例如：`const BOARD_FILENAME = 'C:/code/.vscode/vscode-kanban.json';`

7. 改了之后如果直接保存并在vscode里运行，会提示路径不对，看报错信息是因为程序把上面那个路径和项目文件夹的路径直接拼起来了，显然有问题。回到`workspace.js`，往下没几行就能看到一个`get boardFile()`函数，函数内容就是把路径拼起来并作为新路径返回，那么在这里，把拼路径的去掉，直接返回`BOARD_FILENAME`就可以了，修改之后这个函数如下：

   ```js
   get boardFile() {
           return vscode.Uri.file(Path.resolve(BOARD_FILENAME));
       }
   ```

   注意这个文件夹里其他东西不能乱改。

8. 到此为止就可以实现所有项目文件共享同一个KanBan了，不过每次更新了以后需要重新改js文件。

9. 用了一段时间发现因为css文件里已经写好了每个模块的功能和交互，所以像我那样改每一块的名字用起来效果不太好，想了想还是改回去了，以后研究一下css文件再改。

