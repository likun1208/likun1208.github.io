---
title: Let's Build A Simple Interpreter笔记[1]
date: 2020-01-30 17:50:53
tags:
  - python
  - 编译原理
  - 学习笔记
categories: Let's Build A Simple Interpreter
---

## 课程文档

[原文](https://ruslanspivak.com/lsbasi-part1/)

[翻译](https://feng-qi.github.io/2018/01/23/lets-build-a-simple-interpreter-part-01/)

上一个翻译页面凉了，找了一个[新的翻译](https://www.oschina.net/translate/build-a-simple-interpreter-part-1)

<!--more-->

## 代码

```python
# Token types
# EOF (end-of-file) token is used to indicate that
# there is no more input left for lexical analysis
INTEGER, PLUS, EOF = 'INTEGER', 'PLUS', 'EOF'
class Token(object):
    def __init__(self, type, value):
        # token type: INTEGER, PLUS, or EOF
        self.type = type
        # token value: 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, '+', or None
        self.value = value
    def __str__(self):
        """String representation of the class instance.
        Examples:
        Token(INTEGER, 3)
        Token(PLUS, '+')
        """
        return 'Token({type}, {value})'.format(
            type=self.type,
            value=repr(self.value)
        )    
    def __repr__(self):
        return self.__str__()
class Interpreter(object):
    def __init__(self, text):
        # client string input, e.g. "3+5"
        self.text = text
        # self.pos is an index into self.text
        self.pos = 0
        # current token instance
        self.current_token = None
    def error(self):
        raise Exception('Error parsing input')    
    def get_next_token(self):
        """Lexical analyzer (also known as scanner or tokenizer)
        This method is responsible for breaking a sentence
        apart into tokens. One token at a time.
        """
        text = self.text    
        # is self.pos index past the end of the self.text ?
        # if so, then return EOF token because there no more
        # input left to convert into tokens
        if self.pos > len(text) - 1:
            return Token(EOF, None)    
        # get a character at the position self.pos and decide
        # what token to create based on the single character
        current_char = text[self.pos]    
        # if the character is a digit then convert it to
        # integer, create an INTEGER token, increment self.pos
        # index to point to the next character after the digit,
        # and return the INTEGER token
        if current_char.isdigit():
            token = Token(INTEGER, int(current_char))
            self.pos += 1
            return token    
        if current_char == '+':
            token = Token(PLUS, current_char)
            self.pos += 1
            return token    
        self.error()    
    def eat(self, token_type):
        # compare the current token type with the passed token
        # type and if they match then "eat" the current token
        # and assign the next token to the self.current_token,
        # otherwise raise an exception.
        if self.current_token.type == token_type:
            self.current_token = self.get_next_token()
        else:
            self.error()    
    def expr(self):
        """expr -> INTEGER PLUS INTEGER"""
        # set current token to the first token taken from the input
        self.current_token = self.get_next_token()    
        # we expect the current token to be a single-digit integer
        left = self.current_token
        self.eat(INTEGER)    
        # we expect the current token to be a '+' token
        op = self.current_token
        self.eat(PLUS)    
        # we expect the current token to be a single-digit integer
        right = self.current_token
        self.eat(INTEGER)
        # after the above call the self.current_token is set to
        # EOF token    
        # at this point INTEGER PLUS INTEGER sequence of tokens
        # has been successfully found and the method can just
        # return the result of adding two integers, thus
        # effectively interpreting client input
        result = left.value + right.value
        return result
def main():
    while True:
        try:
            # To run under Python3 replace 'raw_input' call with 'input'
            text = input('calc> ')
        except EOFError:
            break
        if not text:
            continue
        interpreter = Interpreter(text)
        result = interpreter.expr()
        print(result)
if __name__ == '__main__':
    main()
```




## 笔记

1. 词法分析(lexical analysis)：把输入字符串拆分成 token 的过程。解释器中做这个工作的部分被称为 **词法分析器**(lexical analyzer)，简称 **lexer** ，也被称作  **scanner** 或 **tokenizer** 。
2. 在完成词法分析后，解释器需要验证token序列是否符合期望的序列，如果符合，则进行一些运算，不符合则抛出异常。

#### 代码分析

1. 目的是执行一位数的加法运算，整体逻辑是，先取第一个字符，判断是否是数字，如果是，就把这个数字存在`left`中，然后取第二个字符，判读是否为加号，如果是，再继续取第三个字符，判断是否为数字并把数字存在`right`中，接下来就可以计算`left.value + right.value`了。整个过程中字符的移动依靠一个名为`pos`的变量，它作为索引，每当取完一个字符，`pos`的值就会`+1`，这样直到最后一个字符。
2. `Token`类构造了一个结构为`(类型, 值)`的变量，当我们访问某个`Token`类型的变量`v`时，可以通过`v.type`来获取它的类型，通过`v.value`获取它的值。
3. `Interpreter`类是我们的解释器类型，其中`get_next_token()`函数可以从字符串中取一个字符，判断该字符类型是数字还是`+`，并返回正确的`Token`结构，比如是数字，就返回`Token(INTEGER, int(current_char))`，然后索引(`pos`)往后挪一位，如果既不是数字也不是加号，比如是空格，就会报错，如果已经到最后一位了，会返回`EOF`。
4. 接下来的`eat()`函数，参数是判断现在取出来的字符`type`是否符合设想的`type`，如果符合，就调用`get_next_token()`取下一个字符；反之，就输出错误信息。
5. 然后`expr()`函数用来按顺序从头到尾逐个读取字符，在`eat()`的帮助下判断整体结构是否符合词法要求（比如这里的要求是`INTEGER -> + INTEGER`）并执行运算。
6. 最后的`main()`，将输入的字符串存到`text`中，并用`text`初始化类型为`Interpreter`的变量`interpreter`，接下来调用`interpreter.expr()`进行计算，结果存放到`result`中并输出。

#### python相关

1. python命名规则：以下划线开头的标识符是有特殊意义的。以单下划线开头例如`_foo` 代表不能直接访问的类属性，需通过类提供的接口进行访问，不能用 `from xxx import` 而导入。
    以双下划线开头的 `__foo` 代表类的私有成员，以双下划线开头和结尾的 `__foo__` 代表 Python 里特殊方法专用的标识，如 `__init__()` 代表类的构造函数。

2. python中的`main()`函数：python程序按顺序执行，而不是像之前学过的语言那样先找`main()`然后从`main()`开始，执行过程中遇到`def`代码块会先跳过，每个代码块需要保持相同的缩进。

    例如：

    ```python
    # code1
    print("test")
    def foo():
        str = "function"
        print(str)
    if __name__ == '__main__':
        print("main")
        foo()
    # output
    test
    main
    function
    
    # code2
    if __name__ == '__main__':  
        print("Hello python !!!")    
        def func_print():  
            print("in func_print")   
        def main():  
            print("In main")    
        main()  
    # output
    Hello python !!!
    In main
    
    # code3
    if __name__ == '__main__':
        print("Hello python !!!")
    
        def func_print():
            print("in func_print")
    
        def main():
            print("In main")
    
            func_print()
    main()
    # output
    Hello python !!!
    In main
    in func_print
    
    #code4
    if __name__ == '__main__':
        print("Hello python !!!")
    
        def func_print():
            print("in func_print")
    
        def main():
            print("In main")
    
    	func_print()
    main()
    # output 
    Hello python !!!
    in func_print
    In main
    ```

3. python中的`__name__`：`__name__`是python的一个内置类属性，它天生就存在于一个 python 程序中，代表对应程序名称。

   ```
   def test()
   	print(__name__)
   test
   ```

   直接运行temp2.py 时：`__name__="__main__"`，在其它程序中导入temp2.py，则：`__name__="temp2.py"`，所以`__name__`的主要作用就是用来区分，模块是直接被运行还是被导入。

   实际使用时，我们会把测试模块时要执行的语句放到`if __name__ == '__main__':`这个代码块里，这样测试的时候能看到测试结果，而调用的时候，测试语句不会被执行，也就不用删除测试语句。

## 练习

1. 修改代码使得允许输入多位整数，例如`12+3`

   多位数不一定是几位数，也就是说我们在得到输入之前无法判断整个字符串第几位是什么类型，但是整体结构一定是`连在一起的数字 + 连在一起的数字`，因此考虑用`while`循环读取数字，在当前字符不是数字的情况下跳出循环去读取`+`，然后继续循环读数字，直到读取出`EOF`，字符串结束。具体来说是声明`left`和`right`两个数字并初始化为0，然后`left`存第一位数字，接下来乘10+第二位数字，再乘10+第三位数字，依次循环；`right`也是这样。具体代码如下：

   ```python
   # 修改expr()
   left = right = 0
   while self.current_token.type == 'INTEGER':    
   	left = left * 10 + self.current_token.value    
   	self.eat(INTEGER)
   # 中间几行代码省略
   while self.current_token.type != 'EOF':
   	right = right * 10 + self.current_token.value
   	self.eat(INTEGER)
   ```

2. 增加一个跳过空白符的方法，使你的计算器可以处理包含空白符的输入如`12 + 3`

   我们要增加一个`Token`类型用来识别空格，然后在`expr()`中读取到空格时就调用`eat()`来跳过，我们假设空格不会出现在一个多位数的中间（比如`1 2 3`就会报错)。

   具体代码如下：

   ```python
   # 在代码开始的地方加
   SPACE = 'SPACE'
   
   # 在get_next_token()里加
   if current_char == ' ':
   	token = Token(SPACE, current_char)
   	self.pos += 1
   	return token
   
   # 在expr()的数字和操作符的前后加
   while self.current_token.value == ' ':
   	self.eat(SPACE)
   ```

   **这里存在一些问题需要改进**：

   1. 应该可以想办法跳过多位数中间的空格，也就是直接忽略所有空格；
   2. 个人认为不应该在`expr()`这个函数中出现对字符类型的判断，应该是在`expr()`中逐位后移，这个过程中调用其他函数来处理忽略空格等情况，也就是说上一个的`while`循环也不太合适，但是我暂时没想到怎么改，就先这样吧，反正功能上没问题。之后学到正则相关的地方可能会有新的思路。

3. 修改代码使得它可以处理`-`而非`+`的情况

   这个类似于空格，也是要添加一下，我的做法是直接把`+`和`-`统一成类型为`OP`值为对应字符的`TOKEN`，然后在`expr()`中最后运算的时候，判断该变量的值是什么，再执行对应的运算。具体代码如下：

   ```python
   # 文件开头的PLUS = 'PLUS'修改为
   OP = 'OP'
   
   # 修改get_next_token()
   if current_char == '+' or current_char == '-':
   	token = Token(OP, current_char)
   	self.pos += 1
   	return token
   
   # 修改expr()
   if op.value == '+':
   	result = left + right
   if op.value == '-':
   	result = left - right
   ```

   
   
   **下一课的内容说明了我的想法全是错的...暴风哭泣**
   
   ## 检查理解
   
   1. 什么是解释器？
   
      解释器把高级语言逐行转译执行。
   
   2. 什么是编译器？
   
      编译器把高级语言翻译成低级语言，再一起执行。
   
   3. 解释器和编译器的区别是什么？
   
      一个是一边翻译一边执行，一个是翻译完再执行。
   
   4. 什么是 token？
   
      有类型的值的对象
   
   5. 将输入拆分成 token 的过程叫什么？
   
      词法分析
   
   6. 解释器中做词法分析的部分叫什么？
   
      词法分析器(lexical analyzer)
   
   7. 解释器或编译器的这个部分还有什么其他常见的名字？
   
      scanner 或 tokenizer