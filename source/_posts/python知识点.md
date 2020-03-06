---
title: python知识点
author: 范耀文
top: false
cover: false
toc: true
mathjax: false
date: 2020-02-25 09:46:58
img: /medias/banner/python.jpg
coverImg: 
password:
summary:
	总结了一些Python中的知识点。
tags:
	- python
categories:
	- 编程语言
---

## 工厂函数与内建函数

+ **内建函数**，

  一般都是因为==使用频繁或是元操作==，所以通过内置函数的形式提供出来，通过对python的内置函数分类分析可以看出来：基本的数据操作基本都是一些数学运算（当然除了加减乘除）、逻辑操作、集合操作、基本IO操作，然后就是对于语言自身的反射操作，还有就是字符串操作，也是比较常用的，尤其需要注意的是反射操作。

+ **工厂函数**

  工厂函数都是==类对象==, 即当你调用他们时, 创建的其实是一个类实例

+ **内建方法**

  内建类型的==成员方法==

## 单例模式与工厂模式

### 单例模式

+ 含义

  确保某一个类中只有一个实例，而且自行实例化并向整个系统提供这个实例，这个类称为单例类，单例模式是一种对象型模式，整个系统中只需要存在一个对象，所有的信息都从这个对象获取，比如系统的配置对象，或者是线程池。这些场景下，就非常适合使用单例模式。

+ 设计理念

  单例模式的实现只需要找一个变量存放创建的实例，然后每次获取实例时，先检查变量中是否已保存实例，如果没有则创建一个实例并将其存放到变量中，以后都从这个变量中获取实例就可以了。单例模式中，只会创建一次实例。

+ 优点

  由于单例模式的设计要求使得每一个应用、活动只有一个实例，这使得不管我们怎么去调用、实例化，当前唯一存在一个实例，这在资源调度、日志管理、信息注册等应用场景下保证了只有一个实例对其进行操作，而避免了多个实例同时操作一个对象，这保证了协调系统的整体性和统一性。

+ 实现方法
  + 类中静态方法new()的实现
  + 装饰器实现

+ 实现方法一的设计代码

  ```python
  class User(object):
      __instance = None
      def __new__(cls,name):
          if not cls.__instance:
              cls.__instance = object.__new__(cls)
          return cls.__instance
      def __init__(self,name):
          self.name = name
  ```

