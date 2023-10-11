---
title: python课程记录-13
date: 2020-05-26 15:43:07
tags: python
category: 课程笔记
---
这一课讲scipy库。

`from scipy import some_module `

`from scipy.some_module import some_function`

<!--more-->

## linalg模块的使用

1. 基本线性代数操作：

   ```python
   import numpy as np
   from scipy import linalg
   arr = np.array([[1,2],[3,4]])
   # 计算矩阵的行列式
   linalg.det(arr)				
   # output: -2.0
   # 计算特征值和特征向量
   linalg.eig(arr)
   # output: (array([-0.37228132+0.j,  5.37228132+0.j]), array([[-0.82456484, -0.41597356], [ 0.56576746, -0.90937671]]))
   # 矩阵求逆
   linalg.inv(arr)
   # output: array([[-2. ,  1. ], [ 1.5, -0.5]])
   ```

2. 解线性方程组：$Ax=b$，其中A是方阵：`solve(A, b)`

   ```python
   import numpy as np
   from scipy import linalg
   m = 500
   A=np.random.rand(m,m)
   b=np.random.rand(m)
   x1=linalg.solve(A,b)
   x2=np.dot(linalg.inv(A),b)
   print(np.allclose(x1,x2))
   ```

3. 更一般的线性方程组：$Ax=b$，其中A不是方阵：`lstsq(A,q)`找最小二乘解

   例如：给定四个点`(1,6) (2,5) (3,7) (4,10)`，找拟合直线`y=ax+b` 这样的问题可以转化为：矩阵`A=[[1,2],[2,1],[3,1],[4,1]]` ，`b=[6, 5, 7, 10]^T`，`x=[a,b]^T`，求解`Ax=b`

   ```python
   import numpy as np
   from scipy import linalg
   A = np.array([[1,1],[2,1],[3,1],[4,1]])
   y = np.array([6,5,7,10])
   c,resid,rank,sigma=linalg.lstsq(A,y)
   print(c, resid, rank, sigma)
   # [1.4 3.5] 4.200000000000003 2 [5.77937881 0.77380911]
   ```

4. 其他功能：

   1. 范数求解: `linalg.norm`
   2. 广义逆求解: `linalg.pinv, linalg.pinv2`
   3. 矩阵分解：`linalg.sva, linalg.lu, linalg.qr`

## optimize模块的使用

1. 求解带约束条件的函数最小值：`minimize(fun, x0[,args, method, jac, hess, bounds, constrains])`

   1.  fun是目标函数 

   2.  x0是初始解 

   3.  args：需要传递给fun, jac, hess函数的额外的参数 

   4.  method是所选方法：Newton-CG、CG、SLSQP、Nelder-Mead…… 

   5.  jac: Jacobian矩阵，有些方法需要给出 

   6.  hess: Hessian矩阵，有些方法需要给出 

   7.  bounds是解的约束范围， L-BFGS-B,TNC,SLSQP,trust-constr支持 

   8.  constrains是约束条件，COBYLA, SLSQP, trust-constr支持

      例如：$min x1+x2+x3$

      $s.t. x_1x_2x_3>25$

      ​      $x_1^2+x_2^2+x_3^2=40$

      ​     $1<=x_1, x_2<=5$     $x_3>=4$

      $x_0=(3,3,4)$

      ```python
      import numpy as np
      from scipy.optimize import minimize
      def objective(x):
          return np.sum(x)
      
      def constr1(x):
          return x[0]*x[1]*x[2]-25
      
      def constr2(x):
          return np.sum(x**2)-40
      
      def jac(x):
          return np.array([1,1,1])
      
      bnds=((1,5),(1,5),(4,None))
      cons1={'type':'ineq','fun':constr1}
      cons2={'type':'eq','fun':constr2}
      cons=[cons1, cons2]
      result=minimize(objective,[3,3,4],method='SLSQP',jac=jac,bounds=bnds,constraints=cons)
      print(result)
      
      #     fun: 9.807034491627501
      #     jac: array([1., 1., 1.])
      # message: 'Optimization terminated successfully.'
      #    nfev: 7
      #     nit: 7
      #    njev: 7
      #  status: 0
      # success: True
      #       x: array([2.11859914, 2.11859914, 5.5698362 ])
      # result.fun可输出最小值，result.x可输出对应的x解
      ```

2. minimize是局部最优，basinhopping、shgo等可以求解全局最优

