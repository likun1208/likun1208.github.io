---
title: Let-s-Build-A-Simple-Interpreter笔记-5
date: 2020-03-09 08:59:02
tags:
  - python
  - 编译原理
  - 学习笔记
categories: Let's Build A Simple Interpreter
---

## 课程文档

[原文](https://ruslanspivak.com/lsbasi-part5/)

[翻译](https://feng-qi.github.io/2018/01/30/lets-build-a-simple-interpreter-part-05/)

[新的翻译](https://www.oschina.net/translate/lsbasi-part5)

<!--more-->

## 代码

```python
# Token types
#
# EOF (end-of-file) token is used to indicate that
# there is no more input left for lexical analysis
INTEGER, PLUS, MINUS, MUL, DIV, EOF = (
    'INTEGER', 'PLUS', 'MINUS', 'MUL', 'DIV', 'EOF'
)
class Token:
    def __init__(self, type, value):
        # token type: INTEGER, PLUS, MINUS, MUL, DIV, or EOF
        self.type = type
        # token value: non-negative integer value, '+', '-', '*', '/', or None
        self.value = value
    def __str__(self):
        """String representation of the class instance.
        Examples:
            Token(INTEGER, 3)
            Token(PLUS, '+')
            Token(MUL, '*')
        """
        return f'Token({self.type}, {repr(self.value)})'
    def __repr__(self):
        return self.__str__()
class Lexer:
    def __init__(self, text):
        # client string input, e.g. "3 * 5", "12 / 3 * 4", etc
        self.text = text
        # self.pos is an index into self.text
        self.pos = 0
        self.current_char = self.text[self.pos]
    def error(self):
        raise Exception('Lexer error')
    def advance(self):
        """Advance the `pos` pointer and set the `current_char` variable."""
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
        value = ''
        while self.current_char is not None and self.current_char.isdigit():
            value += self.current_char
            self.advance()
        return int(value)
    def get_next_token(self):
        """Lexical analyzer (also known as scanner or tokenizer)

        This method is responsible for breaking a sentence
        apart into tokens. One token at a time.
        """
        while self.current_char is not None:
            if self.current_char.isspace():
                self.skip_whitespace()
                continue
            elif self.current_char.isdigit():
                return Token(INTEGER, self.integer())
            elif self.current_char == '*':
                self.advance()
                return Token(MUL, '*')
            elif self.current_char == '/':
                self.advance()
                return Token(DIV, '/')
            elif self.current_char == '+':
                self.advance()
                return Token(PLUS, '+')
            elif self.current_char == '-':
                self.advance()
                return Token(MINUS, '-')
            else:
                self.error()
        return Token(EOF, None)
class Interpreter:
    def __init__(self, lexer):
        self.lexer = lexer
        # set current token to the first token taken from the input
        self.current_token = self.lexer.get_next_token()
    def error(self):
        raise Exception('Interpreter error')
    def eat(self, token_type):
        # compare the current token type with the passed token
        # type and if they match then "eat" the current token
        # and assign the next token to the self.current_token,
        # otherwise raise an exception.
        if self.current_token.type == token_type:
            self.current_token = self.lexer.get_next_token()
        else:
            self.error()
    def factor(self):
        """factor : INTEGER"""
        value = self.current_token.value
        self.eat(INTEGER)
        return value
    def term(self):
        """term : factor ((MUL | DIV) factor)*"""
        value = self.factor()
        while self.current_token.type in (MUL, DIV):
            if self.current_token.type == MUL:
                self.eat(MUL)
                value *= self.factor()
            elif self.current_token.type == DIV:
                self.eat(DIV)
                value /= self.factor()
        return value
    def expr(self):
        """Arithmetic expression parser / interpreter.

        calc>  14 + 2 * 3 - 6 / 2
        17

        expr   : term ((PLUS | MINUS) term)*
        term   : factor ((MUL | DIV) factor)*
        factor : INTEGER
        """
        value = self.term()
        while self.current_token.type in (PLUS, MINUS):
            if self.current_token.type == PLUS:
                self.eat(PLUS)
                value += self.term()
            elif self.current_token.type == MINUS:
                self.eat(MINUS)
                value -= self.term()
        return value
    def parse(self):
        return self.expr()
def main():
    while True:
        try:
            text = input('calc> ')
        except EOFError:
            print()
            break

        if len(text.strip()):
            interpreter = Interpreter(Lexer(text))
            result = interpreter.parse()
            print(result)
        else:
            continue
if __name__ == '__main__':
    main()
```

## 笔记

这一课讲的是运算结合性和优先级的处理。

#### 什么是结合性

当一个操作数（比如 7+3+1 中的 3）两边都有加号时，我们需要一个规则来决定哪个运算符作用于 3，根据经验，是左边的加号起作用（也就是先计算7+3），因此我们把加号看做是左结合的。类似地，减、乘、除也都是左结合的。

#### 什么是优先级

当一个操作数（比如 7 + 5 * 2 中的5）两边有不同的运算符时，先进行运算的那个运算符优先级较高，比如我们说运算符\*比+先取运算符，\*就有较高的优先级。

在一个表达式中有相同优先级的运算符出现时，我们就使用结合性惯例从左到右执行。

#### 构建语法和写解释器

以算术表达式为例，优先级列表如下：

| 优先级 | 结合性 | 运算符 |
| ------ | ------ | ------ |
| 2      | 左结合 | +, -   |
| 1      | 左结合 | *, /   |

从优先级表构建语法：

1. 对于每一个优先级定义一个非终结符。这个非终结符的生成式的 body 应该由本优先级的算术运算符和更高优先级的非终结符组成。
2. 对表达式的基本单元(在我们的例子中是 INTEGER)新建一个额外的非终结符 `factor`. 一般原则就是如果你有 N 级优先级，你一共会需要 N+1 非终结符：每个优先级一个再 加上基本单元的一个。

具体来说我们现在有2个优先级，因此会有3个非终结符，优先级为2的对应`expr`，优先级为1的对应`term`，最后是`factor`。

`expr`要表达包含`term`的加减运算，即：`expr: term((PLUS|MINUS)term)*`

`term`则是包含`factor`的乘除运算，即：`term； factor((MUL|DIV)factor)*`

最后`factor`是基本单元，即：`factor: INTEGER`

然后就是和上一课一样的步骤写代码了。

（总的来说这一课没有什么新知识，但是我还是不能不看教程自己写...）

## 练习

1. 根据本文的描述凭记忆写出一个解释器，不要参考文中的代码。为你的解释器写一些测试，确保它们可以通过。

   我太菜了所以并没法凭记忆写一个解释器，于是我开始照着代码抄一下，并很快发现就算是照抄我也会出错...

   主要错误如下：

   1. `else`后面忘了加`:`；
   2. 某个地方的赋值(`=`)一个不注意就写成了相等(`==`)；
   3. `while self.current_char is not None and self.current_char.isdigit():`以及前面判断空格的那个类似的语句，`is not None`一定要放前面，如果反过来，在当前字符是空的时候，执行`isdigit()`和`isspace()`就会报错了，所以要先判定不为空。

   我好菜啊.jpg

2. 扩展这个解释器来处理包含括号的算术表达式，使得你的解释器可以对嵌套很深很深的算术表达式进行求值如：7 + 3 * (10 / (12 / (3 + 1) - 1))

   这个我的想法是要设定一下括号的结合性，左括号是右结合而右括号是左结合，但是不会写。

   看了下一课发现是要写递归语法分析，所以放下一课来写吧。

## 检查理解

1. 一个操作符是左结合的是什么意思？

   是指当数字左右两边都有该运算符时，数字取左边的运算符。

2. 操作符加减是左结合的还是右结合的？乘除呢？

   都是左结合。

3. 操作符加是否比操作符乘有更高的优先级？

   否，乘的优先级更高。

