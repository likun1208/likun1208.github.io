---
title: Let-s-Build-A-Simple-Interpreter笔记-7
date: 2020-03-15 08:38:20
tags:
  - python
  - 编译原理
  - 学习笔记
categories: Let's Build A Simple Interpreter
---

## 课程文档

[原文](https://ruslanspivak.com/lsbasi-part7/)

[翻译](https://feng-qi.github.io/2018/02/02/lets-build-a-simple-interpreter-part-07/)

（这次翻译又出现了一些不影响理解的小问题。课程代码越来越长，我决定改一下文档结构，不在一开始放整块代码了。）

<!--more-->

## 笔记

这一课的知识点格外多。

#### 抽象语法树和解析树

1. 在之前的代码中，词法分析结束后进行语法分析的同时就完成了运算，这种 interpreter 被称为语法导向解释器 (syntax-directed interpreter)，对于更复杂的语法结构，我们需要建立一个中间表示（intermediate representation, IR），我们的 parser 会 负责构建 IR 而 interpreter 会用来解释由 IR 所代表的输入。一般来说会用树来构建IR。

2. 解析树（有时叫做具体语法树）是一个根据我们的语法定义来表示一门语言的句法结构的树形结构。它基本上展示了你的 `parser` 如何识别语言结构或者， 换句话说，它展示了你语法的开始符号怎么派生出该编程语言中一个特定的字符串的。

3. 抽象语法树（AST）是我们的解释器和未来编译器项目的中心数据结构。

4. 如下图，分别是的AST和解析树。

   ![AST和解析树对比](https://github.com/kun-bin/image/blob/master/lsbasi_part7_ast_01.png?raw=true)

5. 简单来说，AST就是把操作数放到叶节点，操作符放到中间节点和根节点，操作符节点在树中的高度可以体现运算优先级，括号改变优先级也是通过改变操作符的高度。

6. 具体到代码，首先要构建一个AST类，目前这个类里什么也没有，就只是用来让别的类继承。

   ```python
   class AST():
       pass
   ```

   接下来要定义这个树的节点类：

   ```python
   # 二元操作符类，继承AST
   class BinOp(AST):
       def __init__(self, left, op, right):
   		self.left = left
   		self.token = self.op = op
   		self.right = right
       
   # 整数类，继承AST
   class Num(AST):
       def __init__(self, token):
   		self.token = token
   		self.value = token.value
   ```

   `Token`这个类和前几课里的完全一致。

   到此为止，我们就创建好AST的基本结构了，接下来可以通过赋值的方式一点点创建一个解析式的AST，例如`2*7+3`的AST：

   ```python
   # 建立加号和乘号的Token
   mul_token = Token(MUL, '*')
   plus_token = Token(PLUS, '+')
   # 先建立乘法操作符的节点
   mul_node = BinOp(
       # 左边是第一个乘数
       left=Num(Token(INTEGER, 2)),
       # 是乘法
       op=mul_token,
       # 右边是第二个乘数
       right=Num(Token(INTEGER, 7))
   )
   #建立加法操作符的节点
   add_node = BinOp(
       # 左边是刚才进行的乘法节点
       left=mul_node,
       # 中间是加号
       op=plus_token,
       # 右边是加数
       right=Num(Token(INTEGER, 3))
   )
   ```

7. 把上述内容合并到之前的代码中，在`Lexer`得到词法单元后，不是直接进行运算，而是建立AST，即：

   ```python
   class Parse:
       def __init__(self, lexer):
           和之前一样
       def error(self):
           和之前一样
       def eat(self, token_type):
           和之前一样
       def factor(self):
           token = self.current_token
   		if token.type == INTEGER:
               self.eat(INTEGER)
               # 之前是return token.value
   	    	return Num(token)
   		elif token.type == LPAREN:
               self.eat(LPAREN)
               # 之前是value = self.expr()
               node = self.expr()
               self.eat(RPAREN)
               # 之前是return value
               return node
       def term(self):
           # 之前是value = self.factor()
   		node = self.factor()
   		while self.current_token.type in (MUL, DIV):
               token = self.current_token
               if token.type == MUL:
               	self.eat(MUL)
                   # 之前有value *= self.factor()求值
               elif token.type == DIV:
               	self.eat(DIV)
                   # 之前有value /= self.factor()求值
   	    	node = BinOp(left=node, op=token, right=self.factor())
           # 之前是return value
   		return node
       def expr(self):
           # 之前是value = self.term()
           node = self.term()
   		while self.current_token.type in (PLUS, MINUS):
   		    token = self.current_token
   	    	if token.type == PLUS:
   				self.eat(PLUS)
                   # 之前有value += self.factor()求值
   	    	elif token.type == MINUS:
   				self.eat(MINUS)
                   # 之前有value -= self.factor()求值
   	    	node = BinOp(left=node, op=token, right=self.term())
           # 之前是return value
   		return node
       def parse(self):
   		return self.expr()
   ```

8. 总结一下就是，原本求值的地方，现在改为不断给`node`赋值，而`node`则层层嵌套，最终可以表达出AST。

9. 以`7 + 3 * (10 - 1) `为例，看这份代码：

   1. `parse`调用`expr()`，进入`expr`；
   2. `expr`调用`term`，`term`调用`factor`，发现是`INTEGER`，于是返回`Num(token(INTEGER,7))`作为`node`，`term`发现接下来不是乘除法，于是直接返回`node`，回到`expr`，该节点类型以下简写为`Num(7)`；
   3. `expr`发现接下来是加法，于是`node`变为`BinOp(left=Num(7), op=Token(PLUS, '+'), right=self.term())`，再次调用`term`；
   4. `term`调用`factor`得到`Num(3)`，接下来是乘法，于是这里要返回给第三步中`expr`的`right`的东西变成了`BinOp(left=Num(3), op=Token(MUL, '*'), right=self.factor())`，显然又要调用`factor`；
   5. `factor`判断接下来的左括号，于是调用`expr`处理，内容和上面的类似，会返回`BinOp(left=Num(10), op=Token(MINUS, '-'), right=Num(1))`给第四步的`right`；
   6. 以此类推，最终`parse`中得到的是`node=BinOp(left=Num(7), op=Token(PLUS, '+'), right=BinOp(left=Num(3), op=Token(MUL, '*'), BinOp(left=Num(10), op=Token(MINUS, '-'), right=Num(1))))`。

#### 遍历树求值

1. 在得到AST之后，就要开始求值了，我们采用后序遍历的方法来遍历AST进行运算。

   1. 前序遍历：**根**左右

   2. 中序遍历：左**根**右

   3. 后序遍历：左右**根**

   4. 显然前中后是指根节点的遍历输出顺序，如下图的树，前序遍历的输出结果是：1->2->4->6->7->3->5，中序遍历是4->6->7->2->1->5->3，后序遍历是7->6->4->2->5->3->1

      ![一棵树](https://github.com/kun-bin/image/blob/master/visit.png?raw=true)

   5. 对应到AST中，就是先访问`node`的`left`，得到一个结果，再访问`right`，得到另一个结果，最后访问中间的`op`，进行运算，伪代码如下：

      ```python
      def visit(node):
          # for every child node from left to right
          for child in node.children:
      		visit(child)
          # 指加减乘除等运算操作
          <<postorder actions>>
      ```

2. 有时可能三种遍历都需要进行一些操作，因此伪代码改为：

      ```python
      def visit(node):
          << preorder actions >>
          left_val = visit(node.left)
          << inorder actions >>
          right_action = visit(node.right)
          << postorder actions >>
      ```

3. 具体来实现的时候首先要有`NodeVisitor`类：

      ```python
      class NodeVisitor():
          def visit(self, node):
              method_name = 'visit_' + type(node).__name__
              visitor = getattr(self, method_name, self.generic_visit)
              return visitor(node)
      
          def generic_visit(self, node):
      		raise Exception('No visit_{} method'.format(type(node).__name__))
      ```

      `getattr(object, name, default)`函数的作用是，返回`object`这个变量的`name`属性，如果没有`name`则返回`default`，如果用这个函数的时候没有指明`default`，那么当`name`属性不存在时会报错。

      `f.__name__`的作用是返回`f`的函数名。

      在这段代码中，首先`method_name`是当前节点`node`对应`Token`的`type`，接下来返回该`NodeVisitor`对象的`method_name`属性，如果不存在则报错。具体来说，当`node`类型是`BinOp`时，会返回`visit_BinOp(node)`，而`node`类型是`Num`时，则返回`visit_Num(node)`。

4. 接下来看我们的解释器`Interpreter`类，它继承了`NodeVisitor`：

      ```python
      class Interpreter(NodeVisitor):
          def __init__(self, parser):
      		self.parser = parser
          def visit_BinOp(self, node):
              if node.op.type == PLUS:
                  return self.visit(node.left) + self.visit(node.right)
              elif node.op.type == MINUS:
                  return self.visit(node.left) - self.visit(node.right)
              elif node.op.type == MUL:
                  return self.visit(node.left) * self.visit(node.right)
              elif node.op.type == DIV:
                  return self.visit(node.left) / self.visit(node.right)
          def visit_Num(self, node):
      		return node.value
          def interpret(self):
              tree = self.parser.parse()
              return self.visit(tree)
      ```

      首先是解释器的初始化，我们将`parser`得到的AST传入`Interpreter`。

      接下来访问二元运算符节点的函数，该函数逻辑很简单，就是判定传入的`node`对应的`op`类型是什么，然后访问其左右节点，最后进行运算。

      最后是访问整数节点的函数，该函数负责将节点的`value`返回。

      这里要总结一下`node`这个变量，因为`python`里不需要指定变量类型，会动态决定，实际上这里的`node`有两个可能，当它是叶节点的时候，它是整数，也就是`Num(Token)`类型，在`visit`函数后会返回并执行`visit_Num`函数；而当它是中间节点和根节点时，就变成操作符，也就是`BinOp(left, op, right)`类型，在`visit`函数后会返回并执行`visit_BinOp`函数。

5. 当调用解释器的`interpret`函数时，首先进入`visit`函数，判定是操作符，转入`visit_BinOp`函数，再判断运算类型，并访问左右节点直到得到一个返回值，最后进行运算得到结果。

6. 总结一下，整个流程是：parser 从 lexer 中 得到 token 然后返回生成的 AST 给 interpreter 进行遍历并解释执行所给输入。

#### 递归下降

一个 **递归下降parser** 就 是一个自顶向下的 parser，它使用一组递归过程来处理输入。自顶向下反映了 parser 从 构建解析树的顶部结点开始逐渐构建更低的结点这一事实。

## 练习

1. 写一个翻译器（提示：node visitor），它接收一个算术表达式作为输入并打印出它的后缀形式，即逆波兰式(Reverse Polish Notation, RPN)。例如，如果翻译器接收的输入是 表达式 `(5 + 3) * 12 / 3` 则输入应该是 `5 3 + 12 * 3 /` 。答案在[这儿](https://github.com/rspivak/lsbasi/blob/master/part7/python/ex1.py)，不过要先自己解决再看啊。

   我的想法是：逆波兰式输出顺序实际上就是这一课遍历的顺序，因此只需要在现有的`visit_BinOp`和`visit_Num`加上`print`就行，注意这里因为是后序输出，`BinOP`需要先`visit(left)`和`visit(right)`，再`print(op.value)`，代码修改如下：

   ```python
   def visit_BinOp(self, node):
   	if node.op.type == PLUS:
           tmp = self.visit(node.left) + self.visit(node.right)
       elif node.op.type == MINUS:
           tmp = self.visit(node.left) - self.visit(node.right)
       elif node.op.type == MUL:
           tmp = self.visit(node.left) * self.visit(node.right)
       elif node.op.type == DIV:
           tmp = self.visit(node.left) // self.visit(node.right)
       print(node.op.value, end=' ')
       return tmp
   
   def visit_Num(self, node):
       print(node.value, end=' ')
       return node.value
   ```

2. 写一个翻译器（node visitor），它接收一个算术表达式作为输入并将它打印为 LISP 风 格的记法，即 `2 + 3` 变成 `(+ 2 3)` 及 `(2 + 3 * 5)` 变成 `(+ 2 (* 3 5))` 。你 可以在[这儿](https://github.com/rspivak/lsbasi/blob/master/part7/python/ex2.py)打到答案，但在查看之前还是要先尝试自己解决。

   这是要前序遍历输出，也就是先输出`op.value`，再进行`visit(left)`和`visit(right)`，和练习1相比，就是把`visit_BinOp`中的`print(node.op.value, end=' ')`放到最前面。

（这俩练习的答案和我想的还有点不太一样......不过问题不大......）

