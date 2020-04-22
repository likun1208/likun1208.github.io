---
title: Let's Build A Simple Interpreter笔记[3]
date: 2020-02-01 11:25:53
tags: 
	- python
	- 编译原理
	- 学习笔记
categories: Let's Build A Simple Interpreter
---

## 课程文档

[原文](https://ruslanspivak.com/lsbasi-part3/)

[翻译](https://feng-qi.github.io/2018/01/26/lets-build-a-simple-interpreter-part-03/)

<!--more-->

## 代码

```python
# Token types
#
# EOF (end-of-file) token is used to indicate that
# there is no more input left for lexical analysis
INTEGER, PLUS, MINUS, EOF = 'INTEGER', 'PLUS', 'MINUS', 'EOF'
class Token():
    def __init__(self, type, value):
        # token type: INTEGER, PLUS, MINUX, or EOF
        self.type = type
        # token value: non-negative integer value, '+', '-', or None
        self.value = value
    def __str__(self):
        """String representation of the class instance.
        Examples:
        Token(INTEGER, 3)
        Token(PLUS, '+')
        """
        return f'Token({self.type}, {repr(self.value)})'
    def __repr__(self):
        return self.__str__()
    
class Interpreter():
    def __init__(self, text):
        # client string input, e.g. "3 + 5", "12 - 5 + 3", etc
        self.text = text
        # self.pos is an index into self.text
        self.pos = 0
        # current token instance
        self.current_token = None
        self.current_char = self.text[self.pos]
    ##########################################################
    # Lexer code                                             #
    ##########################################################
    def error(self):
        raise Exception('Invalid syntax')
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
            else:
                self.error()
        return Token(EOF, None)
    ##########################################################
    # Parser / Interpreter code                              #
    ##########################################################
    def eat(self, token_type):
        # compare the current token type with the passed token
        # type and if they match then "eat" the current token
        # and assign the next token to the self.current_token,
        # otherwise raise an exception.
        if self.current_token.type == token_type:
            self.current_token = self.get_next_token()
        else:
            self.error()
    def term(self):
        """Return an INTEGER token value."""
        token = self.current_token
        self.eat(INTEGER)
        return token.value
    def expr(self):
        """Arithmetic expression parser / interpreter."""
        # set current token to the first token taken from the input
        self.current_token = self.get_next_token()
        result = self.term()
        while self.current_token.type in (PLUS, MINUS):
            token = self.current_token
            if token.type == PLUS:
                self.eat(PLUS)
                result += self.term()
            elif token.type == MINUS:
                self.eat(MINUS)
                result -= self.term()
            else:
                self.error()
        return result
def main():
    while True:
        try:
            text = input('calc> ')
        except EOFError:
            print()
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

这一课实现了多个数字的加减操作，思路大致和我想的差不多，不过循环条件是“当前字符是加号或减号”。

引入了句法图的使用和句法分析的概念。

> parser 只是识别出结构并保证它符合某些规范，interpreter 在 parser 成功识别后对表达式进行求值。

## 练习

1. 画一张只包含乘除法的算术表达式句法图，例如“7 * 4 / 2 * 3”。不开玩笑，拿只钢笔 或铅笔试试。

   和加减法的句法图一样。

2. 修改计算器的源代码使它解释只包含乘除法的算术表达式，如“7 * 4 / 2 * 3”。

   把代码里的加减换成乘除就行。

3. 从头写一个可以处理如“7 - 3 + 2 - 1”这样算术表达式的解释器。使用任何你喜欢的语 言都可以，只靠自己，不要参考例子。做这件事时，想想都需要包含的组件：lexer 获取 输入并把它转化为 token 流，parser 从 lexer 提供的 token 流中识别结构， interpreter 在 parser 成功识别到一个合法的算术表达式之后求得其结果。把这些连起 来。花点时间把你学到的知识转化为一个可以运行的算术表达式解释器。

   因为我别的语言都不太会，所以决定用C语言。本以为，这是个非常简单的事情，没想到写了两天，我好难过啊T_T

   第一天发现根本没法不看python代码直接写，这说明我对整个算法流程都还不熟悉，只知道大概原理，没法自己实现，然后开始照着python代码写。这个过程中还发现我对C语言也不太熟悉，很多东西都记不清了，尤其是字符串数组和指针，实际上我到现在也没搞明白`char *str`和`char str[]`什么时候用哪个、怎么用。

   第二天，也就是今天（20200202），我决定全用`char str[100]`这种，但还是有各种各样的问题，不过都渐渐解决了。然后花了一个多小时，才发现最大的问题在于C语言的字符串操作完全不能直接用`=`之类的方式搞，要用`strcpy`和`strcmp`，然后又是一个多小时，才发现我把`strcpy(new,old)`写成了`strcpy(old,new)`，然后第三个一小时，发现我上一步有两个用了`strcpy`的没改过来，还把一个`while`里的`==`写成了`=`。我怕是个傻子吧......

   总之，现在写完了，能正常运行出结果。代码如下：

   ```
   #include <stdio.h>
   #include <stdlib.h>
   #include <string.h>
   #include<assert.h>
   #include<stdbool.h>
   #define SWAP(a,b) (a)^=(b);(b)^=(a);(a)^=(b)
   
   char INTEGER[100]="INTEGER";
   char PLUS[100]="PLUS";
   char MINUS[100]="MINUS";
   char END[100]="EOF";
   int i=1;
   
   struct Token		//词法单元
   {
   	char type[100];	//类型，可以是integer、plus、minus和end
   	char value[100];	//值，可以是数字，+，-，eof
   };
   struct Intepreter		//解释器
   {
   	int pos;			//索引
   	char current_char;	//当前字符
   	char text[100];		//输入的字符串
   	Token token;		//当前词法单元
   };
   Intepreter intepreter;
   int error()			//输错符号的报错
   {
   	printf("Invalid syntax");
   	exit(100);
   }
   Intepreter init(char text[100])	//初始化解释器
   {
   	Token tmp;
   	tmp.type[100]={};
   	tmp.value[100]={};
   	Intepreter intepreter;
   	strcpy(intepreter.text,text);
   	intepreter.pos=0;
   	intepreter.current_char=text[intepreter.pos];
   	strcpy(intepreter.token.type,tmp.type);
   	strcpy(intepreter.token.value,tmp.value);
   	return intepreter;
   }
   //索引后移一位改变当前字符
   Intepreter advance()
   {
   	intepreter.pos+=1;
   	if (intepreter.pos>=strlen(intepreter.text))
   	{
   		intepreter.current_char='\0';
   	}
   	else
   	{
   		intepreter.current_char=intepreter.text[intepreter.pos];
   	}
   	return intepreter;
   }
   //跳过空格
   void skip_whitespace()
   {
    	while(intepreter.current_char==' ')
       {
           intepreter=advance();
       }
   }
    //返回整数词法单元
   int integer()
   {
    	int result=0;
    	while (intepreter.current_char>='0' && intepreter.current_char<='9')
    	{
    		result=result*10+(intepreter.current_char-'0');
    		intepreter=advance();
   	}
   	return result;
   }
   
   //用来取词法单元
   Token get_next_token()
   {
   	Token token;
   	while(intepreter.current_char!='\0')
   	{
   		if(intepreter.current_char==' ')
   		{
   			skip_whitespace();
   			continue;
   		}
   		else if(intepreter.current_char>='0' && intepreter.current_char<='9')
   		{
   			strcpy(token.type,INTEGER);
   			itoa(integer(),token.value,10);
   			return token;
   		}
   		else if(intepreter.current_char=='+')
   		{
   			intepreter=advance();
   			strcpy(token.type,PLUS);
   			strcpy(token.value,"+");
   			return token;
   		}
   		else if(intepreter.current_char=='-')
   		{
   			intepreter=advance();
   			strcpy(token.type,MINUS);
   			strcpy(token.value,"-");
   			return token;
   		}
   		else
   		{
   			error();
   		}
   	}
   	strcpy(token.type,END);
   	strcpy(token.value,"EOF");
   	return token;
   }
   void eat(char type[100])
   {
   	if (strcmp(intepreter.token.type,type)==0)
   	{
   		intepreter.token = get_next_token();
   	}
   	else
   	{
   		int a = error();
   	}
   }
   int term()
   {
       Token token;
       strcpy(token.type,intepreter.token.type);
       strcpy(token.value,intepreter.token.value);
       eat(INTEGER);
       return atoi(token.value);
   }
   //用来算表达式
   int expr()
   {
   	int result=0;
   	Token token;
   	intepreter.token=get_next_token();
       result=term();
       while ((strcmp(intepreter.token.type,PLUS)==0)||(strcmp(intepreter.token.type,MINUS)==0))
       {
           strcpy(token.type,intepreter.token.type);
           strcpy(token.value,intepreter.token.value);
           if (strcmp(token.type,PLUS)==0)
           {
               eat(PLUS);
               result=result+term();
           }
           else if (strcmp(token.type,MINUS)==0)
           {
               eat(MINUS);
               result-=term();
           }
           else
           {
               error();
           }
       }
   	return result;
   }
   int main()
   {
   	char text[100];
   	printf("calc>");
   	scanf("%s", &text);
   	intepreter=init(text);
   	int result=expr();
   	printf("result is %d\n",result);
   	return 0;
   }
   ```

## 检查理解

1. 什么是句法图？

   **句法图** 就是程序语言句法规则的图形表示。基本上，句法图从视觉上向 你展示了在你的程序语言中哪些语句是允许的哪些是不允许的。

2. 什么是句法分析？

   从 token 流中识别组合的过程叫 **parsing**。

3. 什么是句法分析器？

   解释器或编译器中执行parsing的部分叫 **parser**。