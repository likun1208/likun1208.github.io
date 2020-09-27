---
title: Let's Build A Simple Interpreter笔记[4]
date: 2020-02-28 12:18:50
tags: 
	- python
	- 编译原理
	- 学习笔记
categories: Let's Build A Simple Interpreter
---

## 课程文档

[原文](https://ruslanspivak.com/lsbasi-part4/)

[翻译](https://feng-qi.github.io/2018/01/28/lets-build-a-simple-interpreter-part-04/)

(这个翻译里有个别地方翻译错了......)

[新的翻译](https://www.oschina.net/translate/lsbasi-part4)

<!--more-->

## 代码

```python
# Token types
#
# EOF (end-of-file) token is used to indicate that
# there is no more input left for lexical analysis
INTEGER, MUL, DIV, EOF = 'INTEGER', 'MUL', 'DIV', 'EOF'
class Token(object):
    def __init__(self, type, value):
        # token type: INTEGER, MUL, DIV, or EOF
        self.type = type
        # token value: non-negative integer value, '*', '/', or None
        self.value = value
    def __str__(self):
        """String representation of the class instance.
        Examples:
            Token(INTEGER, 3)
            Token(MUL, '*')
        """
        return 'Token({type}, {value})'.format(
            type=self.type,
            value=repr(self.value)
        )
    def __repr__(self):
        return self.__str__()
class Lexer(object):
    def __init__(self, text):
        # client string input, e.g. "3 * 5", "12 / 3 * 4", etc
        self.text = text
        # self.pos is an index into self.text
        self.pos = 0
        self.current_char = self.text[self.pos]
    def error(self):
        raise Exception('Invalid character')
    def advance(self):
        """Advance the `pos` pointer and set the `current_char` variable."""
        self.pos += 1
        if self.pos > len(self.text) - 1:
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
        apart into tokens. One token at a time.
        """
        while self.current_char is not None:
            if self.current_char.isspace():
                self.skip_whitespace()
                continue
            if self.current_char.isdigit():
                return Token(INTEGER, self.integer())
            if self.current_char == '*':
                self.advance()
                return Token(MUL, '*')
            if self.current_char == '/':
                self.advance()
                return Token(DIV, '/')
            self.error()
        return Token(EOF, None)
class Interpreter(object):
    def __init__(self, lexer):
        self.lexer = lexer
        # set current token to the first token taken from the input
        self.current_token = self.lexer.get_next_token()
    def error(self):
        raise Exception('Invalid syntax')
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
        """Return an INTEGER token value.
        factor : INTEGER
        """
        token = self.current_token
        self.eat(INTEGER)
        return token.value
    def expr(self):
        """Arithmetic expression parser / interpreter.
        expr   : factor ((MUL | DIV) factor)*
        factor : INTEGER
        """
        result = self.factor()
        while self.current_token.type in (MUL, DIV):
            token = self.current_token
            if token.type == MUL:
                self.eat(MUL)
                result = result * self.factor()
            elif token.type == DIV:
                self.eat(DIV)
                result = result // self.factor()
        return result
def main():
    while True:
        try:
            text = input('calc> ')
        except EOFError:
            break
        if not text:
            continue
        lexer = Lexer(text)
        interpreter = Interpreter(lexer)
        result = interpreter.expr()
        print(result)
if __name__ == '__main__':
    main()
```

## 笔记

#### 正则表达

虽然课程内容里没有过多涉及这部分，但还是复习一下。从[这里](https://www.runoob.com/regexp/regexp-syntax.html)复制的

##### 非打印字符

指不会显示在文本中的字符，主要用于控制文本显示格式，例如换行、分页等。

| 字符  | 描述                                                         |
| ---- | ------------------------------------------------------------ |
| \cx  | 匹配由x指明的控制字符。例如， \cM 匹配一个 Control-M 或回车符。x 的值必须为 A-Z 或 a-z 之一。否则，将 c 视为一个原义的 'c' 字符。 |
| \f   | 匹配一个换页符。等价于 \x0c 和 \cL。                         |
| \n | 匹配一个换行符。等价于 \x0a 和 \cJ。 |
| \r | 匹配一个回车符。等价于 \x0d 和 \cM。 |
| \s | 匹配任何空白字符，包括空格、制表符、换页符等等。等价于 [ \f\n\r\t\v]。注意 Unicode 正则表达式会匹配全角空格符。 |
| \S | 匹配任何非空白字符。等价于 \[^ \f\n\r\t\v]。 |
| \t | 匹配一个制表符。等价于 \x09 和 \cI。 |
| \v | 匹配一个垂直制表符。等价于 \x0b 和 \cK。 |

##### 特殊字符

指正则匹配规则中比较特殊的一些标记。

| 字符 | 描述                                                         |
| ---- | ------------------------------------------------------------ |
| $    | 匹配输入字符串的结尾位置。如果设置了 RegExp 对象的 Multiline 属性，则 $ 也匹配 '\n' 或 '\r'。 |
| ( )  | 标记一个子表达式的开始和结束位置。子表达式可以获取供以后使用。 |
| .    | 匹配除换行符 \n 之外的任何单字符。                           |
| [    | 标记一个中括号表达式的开始。                                 |
| \    | 将下一个字符标记为或特殊字符、或原义字符、或向后引用、或八进制转义符。 |
| ^    | 匹配输入字符串的开始位置，除非在方括号表达式中使用，当该符号在方括号表达式中使用时，表示不接受该方括号表达式中的字符集合。 |
| {    | 标记限定符表达式的开始。                                     |
| \|   | 指明两项之间的一个选择。                                     |

##### 限定符

指限定表达式匹配次数的符号。

| 字符  | 描述                                                         |
| ----- | ------------------------------------------------------------ |
| *     | 匹配前面的子表达式任意次，可以是零次。例如，zo* 能匹配 "z" 以及 "zoo"。* 等价于{0,}。 |
| +     | 匹配前面的子表达式一次或多次。例如，'zo+' 能匹配 "zo" 以及 "zoo"，但不能匹配 "z"。+ 等价于 {1,}。 |
| ?     | 匹配前面的子表达式零次或一次。例如，"do(es)?" 可以匹配 "do" 、 "does" 中的 "does" 、 "doxy" 中的 "do" 。? 等价于 {0,1}。 |
| {n}   | n 是一个非负整数。匹配确定的 n 次。例如，'o{2}' 不能匹配 "Bob" 中的 'o'，但是能匹配 "food" 中的两个 o。 |
| {n,}  | n 是一个非负整数。至少匹配n 次。例如，'o{2,}' 不能匹配 "Bob" 中的 'o'，但能匹配 "foooood" 中的所有 o。'o{1,}' 等价于 'o+'。'o{0,}' 则等价于 'o*'。 |
| {n,m} | m 和 n 均为非负整数，其中n <= m。最少匹配 n 次且最多匹配 m 次。例如，"o{1,3}" 将匹配 "fooooood" 中的前三个 o。'o{0,1}' 等价于 'o?'。请注意在逗号和两个数之间不能有空格。 |

例如：`/[1-9][0-9]*/`是指第一位是1-9的某个数字，后面有任意多个0-9的数字，也就是正整数。

#### 上下文无关语法/BNF

1. 由一系列规则组成，被称为产生式；

2. 一条规则由一个非终结符(叫做 **head** 或 生成式的 **左边**)，一个分号，和一系列终结 符和/或非终结符(叫做 **body** 或 **右边**)组成；

3. 第一条规则左边的非终结符被叫做 **开始符号**；

4. 通过语法派生出算术表达式的方式 为：首先从开始符号开始，然后反复地使用所包含的非终结符的规则替换该终结符， 直到生成一个只包含终结符的句子。语法能组成的句子构成了一门语言。

   例如：一个只有乘除法的计算表达式：

   ```
   expr   : factor ((MUL|DIV) factor)*
   factor : INTEGER
   ```

   有两条规则，其中，`expr`是开始符号，`expr`和`factor`这样的变量是非终结符，`MUL`、`DIV`、`INTEGER`这样的`token`是终结符。

   解读该语法：

   1. `expr` 是一个`factor`后面可选地跟一个乘或除运算符再跟另一个`factor`，后面也相应可选地跟一个乘或除运算符再跟另一个`factor`，如此重复。
   2. `factor`是一个整数。

#### 语法转代码的规则

1. 对于语法中定义的每个规则`R`，将它做成一个有相同名字的方法，对该规则的引用就变成了一个方法调用：`R()`。该方法的方法体遵循该规则的步骤，过程中使用相同的准则。

2. 多选一 `(a1|a2|aN)` 变成 `if-elif-else`语句。

3. 可选组 `(...)*` 变成一个可以执行 0 或多次的`while`循环。

4. 每个`Token`指针`T`变成一个 `eat` 方法调用: `eat(T)`。`eat` 方法的工作是：当它匹配到当前的 `lookahead` 就消耗掉它，然后从`lexer`中得到一个新`token`并将它赋值给内部变量 `current_token`。

   ![语法转换代码示例](https://github.com/kun-bin/image/blob/master/lsbasi_part4_rules.png)

   以上面的语法为例：

   1. `factor`只有一个终结符，所以直接`eat()`

      ```python
      def factor(self):
          self.eat(INTEGER)
      ```

   2. `expr`规则体开始的 `factor` 引用 变成了对 `factor()` 方法的调用。可行组 `(...)*` 变成了一个 `while` 循环，多选一 `(MUL|DIV)` 变成了一个 `if-elif-else` 语句。

      ```python
      def expr(self):
          self.factor()
      
          while self.current_token.type in (MUL, DIV):
      		token = self.current_token
      		if token.type == MUL:
      	    	self.eat(MUL)
      	    	self.factor()
      		elif token.type == DIV:
      	    	self.eat(DIV)
      	    	self.factor()
      ```

现在的代码整体是这样的：分为三个`class`，第一个`Token()`来定义符号类型，第二个`Lexer()`是词法分析器，用来把字符串分割成符合词法规则的词法单元并返回，比如跳过空格、把连续的数字拼凑成一个整数等，第三个`Interpreter()`是语法解释器，按顺序读入词法单元并按语法规则完成运算。

## 练习

1. 写一个语法来描述包含任意数量的 `+`,`-`, `*`, 或`/`操作符的算术表达式。这个语法需要能够派生出像  `2 + 7 * 4`, `7 - 8 / 4`, `14 + 2 * 3 - 6 / 2` 等等这样的表达式。

   这里要考虑运算优先级的问题，所以把乘除法作为一个单元，加减法作为另一个单元，加减法是高一级的，这样乘除法会被作为一个整体来优先运算，语法规则如下：（其实这里偷看了下一章的答案）

   ```
   expr: term((ADD|MINUS)term)*
   term: factor((MUL|DIV)factor)*
   factor: INTEGER
   ```

2. 使用这个语法，写一个可以对包含任意数量的 `+`,`-`, `*`, 或`/`操作符的算术表达式求值的解释器。你们解释器需要能够处理像 `2 + 7 * 4`, `7 - 8 / 4`, `14 + 2 * 3 - 6 / 2` 等等这样的表达式。

   首先在添加加法和减法的符号定义

   ```python
   # 在开头添加定义
   INTEGER, MUL, DIV, EOF, ADD, MINUS = 'INTEGER', 'MUL', 'DIV', 'EOF', 'ADD', 'MINUS'
   
   # 在Lexer()的get_next_token()添加词法判断
   if self.current_char == '+':
   	self.advance()
   	return Token(ADD, '+')
   
   if self.current_char == '-':
   	self.advance()
   	return Token(MINUS, '-')
   ```

   接着把上一题写的语法规则转成代码表达，和课程内容类似，`factor()`部分不变，`term()`部分是课程中的`expr()`，`expr()`部分实际上是把乘除改成加减的`term()`

   ```python
   def factor(self):
       self.eat(INTEGER)
       
   def term(self):
       self.factor()
       while self.current_token.type in (MUL, DIV):
   		token = self.current_token
   		if token.type == MUL:
   	    	self.eat(MUL)
   	    	self.factor()
   		elif token.type == DIV:
   	    	self.eat(DIV)
   	    	self.factor()
   	    	
   def expr(self):
       self.term()
       while self.current_token.type in (ADD, MINUS):
   		token = self.current_token
   		if token.type == ADD:
   	    	self.eat(ADD)
   	    	self.term()
   		elif token.type == MINUS:
   	    	self.eat(MINUS)
   	    	self.term()	    	
   ```

   接下来就是把课程示例代码中的`expr()`改成`term()`，然后仿照着写新的`expr()`了。

   ```python
   # 新的expr()
   def expr(self):
       """Arithmetic expression parser / interpreter.
       expr   : term   ((ADD | MINUS) term)*
       term   : factor ((MUL | DIV) factor)*
       factor : INTEGER
       """
       result = self.term()
       while self.current_token.type in (ADD, MINUS):
           token = self.current_token
           if token.type == ADD:
               self.eat(ADD)
               result = result + self.term()
           elif token.type == MINUS:
               self.eat(MINUS)
               result = result - self.term()
       return result
   ```

   感受是语法规则写出来以后代码就很好写了。

## 检查理解

1. 什么是上下文无关语法（语法）？

   上下文无关文法（英语：context-free grammar，缩写为CFG），在计算机科学中，若一个形式文法`G = (N, Σ, P, S)` 的产生式规则都取如下的形式：`V->w`，则谓之。其中 `V∈N, w∈(N∪Σ)*` 。上下文无关文法取名为“上下文无关”的原因就是因为字符 V 总可以被字串 w 自由替换，而无需考虑字符 V 出现的上下文。一个形式语言是上下文无关的，如果它是由上下文无关文法生成的。

   上下文无关文法重要的原因在于它们拥有足够强的表达力来表示大多数程序设计语言的语法；实际上，几乎所有程序设计语言都是通过上下文无关文法来定义的。另一方面，上下文无关文法又足够简单，使得我们可以构造有效的分析算法来检验一个给定字串是否是由某个上下文无关文法产生的。例子可以参见LR 分析器和LL 分析器。

   BNF（巴克斯-诺尔范式）经常用来表达上下文无关文法。

   （百度百科复制的）

2. 这个语法有几条规则/生成式？（指例子里的那个乘除法的图）

   2条。

3. 什么是终结符？（指出图片中的所有终结符）

   `INTEGER`这种的

4. 什么是非终结符？（指出图片中的所有非终结符）

   `expr`这种的

5. 什么是一条规则的 head？（指出图片中所有的 head/左边）

   最左边的

6. 什么是一条规则的 body？（指出图片中所有的 body/右边）

   右边的部分

7. 什么是一个语法的开始符号？

   最一开始那个

（2.12开始写的文档，2.28才写完......）