3. 求解非线性方程：`root(fun, x0[, args, method, jac])`

   1.  fun是要求根的方程（组） 

   2.  x0是初始猜测解 

   3.  args是fun以及jac中额外的参数 

   4.  method是所选方法： hybr, lm, broyden1/2, anderson, linearmixing, krylov, df-sane 等 

   5.  jac: Jacobian矩阵

      例如：求解 $f(x)=2x^2+3x-10$

      ```python
      import matplotlib.pyplot as plt
      import numpy as np
      from scipy.optimize import root
      
      def func(x):
          return 2*x**2+3*x-10
      
      def jac(x):
          return 4*x+3
      
      x=np.linspace(-5,3)
      plt.plot(x,func(x))
      plt.plot(x,np.zeros(len(x)))
      
      result1=root(func,-3,method='hybr',jac=jac)
      result2=root(func,1,method='lm',jac=jac)
      
      print(result1.fun,result2.fun)
      print(result1.x, result2.x)
      # [-1.77635684e-15] [0.]
      # [-3.10849528] [1.60849528]
      ```

4. 求解非线性方程组：和前面一样，把给定的目标函数和雅可比矩阵写成函数然后运算。

## integrate模块的使用 

1. 根据函数求解积分：

   ```python
   from scipy import integrate
   def half_circle(x):
       return (1-x**2)**0.5
   result = integrate.quad(half_circle, -1, 1)	#积分函数和积分上下限
   print(result)
   # quad是一重积分，dblquad()和tplquad()分别是二重和三重积分
   ```

2. 根据样本求解积分：分布均匀用`romb`，不均匀用`trapz(order 1), simple(order 2)`

   ```python
   from scipy.integrate import simps
   x=np.array([1,3,4])
   y=np.array([1,9,16])
   result=simps(y1,x)
   print(result)	# 21.0
   # 相当于计算x的2次方在1到4的定积分
   ```

3. 求解常微分方程：`odeint(func, y0, t, args=())`

   1.  func: 计算微分方程组中每个未知函数的一阶导数值

   2. y0：微分方程组中每个未知函数的初始值

   3. t：需要进行数值求解的时间点 （数值解）

   4. args：计算导数时的其他参数

      求解方程以后还可以画好看的函数图像。

      这个涉及的数学知识略多，暂时也不用，就先不看例子了

## interpolate模块的使用

1. 插值：

   1. 一/二维插值：interp1d/interp2d 
   2. 多维插值：griddata 
   3. 其他常用插值：Spline样条插值(spl（曲线）, bispl（曲面）等)、Rbf插值

2. `interp1d(x, y, kind='linear‘,……) `

   1. x,y：要插值的数据点，注意x是一个递增序列 
   2. kind：插值的方法：‘linear’, ‘nearest’, ‘zero’, ‘slinear’, ‘quadratic’, ‘cubic’, ‘previous’,  ‘next’等
   3. 返回值：一个用于插值的函数，调用这个函数时以新的x为参数 ，会得到对应的y值。

3. 一维B样条插值：

   1. splrep(x,y,k=3,s,……)：获得一维曲线的B样条表示 
   2.  splev(x, tck, der=0,......)：根据B样条表示得到对应数值 

   呜呜呜我不想看B样条了就这样把

## 拟合

1. 最小二乘拟合： least_squares(fun, x0, bounds=(-inf, inf), method=‘trf’, args,……) 

   1. fun：计算残差向量(residuals)的函数 

   2. x0：猜测的参数值𝑝0 

   3. bounds：参数𝑝的约束范围，2-tuple：((𝑝i的下限),(𝑝i的上限)) 

   4. method： ‘trf’, ‘dogbox’, ‘lm’，其中‘lm’不支持bounds 

   5. args：计算fun需要的其他参数，例如样本数据x，y

      返回值： 

   1. x：求解出来使得S最小的参数𝑝 

   2. fun：对应的残差向量
   
   一个例子：
   
   ```python
   def func(x,p):
       A,k,theta = p
       return A*np.sin(2*np.pi*k*x+theta)
   x=np.linspace(0,2*np.pi,100)
   A,k,theta=10,0.34,np.pi/6	# 真实数据的函数参数
   y0=func(x,[A,k,theta])		# 真实数据
   np.random.seed(0)			# 噪声种子
   y=y0+2*np.random.randn(len(x))	# 添加噪声后的数据
   
   plt.plot(x,y,"o")
   
   def residuals(p,y,x):
       return y-func(x,p)
   p0=[7,0.4,0]
   plsq=optimize.least_squares(residuals,p0,args=(y,x))
   print("真实参数：",[A,k,theta])
   print("拟合参数：",plsq.x)
   plt.plot(x,func(x,plsq.x))
   # 真实参数： [10, 0.34, 0.5235987755982988]
   # 拟合参数： [10.25218748  0.3423992   0.50817423]
   ```
   
2. `scipy.optimize.curve_fit `：实质和最小二乘一样

   用法上和least_squares稍有点不同：不用定义误差函数，直接 使用目标函数，且目标函数的各个待优化参数𝑝直接作为函数的参 数传入。

