---
title: Let-s-Build-A-Simple-Interpreter翻译-1
date: 2020-08-14 17:24:05
tags: 
	- python
	- 编译原理
	- 翻译
categories: Let's Build A Simple Interpreter
---

因为之前参考的翻译网站突然没了，所以开始自己从头翻译一遍。希望原文链接不要某一天也突然消失。

说是自己翻译，但主要还是用有道、必应等等翻译，也复制了[oschina](https://www.oschina.net/translate/build-a-simple-interpreter-part-1)的翻译。

[原文](https://ruslanspivak.com/lsbasi-part1/)

<!--more-->

> 如果您不知道编译器是如何工作的，那么您就不知道计算机是如何工作的。如果您不能百分之百地确定您是否知道编译器是如何工作的，那么您不知道编译器是如何工作的。
>
> ——Steve Yegge

​	好了，思考一下这个问题。无论你是菜鸟，还是经验丰富的软件开发人员，如果你不了解编译器和解释器的工作原理，那么你就不了解计算机的工作原理。就是这么简单。

​	因此，你知道编译器和解释器的工作原理吗？我的意思是，你必须确保100%掌握其工作原理，如果你无法确保这点。

![img](https://i.loli.net/2020/08/14/ds7IfUOhEwYD1cH.png)

或如果你不了解其工作原理，并为之焦虑不安。

![img](https://i.loli.net/2020/08/14/9gcOBqe3mbMHwrp.png)

其实完全不必担心。如果你能坚持下来，完成这一系列任务，并跟着我编写一个解释器和编译器，最后你会掌握它们的工作原理。同时你也会为此感到快乐且信心百倍。至少我希望实现这种效果。

![img](https://i.loli.net/2020/08/14/StfNxPn1DsZ23gB.png)

​	为何需要学习解释器和编译器？此处我给出三个理由：

1. 如果想要编写一个解释器或编译器，需要掌握并结合运用大量的专业技能。通过编写解释器或编译器，有助于提升自己这方面的专业技能，并成为一名更优秀的软件开发者。你所学到的技能也可用于编写任何软件，而不仅限于解释器或编译器。	
2. 迫切渴望了解计算机的工作原理。解释器和编译器常常看起来就像一种魔术，而你不应该满足于简单地运用这种魔术。你想揭开解释器和编译器编写过程的神秘面纱，理解它们的工作原理，并实现对解释器和编译器的控制。	
3. 渴望创造自己的程序设计语言或领域特定语言。如果你想创造一门语言，也需要为这种语言创建一个解释器或编译器。最近，新程序设计语言的研究热潮再次到来。我们几乎每天都能看到突然冒出一门新的程序设计语言：Elixir、Go、Rust，此处就只列出这些例子了。	

​	理由就是这些了。但是，解释器和编译器到底是什么呢？

​	一个解释器或编译器的目标是将某些高级语言的源程序翻译成其他的形式。解释得相当含糊，不是吗？再忍我一下，之后的一系列章节你将能够确实地了解源程序被翻译成什么。

​	在这个时间点上，你可能会好奇到底解释器和编译器之间有什么差别呢。为了达成这个系列的目的，让我们同意以下解释：如果翻译器把源程序翻译成了机器语言，它就是一个编译器。如果源程序没先被翻译成机器语言，翻译器就处理并且执行了源程序，它就是一个解释器。形象地看起来就像这样：

![img](https://i.loli.net/2020/08/14/2uIEJAlgjbUWQRm.png)

------

直说了吧，你我打算为 Pascal 语言大子集实现一个简单的解释器。在该系列最后部分，你会得到一个可工作的 Pascal 解释器，和一个类似 python 中 pdb 的源码级别的调试器。

​	你或许会问，为什么选  pascal？首先，它不是一个虚假的语言，所以我选中它在这个系列中使用：这是一个真实的编程语言，具备许多重要的语法结构。它虽然有点古老了，但仍被使用。CS（注，计算机科学）书籍使用 pascal 编程语言实现书中示例（我知道，选择为这个语言实现解释器的理由并不充分，但我想，这也是一个学习非主流语言的好机会：））

​	下面例子使用 pascal 写的阶乘函数实现。可以用你自己构建的解释器解析，还可以使用后面将实现的调试器做代码级别的交互式调试。

```pascal
program factorial;function factorial(n: integer): longint;begin
    if n = 0 then
        factorial := 1
    else
        factorial := n * factorial(n - 1);end;var
    n: integer;begin
    for n := 0 to 16 do
        writeln(n, '! = ', factorial(n));end.
```

我们使用 python 作为 Pascal 解释器的实现语言，当然，你可以使用任何你熟悉的语言（来实现），因为实现原理并不依赖具体的语言特性。Okay, 该做正事了。 各就各位，预备，开始!

------

开始制作解释器和编译器的第一步尝试是写一个简单的算术表达式解释器，也叫计算器。今天的目标很简单：能处理计算两个个位整数相加，如 **3+5。**下面是是计算器，啊不，解释器的源代码：

```python
# Token types## EOF (end-of-file) token is used to indicate that# there is no more input left for lexical analysisINTEGER, PLUS, EOF = 'INTEGER', 'PLUS', 'EOF'class Token(object):
    def __init__(self, type, value):
        # token type: INTEGER, PLUS, or EOF
        self.type = type
        # token value: 0, 1, 2. 3, 4, 5, 6, 7, 8, 9, '+', or None
        self.value = value

    def __str__(self):
        """String representation of the class instance.        Examples:            Token(INTEGER, 3)            Token(PLUS '+')        """
        return 'Token({type}, {value})'.format(
            type=self.type,
            value=repr(self.value)
        )

    def __repr__(self):
        return self.__str__()class Interpreter(object):
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
        """Lexical analyzer (also known as scanner or tokenizer)        This method is responsible for breaking a sentence        apart into tokens. One token at a time.        """
        text = self.text

        # is self.pos index past the end of the self.text ?
        # if so, then return EOF token because there is no more
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
        return resultdef main():
    while True:
        try:
            # To run under Python3 replace 'raw_input' call
            # with 'input'
            text = raw_input('calc> ')
        except EOFError:
            break
        if not text:
            continue
        interpreter = Interpreter(text)
        result = interpreter.expr()
        print(result)if __name__ == '__main__':
    main()
```

​	将上面代码保存为 *calc1.py* 文件，或者直接从[ GitHub ](https://github.com/rspivak/lsbasi/blob/master/part1/calc1.py)中下载。 在你打算深入研究代码之前，先在命令行中运行它，观察它的动作。自己动手试试！下面是我笔记本电脑上的会话（注，交互式执行）示例 (如果你在 python3 上运行计算器， 你应该是用 *input*，而不是 *raw_input*)：

```
$ python calc1.py
calc> 3+4
7
calc> 3+5
8
calc> 3+9
12
calc>
```

​	为确保你的简单计算器能正确执行，不抛出异常，你的输入需满足以下指定的规则:

- 输入中只允许出现个位数整数 	
- 当前算术运算符解释器只支持加法运算 	
- 输入中的任何位置不能出现空白（注，空格，TAB等） 	

​	这些限制对保持计算器简单很有必要。别担心，很快，你的实现就会越来越复杂的。