+ 参考文章
  
  + [单例模式的妙用](https://zhuanlan.zhihu.com/p/87524388)

### 工厂模式

#### 简单工厂模式

+ 定义

  简单工厂模式(Simple Factory Pattern)：又称为静态工厂方法(Static Factory Method)模式，它属于类创建型模式。==在简单工厂模式中，可以根据参数的不同返回不同类的实例==。简单工厂模式专门定义一个类来负责创建其他类的实例，被创建的实例通常都具有共同的父类。

+ 结构

  + Factory：工厂类

  + Product：抽象产品类

  + Concrete Product：具体产品类

+ 设计代码

  ```python
  # 抽象产品类
  class WeChat(object):
  
      def send_message(self, content):
          pass
  
      def send_image(self, imageid):
          pass
  # 具体产品类
  class AccountA(WeChat):
  
      def send_message(self, content):
          print("使用企业微信账号A推送信息: ", content)
  
      def send_image(self, imageid):
          print("使用企业微信账号A推送图片: ", imageid)
  
  class AccountB(WeChat):
  
      def send_message(self, content):
          print("使用企业微信账号B推送信息: ", content)
  
      def send_image(self, imageid):
          print("使用企业微信账号B推送图片: ", imageid)
  # 工厂类
  class WeChatFactory(object):
  	@staticmethod
      # 增加账户需要修改此处代码，不否合开放封闭原则
      def create_wechat(type):
          if type=="A":
              account=AccountA()
              return account
          elif type=="B":
              account=AccountB()
              return account
          else:print("输入类型错误，没有此账户")
  # 使用者
  class Person(object):
      def __init__(self,name):
          self.name=name
      def work(self,axe_type):
          print("%s开始工作"%self.name)
  
          #在原始社会，需要一把石斧
          axe=Factory.create_axe(axe_type)
          axe.Cut_tree()
          #在现代社会，需要一把铁斧头
  if __name__ == "__main__":
      # 实例化账号
      wechat_factory = WeChatFactory()
      # 创建账号A的微信对象
      wechat1 = wechat_factory.create_wechat("A")
      wechat2 = wechat_factory.create_wechat("A")
      wechat3 = wechat_factory.create_wechat("A")
      # 使用账号A对象发送信息
      wechat1.send_message(content="haha")
      wechat2.send_message(content="hehe")
      wechat3.send_message(content="xixi")
      # 创建账号B的微信对象
      wechat4 = wechat_factory.create_wechat("B")
      wechat5 = wechat_factory.create_wechat("B")
      # 使用账号B对象发送信息
      wechat4.send_message(content="heihei")
      wechat5.send_message(content="pupu")
  ```

+ 简单工厂模式的优点

  + 工厂类含有必要的判断逻辑，可以决定在什么时候创建哪一个产品类的实例，客户端可以免除直接创建产品对象的责任，而仅仅“消费”产品；简单工厂模式通过这种做法实现了对责任的分割，它提供了专门的工厂类用于创建对象。

  + ==客户端无须知道所创建的具体产品类的类名，只需要知道具体产品类所对应的参数即可，对于一些复杂的类名，通过简单工厂模式可以减少使用者的记忆量==。

  + 通过引入配置文件，可以在不修改任何客户端代码的情况下更换和增加新的具体产品类，在一定程度上提高了系统的灵活性。

+ 简单工厂模式的缺点

  + 由于工厂类集中了所有产品创建逻辑，一旦不能正常工作，整个系统都要受到影响。

  + 使用简单工厂模式将会增加系统中类的个数，在一定程序上增加了系统的复杂度和理解难度。

  + ==系统扩展困难，一旦添加新产品就不得不修改工厂逻辑，在产品类型较多时，有可能造成工厂逻辑过于复杂，不利于系统的扩展和维护。==

  + 简单工厂模式由于使用了静态工厂方法，造成工厂角色无法形成基于继承的等级结构。

+ 参考资料

  + [python实现简单工厂模式](https://blog.csdn.net/weixin_34233421/article/details/94607114)

  + [python实现工厂模式](https://www.jianshu.com/p/a1b97b93614e)

#### 工厂方法模式

+ 定义

  ==定义一个接口来创建对象==，工厂本身并不负责创建对象，由子类完成 2、工厂方法的创建通过继承而不是实例化完成 3、工厂方法使得设计更加具有可定制性，返回相同的实例或子类，而不是某种类型的对象（类似简单工厂方法）

+ 设计理念

  + 定义一个抽象的工厂接口类
  + 定义一个抽象的产品接口类
  + 根据需要从产品接口派生产品子类
  + 对于每一个产品子类，从工厂接口派生工厂子类，负责该产品的创建
  + 客户端根据实际需要，调用工厂子类，创建所需要的产品。

  

+ 设计代码

  ```python
  # 抽象产品类
  class WeChat:
  
      def send_message(self, content):
          pass
  
      def send_image(self, imageid):
          pass
  
  # 具体产品类
  class AccountA(WeChat):
  
      def send_message(self, content):
          print("使用企业微信账号A推送信息: ", content)
  
      def send_image(self, imageid):
          print("使用企业微信账号A推送图片: ", imageid)
  
  
  class AccountB(WeChat):
  
      def send_message(self, content):
          print("使用企业微信账号B推送信息: ", content)
  
      def send_image(self, imageid):
          print("使用企业微信账号B推送图片: ", imageid)
  
  # 抽象工厂类
  class WeChatFactory:
  
      def create_wechat(self):
          pass
      
  # 具体工厂类
  class AccountAFactory(WeChatFactory):
  
      def create_wechat(self):
          return AccountA()
  
  
  class AccountBFactory(WeChatFactory):
  
      def create_wechat(self):
          return AccountB()
  
  
  if __name__ == "__main__":
      # 实例化账号A
      wechat_factory_a = AccountAFactory()
      # 创建账号A的微信对象
      wechat1 = wechat_factory_a.create_wechat()
      wechat2 = wechat_factory_a.create_wechat()
      wechat3 = wechat_factory_a.create_wechat()
      # 使用账号A对象发送信息
      wechat1.send_message(content="haha")
      wechat2.send_message(content="hehe")
      wechat3.send_message(content="xixi")
  
      # 实例化账号B
      wechat_factory_b = AccountBFactory()
      # 创建账号B的微信对象
      wechat4 = wechat_factory_b.create_wechat()
      wechat5 = wechat_factory_b.create_wechat()
      # 使用账号B对象发送信息
      wechat4.send_message(content="heihei")
      wechat5.send_message(content="pupu")
  # 如果此时, 两个微信账号都不够用了, 需要增加第三个账号时, 所有的类都不需要修改, 只需创建新的类即可, 符合开放封闭原则
  class AccountC(WeChat):
  
      def send_message(self, content):
          print("使用企业微信账号C推送信息: ", content)
  
      def send_image(self, imageid):
          print("使用企业微信账号C推送图片: ", imageid)
        
      
  class AccountCFactory(WeChatFactory):
  
      def create_wechat(self):
          return AccountC()
  
  
  ```

+ 工厂方法模式的优点

  工厂方法用来创建客户所需要的产品，同时还向客户隐藏了哪种具体产品类将被实例化这一细节 能够让工厂自主确定创建何种产品对象，而如何创建这个对象的细节则完全封装在具体工厂内部 在系统中加入新产品时，完全符合开闭原则

+ 工厂方法模式的缺点

  系统中类的个数将成对增加，在一定程度上增加了系统的复杂度，会给系统带来一些额外的开销 增加了系统的抽象性和理解难度

+ 参考资料

  + [Python语言实现工厂方法设计模式](https://www.jianshu.com/p/4e49f326169e)
+ [python工厂方法模式](https://www.cnblogs.com/welan/p/9126922.html)
  + [[python 设计模式之工厂模式 Factory Pattern (简单工厂模式，工厂方法模式，抽象工厂模式)](https://www.cnblogs.com/baxianhua/p/11648485.html)](https://www.cnblogs.com/baxianhua/p/11648485.html)

  ### 抽象工厂模式

+ 定义

  + 提供一个创建一系列相关或相互依赖对象的接口，而无需指定它们具体的类。

  + 对于同一系列的集中式生产，对于不同系列的分散式生产。
  + 抽象工厂模式是所有形式的工厂模式中最为抽象和最具一般性的一种形式。当系统所提供的工厂生产的具体产品并不是一个简单的对象，而是多个==位于不同产品等级结构、属于不同类型的具体产品==时就可以使用抽象工厂模式 ，==抽象工厂模式中的具体工厂不只是创建一种产品，它负责创建一族产品== 当一个工厂等级结构可以创建出分属于不同产品等级结构的一个产品族中的所有对象时，抽象工厂模式比工厂方法模式更为简单、更有效率

+ 设计代码

  ```python
  # ------1、第一步，先定义形状类这个系列------
  import math
   
  #定义一个“形状”的接口，里面定义一个面积的接口方法，只有方法的定义，并没有实现体
  class IShape(object): 
      def Area(self):
          pass
   
  #定义4个图形类，都是实现Ishape接口，并且每一个图形都有一个可以计算面积的方法，相当于重写接口方法
  class Circle(IShape):
      def Area(self,radius):
          return math.pow(radius,2)*math.pi
   
  class Rectangle(IShape):
      def Area(self,longth,width):
          return 2*longth*width
   
  class Triangle(IShape):
      def Area(self,baselong,height):
          return baselong*height/2
   
  class Ellipse(IShape):
      def Area(self,long_a,short_b):
          return long_a*short_b*math.pi
  # ------2、第二步，再定义颜色类这个系列------
  #定义一个“颜色”的接口，里面定义一个颜色名称的接口方法，只有方法的定义，并没有实现体
  class IColor(object): 
      def color(self):
          pass
   
  #定义3个颜色类，都是实现IColor接口，并且每一个图形都有一个可以获取颜色名称的方法，相当于重写接口方法
  class Red(IColor):
      def color(self,name):
          print(f'我的颜色是：{name}')
   
  class Blue(IColor):
      def color(self,name):
          print(f'我的颜色是：{name}')
   
  class Black(IColor):
      def color(self,name):
          print(f'我的颜色是：{name}')
  # ------3、第三步，定义抽象工厂以及与每一个系列对应的工厂------
  class IFactory:  #模拟接口
      def create_shape(self):  #定义接口的方法，只提供方法的声明，不提供方法的具体实现
          pass
      def create_color(self):
          pass
   
  #创建形状这一个系列的工厂
  class ShapeFactory(IFactory): #模拟类型实现某一个接口，实际上是类的继承
      def create_shape(self, name):  #重写接口中的方法
          if name =='Circle':
              return Circle()
          elif name == 'Rectangle':
              return Rectangle()
          elif name == 'Triangle':
              return Triangle()
          elif name == 'Ellipse':
              return Ellipse()
          else:
              return None
   
  #创建颜色这一个系列的工厂
  class ColorFactory(IFactory): #模拟类型实现某一个接口，实际上是类的继承
      def create_color(self, name):  #重写接口中的方法
          if name =='Red':
              return Red()
          elif name =='Blue':
              return Blue()
          elif name =='Black':
              return Black()
          else:
              return None
  # ------4、第四步，定义产生工厂类的类——抽象工厂模式的核心所在------
  #定义一个专门产生工厂的类
  class FactoryProducer:
      def get_factory(self,name):
          if name=='Shape':
              return ShapeFactory()
          elif name=='Color':
              return ColorFactory()
          else:
              return None
   if __name__=='__main__':
      factory_producer=FactoryProducer()
      shape_factory=factory_producer.get_factory('Shape')
      color_factory=factory_producer.get_factory('Color')
      #--------------------------------------------------------------
   
      circle=shape_factory.create_shape('Circle')
      circle_area=circle.Area(2)
      print(f'这是一个圆，它的面积是：{circle_area}')
   
      rectangle=shape_factory.create_shape('Rectangle')
      rectangle_area=rectangle.Area(2,3)
      print(f'这是一个长方形，它的面积是：{rectangle_area}')
   
      triangle=shape_factory.create_shape('Triangle')
      triangle_area=triangle.Area(2,3)
      print(f'这是一个三角形，它的面积是：{triangle_area}')
   
      ellipse=shape_factory.create_shape('Ellipse')
      ellipse_area=ellipse.Area(3,2)
      print(f'这是一个椭圆，它的面积是：{ellipse_area}')
   
      #---------------------------------------------------------------
      red=color_factory.create_color('Red')
      red.color('红色')
   
      blue=color_factory.create_color('Blue')
      blue.color('蓝色')
   
      black=color_factory.create_color('Black')
      black.color('黑色')
  
  ```

+ 抽象工厂模式的优点
  + 对于出现多系列的类型，创建逻辑清楚，因为是分门别类进行创建的；结合了“简单工厂模式”和“工厂方法模式”的优点
  + 易于交换“产品系列”，只要更改相应的工厂即可。

+ 抽象工厂模式的缺点
  + 建立产品的时候很繁琐，需要增加和修改很多东西。
    + 需要再定义一个抽象接口，然后定义很多的类去实现这个接口
    + 需要在抽象工厂接口中添加一个方法，并且还需要实现一个工厂类
    + 需要更改超级工厂Factory Producer类

+ 参考资料

  + [一文详解“抽象工厂模式”以及python语言的实现](https://blog.csdn.net/qq_27825451/article/details/84284681)

  

  





