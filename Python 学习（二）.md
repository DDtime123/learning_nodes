# Python 学习（二）

### 1. Python 关键字

| and     | as       | assert   | wait   |
| ------- | -------- | -------- | ------ |
| class   | continue | def      | del    |
| elif    | else     | except   | False  |
| finally | for      | from     | global |
| if      | import   | in       | is     |
| lambda  | None     | nonlocal | not    |
| or      | pass     | raise    | return |
| True    | try      | while    | with   |
| yield   |          |          |        |

* True：此关键字用于表示布尔值 true，如果语句为真，则打印 “True”

* False：此关键字用于表示布尔值 false，如果语句为真，则打印 “False”

* None：这是一个特殊的常量，用于表示 null 值 或者 void，值得注意的是，0 和任何空容器（如空列表）都不会被计算为None

  ~~~python
  print(None == 0)
  print(None == [])
  ~~~

  输出:

  ~~~python
  False
  False
  ~~~

* and：逻辑运算符， and 返回第一个假值，没有找到假值则返回最后一个值

  ~~~python
  3 and 0 # 返回 0 
  3 and 10 # 返回 10
  ~~~

* or：逻辑运算符，or 返回第一个真值，如果未找到真值则返回最后一个值

  ~~~python
  3 or 0 # 返回 0
  3 or 10 # 返回 3
  0 or 0 # 返回 0
  ~~~

* not：此逻辑运算符反转真值。

* in：此关键字用于检查容器是否包含值，还可以用于循环遍历容器

  ~~~python
  print('s' in 'geeksforgeeks') # 返回 true
  
  for i in 'geeksforgeeks':
      print(i,end=" ")	# 打印 g e e k s f o r g e e k s
  ~~~

* is：此关键字用于测试对象身份，即检查两个对象是否占用相同的内存位置

  ~~~python
  print (' ' is ' ') # 字符串字面量和元组是不可变的，它们共享同一份内存，返回 True
  print ({} is {})   # 字典是可变的，不共享一份内存，返回 False
  ~~~

* for，while：此二关键字用于控制流和for循环

  break：“break” 用于跳出循环并将控制权传递给紧跟在循环之后的语句

  continue：“continue” 用于跳过循环的当前迭代但不结束循环

  ~~~python
  for i in range(10):
      print(i,end=" ")
      
      if i == 6:
          break;	# 打印 0 1 2 3 4 5 6
          
  i = 0
  while i < 10:
      if i == 6:
          i+=1;
          continue;
      else:
      	print(i,end=" ");
          
      i += 1;	# 打印 0 1 2 3 4 5 7 8 9
  ~~~

* if：用于决策的控制语句。表达式为真则进入 “if” 语句块

  else：用于决策的控制语句。表达式为假则进入 “else” 语句块

  elif：用于（多条件决策）决策的控制语句。是 ”else if“ 的缩写

  ~~~python
  i = 20
  if (i == 10):
      print ("i is 10")
  else:
      print ("i is not 10") # if else 二条件决策
  
  i = 20
  if (i == 15):
      print ("i is 15")
  elif (i == 10):
      print ("i is 10")
  elif (i == 5):
      print ("i is 5")
  else:
      print ("i is not 15, 10 or 5") # if elif else 多条件决策
  ~~~

* def 关键字：用于声明用户定义的函数

  ~~~python
  def func():
      print("def defines function")
  
  func(); // 打印 def defines function
  ~~~

* return：此关键字用于从函数返回

  yield：此关键字的用法类似 return 语句，但是它用于返回一个**生成器**

  ~~~python
  def func():
      S = 0;
      for i in range(10):
          S += i;
      return S;
  
  print(func()) # 打印 sum(0...10) -> 45
  
  def func():
      S = 0;
      for i in range(10):
          S += i;
      yield S;
      
  for i in range(10):
      print(i);  // 打印 0 1 3 6 10 15 21 28 36 45
  ~~~

* class：“class” 关键字用于声明用户定义的类

  ~~~python
  class Dog:
      
      attr1 = "mammal";
      attr2 = "dog"
      
      def func(self):
          print("I'm a",self.attr1);
          print("I'm a",self.attr2);
          
  
  Rodger = Dog() # 实例化一个Dog对象
  
  print(Rodger.attr1) # 访问对象的属性
  Rodger.func()	# 调用对象方法
  ~~~

* with：“with” 关键字用于将代码块的执行包装在上下文管理器定义的方法中。这个关键字在日常编程中用得不多

  ~~~python
  with open('path/file','w') as file:
      file.write('Hello World!');
  ~~~

* as：“as” 关键字用于为导入的模块创建别名，通常用于为一些很长的模块与包的组合创建别名，方便调用它们。

  ~~~python
  import math as gfg
  print(gfg.factorial(5))
  ~~~

* pass：“pass” 是 python 中的空语句，用于防止缩进错误并用作占位符

  ~~~python
  n = 10
  for i in range(n):
      
  pass
  
  ~~~

* lambda：“lambda” 关键字用于制作内联返回函数，内部不允许使用任何语句

  ~~~python
  g = lambda x: x*x*x
  
  print(g(7)) # 打印 7*7*7 = 343
  ~~~

* import：此语句用于将特定模块包含到当前程序中。

  from：通常与 import 一起使用，from 用于从导入的模块中导入特定功能

  ~~~python
  import math
  print(math.factorial(10))
  
  from math import factorial
  print(factorial(10))
  ~~~

* try：该关键字用于异常处理，用于捕获使用关键字 except 的代码中的错误。

  except：“except” 与 “try” 一起使用用于捕获异常

  finally：无论 “try” 块的结果如何，始终执行称为 “finally” 的块

  raise：可以使用 “raise” 关键字显示引发异常

  assert：断言，用于调试目的。通常用于检查代码的正确性。

  ~~~python
  a = 4
  b = 0
  
  try:
      k = a//b
      print(k)
      
  except ZeroDivisionError:
      print("不能除以0")
      
  finally:
      print('这个总是执行')
      
  print("a / b 的值是:")
  assert b != 0,"除以0错误"
  print(a/b)
  ~~~

  输出：

  ~~~python
  不能除以0
  这个总是执行
  a / b 的值是:
  AssertionError: 除以0错误
  ~~~

* del：“del” 用于删除对对象的引用，可以使用 “del” 删除任何变量或列表值

  ~~~python
  my_varuable1 = 20
  my_variable2 = "GeeksForGeeks"
  
  print(my_variable1)
  print(my_variable2)
  
  del my_variable1
  del my_variable2
  
  print(my_variable1) # 抛出未定义变量错误并停止执行程序
  print(my_variable2)
  ~~~

  输出：

  ~~~python
  20
  GeeksForGeeks
  NameError: name 'my_variable' is not defined
  ~~~

* global：此关键字用于将函数内部的变量定义为全局范围

  nonlocal：此关键字的工作方式类似 “global” ，但是它用于声明一个变量用于指向外部封闭函数的变量

  ~~~python
  a = 15
  b = 10
  
  def add():
      c = a + b;
      print(c);
      
  add();
  
  # 使用 nonlocal 关键字
  def func():
      var1 = 10
      
      def gun():
          # nonlocal 声明该变量是在外部封闭函数中定义的，这里是它的一个引用
          nonlocal var1;
          var1 = var1 + 10;
          print(var1)
          
      gun();
  func();
  ~~~

  输出：

  ~~~python
  25
  20
  ~~~

### 2. Python 命名空间和作用域



