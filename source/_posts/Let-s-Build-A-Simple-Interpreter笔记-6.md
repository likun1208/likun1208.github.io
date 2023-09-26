---
title: Let-s-Build-A-Simple-Interpreter笔记-6
date: 2020-03-12 16:26:15
tags:
  - python
  - 编译原理
  - 学习笔记
categories: Let's Build A Simple Interpreter
---

## 课程文档

[原文](https://ruslanspivak.com/lsbasi-part6/)

[翻译](https://feng-qi.github.io/2018/02/01/lets-build-a-simple-interpreter-part-06/)

[新的翻译](https://www.oschina.net/translate/lsbasi-part6)

<!--more-->

## 代码

```python
# Token types
#
# EOF (end-of-file) token is used to indicate that
# there is no more input left for lexical analysis
INTEGER, PLUS, MINUS, MUL, DIV, LPAREN, RPAREN, EOF = (
    'INTEGER', 'PLUS', 'MINUS', 'MUL', 'DIV', 'LPAREN', 'RPAREN', 'EOF'
)
class Token:
    def __init__(self, type, value):
        self.type = type
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
        # client string input, e.g. "4 + 2 * 3 - 6 / 2"
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
            elif self.current_char == '+':
                self.advance()
                return Token(PLUS, '+')
            elif self.current_char == '-':
                self.advance()
                return Token(MINUS, '-')
            elif self.current_char == '*':
                self.advance()
                return Token(MUL, '*')
            elif self.current_char == '/':
                self.advance()
                return Token(DIV, '/')
            elif self.current_char == '(':
                self.advance()
                return Token(LPAREN, '(')
            elif self.current_char == ')':
                self.advance()
                return Token(RPAREN, ')')
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
        """factor : INTEGER | LPAREN expr RPAREN"""
        token = self.current_token
        if token.type == INTEGER:
            self.eat(INTEGER)
            return token.value
        elif self.current_token.type == LPAREN:
            self.eat(LPAREN)
            value = self.expr()
            self.eat(RPAREN)
            return value
    def term(self):
        """term : factor ((MUL | DIV) factor)*"""
        value = self.factor()

        while self.current_token.type in (MUL, DIV):
            token_type = self.current_token.type
            if token_type == MUL:
                self.eat(MUL)
                value *= self.factor()
            elif token_type == DIV:
                self.eat(DIV)
                value /= self.factor()
        return value
    def expr(self):
        """Arithmetic expression parser / interpreter.

        calc> 7 + 3 * (10 / (12 / (3 + 1) - 1))
        22

        expr   : term ((PLUS | MINUS) term)*
        term   : factor ((MUL | DIV) factor)*
        factor : INTEGER | LPAREN expr RPAREN
        """
        value = self.term()
        while self.current_token.type in (PLUS, MINUS):
            token_type = self.current_token.type
            if token_type == PLUS:
                self.eat(PLUS)
                value += self.term()
            elif token_type == MINUS:
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
            print(interpreter.parse())
        else:
            continue
if __name__ == '__main__':
    main()
```

## 笔记

这一课在加减乘除的基础上加入了括号，而括号里的内容又可以看做是一个新的加减乘除表达式，即如下语法：

```
expr   : term   ((PLUS | MINUS) term)*
term   : factor ((MUL | DIV) factor)*
factor : INTEGER | LPAREN expr RPAREN
```

可以看出，`expr`和`term`没有任何变化，而`factor`则多了一种可能：`(expr)`，因此需要作出以下修改：

1. `Lexer`部分添加左右括号的返回；
2. `Interpreter`现在分成了两个分支，字符类型是`INTEGER`时，和之前的一样，返回该字符的`value`；字符类型是左括号时，则返回该括号里的`expr`的值，并把右括号也读过去。

这样的递归语法，称之为递归下降解释器。

## 练习

1. 如本文的描述，写一个你自己版本的算术表达式解释器。记住：重复是学习之母。

   试着回顾一下整个代码的思路：

   1. 代码除了`main()`以外，有3个`class`，分别是`Token`、`Lexer`、`Interpreter`。

   2. `Token`是最基础的词法单元，有两个成员变量：`type`和`value`，此外有初始化和输出的函数。

   3. `Lexer`是词法分析器，成员变量为：`text`（完整的语句）、`pos`（当前读取到语句位置的索引）、`current_char`（当前读取出的字符），成员函数为：`error`（报词法错误）、`advance`（往后读取一个新字符）、`skip_whitespace`（跳过空格）、`integer`（把连续的数字字符转成整数返回）、`get_next_token()`（返回当前词法单元）。

   4. `Interpreter`是语法解释器，成员变量为：`lexer`（词法分析器）、`current_token`（当前处理的词法单元），成员函数为：`error`（报语法错误）、`eat()`（往后处理下一个词法单元）、`factor`、`term`、`expr`三个都是语法图里的内容，最后加了一个`parse`返回`expr()`。

   大致就是这样了。


## 检查理解

这一课没有这一部分。

