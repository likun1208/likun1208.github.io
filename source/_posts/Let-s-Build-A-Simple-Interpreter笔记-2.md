---
title: Let's Build A Simple Interpreter笔记[2]
date: 2020-01-31 10:54:53
tags: 
	- python
	- 编译原理
	- 学习笔记
categories: Let's Build A Simple Interpreter
---

## 课程文档

[原文](https://ruslanspivak.com/lsbasi-part2/)

[翻译](https://feng-qi.github.io/2018/01/23/lets-build-a-simple-interpreter-part-02/)

<!--more-->

## 代码

```python
# Token types
# EOF (end-of-file) token is used to indicate that
# there is no more input left for lexical analysis
INTEGER, PLUS, MINUS, EOF = 'INTEGER', 'PLUS', 'MINUS', 'EOF'
class Token():
    def __init__(self, type, value):
        # token type: 'INTEGER', 'PLUS', 'MINUS', or 'EOF'
        self.type = type
        # token value: non-negative integer value, '+', '-', or None
        self.value = value
    def __str__(self):
        """String representation of class instance
        Examples:
            Token(INTEGER, 3)
            Token(PLUS, '+')
        """
        return f'Token({self.type}, {self.value})'
    def __repr__(self):
        return self.__str__()

class Interpreter():
    def __init__(self, text):
        # client string input, e.g. "3 + 5", "12 - 5", etc
        self.text = text
        # self.pos is an index into self.text
        self.pos = 0
        # current token instance
        self.current_token = None
        self.current_char = self.text[self.pos]
    def error(self):
        raise Exception('Error parsing input')
    def advance(self):
        """Advance the 'pos' pointer and set the 'current_char' variable."""
        self.pos += 1
        if self.pos >= len(self.text):
            self.current_char = None  # Indicates end of input
        else:
            self.current_char = self.text[self.pos]
    def skip_whitespace(self):
        while self.current_char is not None and self.current_char.isspace():
            self.advance()
    def integer(self):
        """Return a (multidigit) integer consumed from the input."""
        result = ''
        while self.current_char is not None and self.current_char.isdigit():
            result += self.current_char
            self.advance()
        return int(result)
    def get_next_token(self):
        """Lexical analyzer (also known as scanner or tokenizer)
        This method is responsible for breaking a sentence
        apart into tokens.
        """
        while self.current_char is not None:
            if self.current_char.isspace():
                self.skip_whitespace()
                continue
            if self.current_char.isdigit():
                return Token(INTEGER, self.integer())
            if self.current_char == '+':
                self.advance()
                return Token(PLUS, '+')
            if self.current_char == '-':
                self.advance()
                return Token(MINUS, '-')
            self.error()
        return Token(EOF, None)
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
        """Parser / Interpreter
        expr -> INTEGER PLUS INTEGER
        expr -> INTEGER MINUS INTEGER
        """
        # set current token to the first token from the input
        self.current_token = self.get_next_token()
        # we expect the current token to be an integer
        left = self.current_token
        self.eat(INTEGER)
        # we expect the current token to be either a '+' or '-'
        op = self.current_token
        if op.type == PLUS:
            self.eat(PLUS)
        elif op.type == MINUS:
            self.eat(MINUS)
        else:
            self.error()
        # we expect the current token to be an integer
        right = self.current_token
        self.eat(INTEGER)
        # after the above call the self.current_token is set to
        # EOF token
        # at this point either the INTEGER PLUS INTEGER or
        # the INTEGER MINUS INTEGER sequence of tokens
        # has been successfully found and the method can just
        # return the result of adding or subtracting two integers,
        # thus effectively interpreting client input
        if op.type == PLUS:
            result = left.value + right.value
        elif op.type == MINUS:
            result = left.value - right.value
        else:
            self.error()
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

这个处理方式和我想的不太一样...

整体逻辑是：

1. `get_next_token`是词法分析器，输入一个字符串，逐一输出词法单元，比如输入`32 + 5`，输出的流应该是`Token(INTEGER, 32) -> Token(PLUS, +) -> Token(INTEGER, 5)`。
   这个词法分析器用到的辅助函数为`advance()`，`skip_whitespace()`和`interger()`，其中`advance()`让索引后移一位，`skip_whitespace()`跳过空格，`interger()`把连续的数字变成一个完整的数字。注意这里如果是多位数中间有空格，多位数会被拆成两个数字，比如`32 3`会被判断为`32`和`3`，而不是`323`。
   词法分析过程中会遇到以下几种情况：(1)空格，要跳过空格，循环继续运行，分析下一个字符；(2)结束符，直接返回`EOF`对应的`token`；(3)数字，循环终止，连后面的几位数字字符一起变成一个数字，返回对应的`token`；(4)是`+`或者`-`，索引移到下一位，循环终止，返回运算符对应的`token`。
   分析几个辅助函数：
   1. `advance()`，索引`pos`后移一位，当前字符`current.char`的值变为新索引对应的字符。
2. `skip_whitespace()`，循环调用`advance()`直到当前字符不是空格。
   3. `integer()`，先声明一个初始为空字符串的`result`，然后循环把`current.char`加到`result`这个字符串里，接着`advance()`移动到下一位，直到当前字符不是数字。
2. `expr()`是用来计算表达式的，判断词法分析后的词法单元是否符合规则，然后根据规则来计算结果。辅助函数为`eat()`，用来判断当前词法单元是否符合规则。

以输入`32 + 5`为例分析整个流程：

1. 首先运行的是`main()`，获取输入字符串，存到`text`中（`line116`)，并用`text`初始化`interpreter`这个解释器类(`line121`)，此时，`interpreter.text`就是我们输入的字符串，`interpreter.pos`是初始值`0`，`self.current_token`是初始值`None`，`self.current_char`是`interpreter.text`这个字符串数组下标为`pos=0`对应的字符，也就是`3`。

2. 接下来`line122`调用了表达式计算器`expr()`，而`expr()`的第一行调用了词法分析器`get_next_token()`，因为字符3是数字，所以要去调用`integer()`函数，并返回一个类型为`INTEGER`、值为函数结果的`TOKEN`。

   在执行完`integer()`并返回之前，`pos=0`，`current_token=None`，`current_char=3`。

3. 进入`integer()`函数，最初`result`是空字符串，`current_char=3`符合循环条件，进入`while`循环，`result`变成`3`（是字符串格式）,调用`advance()`，接下来先让索引后移，即`pos=1`，此时还没移到最后一位，更新`current_char=text[pos]=text[1]=2`。此时还符合循环条件，`result`把新的`current_char`加上，变成`32`（字符串格式），再次`advance()`，调用完后，`pos=2`，依旧没移到最后一位，`current_char=text[2]=空格`。这时不满足循环条件了，循环结束，返回字符串`result`对应的数字也就是32。

4. 然后回到`get_next_token()`，它已经有返回值了，返回给之前的`expr()`中的`self.current_token`，这是我们得到的第一个词法单元，是我们要计算的表达式的左值，将它存到`left`中，之后可以使用`left.value`来访问它的值，这之后就调用`eat()`看该值是不是一个`INTEGER`，如果是，就再次调用`get_next_token()`继续分析下一个单元，反之则报错。很显然此时`current_token`是`(INTEGER,32)`，符合要求，进入`get_next_token()`。

5. `get_next_token()`判断此时`current_char`是空格，因此执行`skip_whitespace()`。

6. 在`skip_whitespace()`中，`current_char`满足循环条件，进入循环体，调用`advance()`，`pos=3`，`current_char=text[3]=+`，循环结束，回到`get_next_token()`，执行`continue`，也就是继续进行词法判断。

7. 由于`current_char`是`+`，在对应的`if`分支中，执行`advance()`，`pos=4`,`current_char=text[4]=空格`，然后把`+`对应的`token`返回到`expr()`中。

8. `expr()`把新得到的`token`存入`op`，并通过`eat()`判断该`token`是否是一个加号或者减号。此时是加号，又开始了`get_next_token()`。

9. 这次`get_next_token()`首先进入空格分支，跳过空格后，`pos=5`，`current_char=5`，进入数字分支，执行`integer()`。`integer()`中和第三步一样，最终返回数字5，再回到`get_next_token()`，返回5对应的`token`给`expr()`，存在`right`中。

10. 此时，我们已经完成了词法分析，得到了`(INTEGER, 32) -> (PLUS, '+') -> (INTEGER, 5)`的词法结构。由于`OP`对应的类型是`PLUS`，对`left.value`和`right.value`执行加法，存入`result`并返回。

11. 再回到`line122`，`result`已经得到了结果，`line123`输出该结果，程序运行结束。

也就是说，在计算表达式结果的函数中调用词法分析器，判断词法分析的结果是否符合规则，如果符合规则，就按规则继续运行。在计算器函数中，只会出现`token`类型的变量，而词法分析函数会负责把词法单元打包成`token`交给计算器。

> 在解释一个表达式之前，你需要知道它是哪种组合，比如相加或相减。这是 `expr` 方法本质上做的事： 它从 `get_next_token` 方法得到的 token 流中找到结构，然后解释它识别出的组合，产 生算术表达式的结果。
> 从 token 流中查找结构，或者说从 token 流中识别组合，的过程叫做 **parsing**. 解释器 或编译器中执行这部分任务的叫 **parser**.
> 现在你知道解释器的 **parsing** 和 **解释** 都在 `expr` 方法中了── `expr` 方法首先尝 试从 token 流中识别（即parse） INTEGER -> PLUS -> INTEGER 或 the INTEGER -> MINUS -> INTEGER 组合，在成功识别到（即parsed）其中一个组合时，该方法就解释执行 它并返回给调用者两个整数相加或相减的结果。

## 练习

1. 扩展计算器以处理两个整数相乘

2. 扩展计算器以处理两个整数相除

   前两个都很简单，仿照加法减法的代码复制改写就行。

   ```python
   # 运算符声明
   INTEGER, PLUS, MINUS, MULTIPLY, DIVIDE, EOF = 'INTEGER', 'PLUS', 'MINUS', 'MULTIPLY', 'DIVIDE', 'EOF'
   
   # get_next_token()
   if self.current_char == '*':
   	self.advance()
   	return Token(MULTIPLY, '*')
   if self.current_char == '/':
   	self.advance()
   	return Token(DIVIDE, '/')
   
   # expr()
   # 修改读取运算符
   op = self.current_token
   if op.type == PLUS:
   	self.eat(PLUS)
   elif op.type == MINUS:
   	self.eat(MINUS)
   elif op.type == MULTIPLY:
   	self.eat(MULTIPLY)
   elif op.type == DIVIDE:
   	self.eat(DIVIDE)
   else:
   	self.error()
   
   # 修改计算结果那里
   if op.type == PLUS:
   	result = left.value + right.value
   elif op.type == MINUS:
   	result = left.value - right.value
   elif op.type == MULTIPLY:
   	result = left.value * right.value
   elif op.type == DIVIDE:
   	result = left.value / right.value
   else:
   	self.error()
   ```

3. 修改代码以使它可以解释包含任意个数字的加减操作，如“9 - 5 + 3 + 11”

   这个也不难。此时的词法结构是`INTEGER -> 循环[OP ->INTEGER]`，在`expr()`里首先读取并`eat()`第一个词法单元，`result`先等于这个单元的值，接下来用`while`进行两个词法单元为一组的循环，直到读取到`EOF`，每个循环里，`result`与新的`INTEGER`进行加减运算。

   ```python
   # 修改expr()
   result = left.value
   while self.current_char is not None:
   	op = self.current_token
   	if op.type == PLUS:
   		self.eat(PLUS)
   	elif op.type == MINUS:
   		self.eat(MINUS)
   	else:
   		self.error()
   	right = self.current_token
   	self.eat(INTEGER)
   	if op.type == PLUS:
   		result += right.value
   	elif op.type == MINUS:
   		result -= right.value
   	else:
   		self.error()
   ```

## 检查理解

1. 什么是 lexeme？

   lexeme 是组成 token 的一个字符序列。（这个词翻译过来是**词位**或**词素**）

   `token`和`lexeme`的关系类似于类和实例（或者对象）之间的关系。举例来说，变量`a`和`b`，它们属于同一种`token`：`identifier`，而`a`的`lexeme`是`a`，`b`的`lexeme`是`b`。每个关键字是一种`token`。`token`可以附带一个值属性，例如变量`a`，调用`gettoken()`时，会返回一个`identifier`类型的`token`，其值属性是`a`。

2. 在 token 流中找到结构的过程叫什么？或者这么问，在 token 流中识别出特定组合的过程叫什么？

   parsing（翻译是语法分析或句法分析）

3. 解释器（编译器）做 parsing 工作的部分叫什么？

   parser（也就是语法分析器）