3. 多项式拟合polyfit：

   1. numpy/scipy.polyfit(x, y, deg,……)： 

      1. x,y：待拟合的数据 
      2. deg：多项式的次数
         返回值：
      p：拟合后的多项式的系数，从**高**位到**低**位
   2. numpy/scipy.polyval(p, x)：计算多项式p在x处的值
   
   例：
   
   ```python
   p = np.polyfit(x,y,10)
   plt.plot(x,np.polyval(p,x),'k-')
   ```
   
4. polynomial模块：

   1. 拟合：和上面的区别在于，返回的多项式系数是从**低**到**高**的

   例：

   ```python
   from numpy.polynomial import polymial as P
   p2=P.polyfit(x,y,10)
   plt.plot(x,P.polyfit(x,p2),'m-')
   ```

   2. 四则运算：`P.polyadd(), P.polysub(), P.polymul(), P.polydiv()`
   
   3. 微分：`P.polyder()`用来求微分以后的多项式参数，默认是一阶导，加参数就是参数对应的导数
   
      例：
   
      ```python
      a = (1,2,3,4)	# 1+2x+3x**2+4x**3
      P.polyder(a)	# (d/dx)(c)=2+6x+12x**2	
      # array([ 2., 6., 12.])
      P.polyder(a,3)	# (d**3/dx**3)(c)=24
      # array([ 24.])
      ```
   
   4. 积分：`P.polyint(c)`  和微分类似的用法
   
   5. 求根：`P.polyroots(a)` 和微分类似，对多项式求根，返回运算之后的参数
   
   ## 随堂练习
   
   IBM.csv中保存了2014年以来的IBM股票信息，请读入IBM的收盘价，然后选取其中2019年的数据，之后： （1）假设每天的收盘价可以用之前5天的收盘价的线性组合表示出来，由此建立一个线性模型进行收盘价的预测，求解最佳的线性组合系数，并绘制收盘价以及预测的收盘价的曲线图。 
   
   （2）采用多项式对收盘价进行拟合，尝试不同的多项式次数， 选取其中较优的结果，打印该多项式的各项系数，并绘制多项式曲线以及收盘价散点图。进一步，求解该多项式的转折点， 即一阶导数为0的点（只要实数解），并在多项式曲线上以上三角的标记绘制出来。
   
   ### 思路
   
   1. 首先是读取csv文件并提取2019年收盘价：
   2. 接下来，第一问可以看作是求解线性方程组的系数，`y=a*x1+b*x2+c*x3+d*x4+e*x5+f`，其中`abcdef`是待求解参数，`x1,x2,x3,x4,x5`分别是连续5天的收盘价，而`y`是第六天收盘价。
      1. 建立存放`x`的列表；
      2. 遍历收盘价列表，把每一组收盘价（连续5个）作为一个列表加到x中，注意这里遍历的终点是倒数第5个，因为再往后就无法凑成连续5个了；
      3. 每一行`x`对应的`y`都是连续5个收盘价的下一个，也就是第6个收盘价开始，即`ibm_2019[5:]`
      4. `x`和`y`都要转为`np.array`，然后用`lstsq`求解系数并绘制图像即可；
   3. 第二问则是多项式拟合，这里我把自变量`x`设置为`1,2,3,...`，因变量`y`就是所有收盘价，然后使用`polyfit`求解并输出和绘图即可。经过尝试和比较，选择了`deg=17`。
   4. 求解一阶导数为0的点，也就是先对之前的多项式求导得到新多项式，再求解新多项式的根，最后在图像中标注即可。
   
   整体代码如下：
   
   ```python
   import numpy as np
   import pandas as pd
   import matplotlib.pyplot as plt
   from scipy import linalg
   from numpy.polynomial import polynomial as P
   ibm = pd.read_csv('IBM.csv', header=0, index_col=0, parse_dates=True)
   ibm_group = ibm.groupby(ibm.index.year)
   ibm_2019 = ibm_group.get_group(2019)['Close']
   x = []
   for i in range(0, len(ibm_2019)-5):
       tmp=[ibm_2019[i],ibm_2019[i+1],ibm_2019[i+2],ibm_2019[i+3],ibm_2019[i+4],1]
       x.append(tmp)
   x = np.array(x)
   y = np.array(ibm_2019[5:])
   c,resid,rank,sigma=linalg.lstsq(x,y)
   x_1=np.linspace(1,y.size,y.size)
   plt.figure()
   plt.plot(x_1,y,'x', x_1,x.dot(c))
   x_2=np.linspace(1,len(ibm_2019),len(ibm_2019))
   p=P.polyfit(x_2,ibm_2019,17)
   print("多项式系数为：",p)
   plt.figure()
   plt.plot(x_2,ibm_2019,'x',x_2,P.polyval(x_2,p),'k-')
   p2=P.polyder(p)
   root=P.polyroots(p2)
   plt.plot(root, P.polyval(root,p),'r^')
   ```
   
   

