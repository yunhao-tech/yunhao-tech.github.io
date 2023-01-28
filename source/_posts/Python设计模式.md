---
title: Python 设计模式
tags:
  - Python
  - 设计模式
categories: Python 深耕
cover: https://s2.loli.net/2023/01/29/W1fYS8Dx9yqghcA.png
abbrlink: 47d2101d
date: 2023-01-28 20:15:36
description: 设计模式是一种编程思路，了解一些设计模式的知识，有助于写出更优雅的代码，增强可维护性。另外还可以在阅读大的开源项目源码时，更好地梳理开发者的思路。
---
# 开篇总领

设计模式是一种编程思路，对于面向对象的语言，比如 Java, Python 是通用的。但在具体实现中，由于各个语言的特性不同，某些设计模式可能不再需要了。

一般只有在大框架中，设计模式才能更好地发挥作用。平时交个Python作业，设计模式可能用武之地不大，但了解一些设计模式的知识，还是有助于写出比较优雅的代码，增强可维护性。另外，了解设计模式的知识，可以在阅读大的开源项目源码时，更好地梳理开发者的思路。

最后谨记，了解这些只是第一步，接下来要在实践中不断应用，才能烂熟于心。

> 本文中的代码例子来自 [Python 常用设计模式](https://www.bilibili.com/video/BV19541167cn?p=1)

---

# SOLID原则

SOLID（单一功能、开闭原则、里氏替换、接口隔离以及依赖反转）是在21世纪早期引入的程序设计原则，它加强了程序的**易维护性和可扩展性**。

- **单一职责原则**

一个类应该只负责一项职责——应该只存在一个使得类变更的原因。（甲类负责两个不同的职责：职责A和B。当职责A需求发生改变而需要修改甲类时，可能导致原本运行正常的职责B发生故障。这种情况说明职责A和B被耦合在了一起，是一种糟糕的设计）

- **开放封闭原则**

类、模块和函数等应该对扩展开放，对修改关闭（open to extension, close to modification）。即，应该在尽量不修改原代码的前提下进行扩展。

- **里氏替换原则**

一个对象在其出现的任何地方，都可以用其子类实例做替换，并且不会导致程序的错误。很容易理解，一个子类的实例，必然也是它父类的实例。

- **依赖倒置原则**

高层模块不应该依赖底层模块（底层实现算法的修改，不应该影响高层模块的调用），二者都应该依赖抽象。抽象不应该依赖细节，细节应该依赖抽象。要针对接口编程，而不是针对实现细节编程。

这里的“抽象”指 Python 的抽象类，它负责定义接口——即子类应该实现哪些函数，参数形式是什么。对应着Java中的 Interface.

- **接口隔离原则**

使用多个专门的接口，而不使用单一的总接口，子类不应该被强迫实现一些他们不会使用的接口。子类可以通过多继承的方式实现多个细分的接口。

---

# 创建型模式

创建型模式是一些创建对象时采用的模式。主要有简单工厂模式、工厂模式、抽象工厂模式、建造者模式、单例模式等。通常情况下，设计从简单工厂模式或工厂模式开始；如果发现需要更多的灵活性和更复杂的应用场景，再考虑抽象工厂模式、建造者模式。

单例模式是为了保证一个类只有一个实例，可以通过重写 `__new__` 方法实现，这里不展开。可参考视频：[Python常用设计模式——单例模式](https://www.bilibili.com/video/BV19541167cn/?p=7)

## 简单工厂模式

思路：不直接向调用者暴露对象创建的细节，而是通过一个工厂类来负责创建产品类的实例。

简单工程模式的角色有：工厂角色、抽象产品角色、具体产品角色。

首先定义一个抽象类，规定所有的接口：

```python
from abc import ABCMeta, abstractmethod

# 抽象类（目标接口）————抽象产品角色
class Payment(metaclass=ABCMeta):
    @abstractmethod
    def pay(self, money):
        pass
```

具体产品继承这个抽象类，实现接口：

```python
# 具体产品角色
class Alipay(Payment):
    def pay(self, money):
        print("支付宝余额支付了{0}元!".format(money))

# 具体产品角色
class WechatPay(Payment):
    def pay(self, money):
        print("微信支付了%d元!" % (money))
```

接下来定义工厂类，隐藏对象创建的细节：

```python
# 工厂角色
class PaymentFactory:
    def create_payment(self, method):
        if method == 'Alipay':
            return Alipay()
        elif method == 'WechatPay':
            return WechatPay()
        else:
            raise TypeError('No such payment named %s' % method)

# 客户端调用。不直接向客户端暴露对象创建的实现细节，而是通过一个工厂类来负责创建产品类的实例
pf = PaymentFactory()
p = pf.create_payment('Alipay')
p.pay(100)
```

简单工厂模式只创建一个工厂类，当有新的产品时，需要修改工厂类代码。这违反了“单一职责原则”。

## 工厂模式

工厂模式思路：定义一个抽象的工厂类，让它的子类决定去实例化哪一个产品类。角色有抽象工厂角色、具体工厂角色、抽象产品角色和具体产品角色。

这样的话，每个具体产品对应一个具体的工厂类，遵循了单一职责原则。但是每增加一个具体产品类，就必须增加一个相应的具体工厂类，代码会变得很庞大。

还是上面的例子。这次先定义一个抽象工厂类，规定具体工厂类的接口：

```python
# 抽象工厂角色
class PaymentFactory(metaclass=ABCMeta):
    @abstractmethod
    def create_payment(self):
        pass
```

然后为每一个产品实现一个具体工厂：

```python
# 具体工厂角色
class AlipayFactory(PaymentFactory):
    def create_payment(self):
        return Alipay()

class WechatPayFactory(PaymentFactory):
    def create_payment(self):
        return WechatPay()
```

调用：

```python
p = WechatPayFactory().create_payment()
p.pay(100)
```

## 抽象工厂模式 & 建造者模式

相比工厂方法模式，抽象工厂模式中的每一个具体工厂都生产一套产品。注重产品的组装，考虑了不同产品之间的约束关系。

建造者模式则着重考虑产品的组装顺序，对构造过程有着更精细的控制。

例子可以参考这个 Github 上的笔记：[创建型模式.md](https://github.com/ThanlonSmith/design-pattern/blob/master/3.%E5%88%9B%E5%BB%BA%E5%9E%8B%E6%A8%A1%E5%BC%8F.md)

---

# **结构型模式**

结构型模式主要研究几个类之间如何协同使用。其中适配器模式和代理模式一般应用在和其他项目对接过程中，不方便更改原代码，而进行的打补丁操作。

## **适配器模式**

这个模式不是用在项目设计阶段的，而是用来整合多个项目。目标是使得原本由于接口不兼容而不能一起工作的那些类可以一起工作。实现适配器的两种方式，**类适配器使用多继承，对象适配器使用组合**（组合就是把一个类的对象作为属性放入另一类中）。

假如现在有一些类已经继承了目标接口。

```python
from abc import ABCMeta, abstractmethod

# 支付功能的抽象类（目标接口）
class Payment(object, metaclass=ABCMeta):
    @abstractmethod
    def pay(self, money):
        pass

# 已经继承了目标接口的类
class Alipay(Payment):
    def pay(self, money):
        print('支付宝支付%d元' % money)
```

但有其他项目中有一些类的接口不一致——`cost` 与 `pay` 不一致。（实际场景中，不光函数名不一样，参数个数、顺序、返回值类型可能都不一致）

```python
# 其他项目中接口不一致的类
class BankPay():
    def cost(self, money):
        print('银联支付%d元' % money)
```

这个时候可以使用类适配器，**利用多继承，创建一个新的类，统一接口**：

```python
# 类适配器
class PaymentAdapter(Payment, BankPay):
#### 统一接口 ####
    def pay(self, money):
        self.cost(money)

# 调用
p = PaymentAdapter()
p.pay(100)
```

还可以使用对象适配器，**利用组合统一接口**：

```python
# 对象适配器
class PaymentAdapter(Payment):
    def __init__(self, payment):
        self.payment = payment

    def pay(self, money):
        self.payment.cost(money)
```

总结：如果只需要适配一个类的接口，那么类适配器就可以。如果有多个待适配的类，如果用类适配器，需要对每一个待适配的类创建一个新的类；这个时候用对象适配器比较方便。

## 代理模式

为其它对象提供一种代理以控制对这个对象的访问，相当于在访问对象之前加了一层关卡。

角色有抽象实体、实体和代理。实体和代理都要继承抽象实体，以对外界实现同样的调用接口。可以通过代理控制访问权限、优化流程等。

```python
from abc import ABCMeta, abstractmethod

# 抽象实体
class Subject(metaclass=ABCMeta):
    @abstractmethod
    def get_content(self):
        pass

    @abstractmethod
    def set_content(self, content):
        pass

# 实体
class RealSubject(Subject):
    def __init__(self, filename):
        self.filename = filename
        print('读取文件内容！')
        with open(self.filename, 'r', encoding='utf-8') as f:
            self.content = f.read()

    def get_content(self):
        return self.content

    def set_content(self, content):
        with open(self.filename, 'w', encoding='utf-8') as f:
            f.write(content)
```

每次创建 `RealSubject` 的时候就会读取文件。如果文件特别大，我们希望只在调用 `get_content` 方法时再读取文件该怎么办？（当然可以直接修改 RealSubject 的代码，但这里考虑的是无权做这样的修改）可以使用一个虚代理，对 RealSubject 的功能进行“重组”：

```python
# 代理
class VirtualProxy(Subject):
    def __init__(self, filename):
        self.filename = filename
        self.subj = None

    def get_content(self):
        if not self.subj:
            self.subj = RealSubject(self.filename)
        return self.subj.get_content()

    def set_content(self, content):
        if not self.subj:
            self.subj = RealSubject(self.filename)
        return self.subj.set_content(content)
```

虚代理中，只有在调用 `get_content` 方法时，才会真正读取文件，节省了内存的开销。这就好比手机开流量节省模式，不自动加载图片，除非收到明确加载的指令。

除此之外，当然也可以控制访问权限：

```python
# 代理
class ProtectedSubject(Subject):
    def __init__(self, filename):
        self.subj = RealSubject(filename)

    def get_content(self):
        return self.subj.get_content()

    def set_content(self, content):
#### 检查权限
        raise PermissionError('无写入权限！')
```

看下来，代理模式有点类似于装饰器。只不过装饰器一般用来加强功能，而代理模式一般用来保持接口不变的前提下，“重组”现有的功能，以优化流程或进行控制。

## 桥模式

桥模式是将一个事物的两个属性分离，使其都可以独立地变化、方便地扩展。它使用松耦合的方式绑定两个属性。

与之相对的，为每个可能的属性组合定义一个专门的类，是紧耦合。这种方式不可取，可扩展性极差。比如这个例子，有形状、颜色两个维度的属性。如何定义具有这两个属性的物体呢？——为每一种形状、每一种颜色的物体定义一个类：

```python
class Shape:
	pass

class Rectangle(Shape):
	pass

class Circle(Shape):
	pass

# 紧耦合。
class RedRectangle(Rectangle):
	pass

class RedCircle(Circle):
	pass

class GreenCircle(Circle):
	pass
```

如果要增加颜色或形状，那么类的数量会指数级增加。

我们来看看松耦合的一种实现思路：把继承抽象类 `Color` 的类（如 `Red`, `Green`）的实例传入抽象类 `Shape`；继承 `Shape` 的子类就有了颜色的实例，可以访问它的属性和方法。

```python
from abc import ABCMeta, abstractmethod

# 抽象
class Shape(metaclass=ABCMeta):
    def __init__(self, color):
        self.color = color

    @abstractmethod
    def draw(self):
        pass

# 实现
class Color(metaclass=ABCMeta):
    @abstractmethod
    def paint(self, shape):
        pass

# 细化抽象
class Rectangle(Shape):
    name = '长方形'

    def draw(self):
        self.color.paint(self)

# 如果要扩展形状，只需要添加形状类
class Circle(Shape):
    name = '圆形'

    def draw(self):
        self.color.paint(self)

# 细化实现
class Red(Color):
    def paint(self, shape):
        print('画红色的%s' % shape.name)

# 如果要扩展颜色，只需要添加颜色类
class Green(Color):
    def paint(self, shape):
        print('画绿色的%s' % shape.name)

rectangle = Rectangle(Red())
rectangle.draw() ### 画红色的长方形
circle = Circle(Green())
circle.draw() ### 画绿色的圆形
```

桥模式的松耦合，用到了“组合”（把一个类的对象作为属性放入另一类中）。

## 外观模式

外观模式把子系统中的一组接口封装起来，提供一个高层的接口，方便子系统的调用。减少系统相互依赖，提高灵活性，提高了安全性。

```python
# 子系统类
class CPU:
    def run(self):
        print('CPU start to run...')

    def stop(self):
        print('CPU stop to run...')

# 子系统类
class Disk:
    def run(self):
        print('Disk start to run...')

    def stop(self):
        print('Disk stop to run...')

# 外观
class Computer():
    def __init__(self):
        self.CPU = CPU()
        self.Disc = Disk()

    def run(self):
        self.CPU.run()
        self.Disc.run()

    def stop(self):
        self.CPU.stop()
        self.Disc.stop()

# 客户端，高层代码
c = Computer()
c.run()
c.stop()
```

外观模式的核心思想就是封装。

## 组合模式

将对象组合成树形结构以表示“部分-整体”的层次结构，使得用户对单个对象和组合对象的使用具有一致性。

看一个例子：做PPT的时候，需要把多个图形组合成一个图形。这个组合图形（组合对象）的操作与单个图形（叶对象）的操作是一样的。组合图形与单个图形、组合图形之间都可以继续组合。

组合模式如何应用？先定义一个抽象组件。后续的叶对象、组合对象都是它的子类。

```python
from abc import ABCMeta, abstractmethod

# 抽象组件
class Graphic(metaclass=ABCMeta):
    @abstractmethod
    def draw(self):
        pass
```

然后可以定义一些叶子类：

```python
# 叶子组件
class Point(Graphic):
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __str__(self):
        return '点(%s,%s)' % (self.x, self.y)

    def draw(self):
        print(self)

# 叶子组件
class Line(Graphic):
    def __init__(self, p1, p2):
        self.p1 = p1
        self.p2 = p2

    def __str__(self):
        return '线段[(%s,%s)]' % (self.p1, self.p2)

    def draw(self):
        print(self)
```

以及组合类：

```python
# 复合组件
class Picture(Graphic):
    def __init__(self, iterable):
#### 用可遍历的容器初始化，容器中元素是抽象组件 Graphic 的子类实例
        self.children = []
        for g in iterable:
            self.add(g)

    def add(self, graphic):
        self.children.append(graphic)

    def draw(self):
#### 递归地调用它的 children
        for g in self.children:
            g.draw()
```

客户端调用：

```python
p = Point(1, 2)
l1 = Line(Point(1, 2), Point(3, 4))

# 复合图形
pic = Picture([p, l1])
pic2 = Picture([pic, l2])
pic2.draw()
```

组合模式的核心思路：利用多态，让组合对象和叶对象的对外接口保存一致。表示出 “部分-整体” 的层次结构，生成树状结构。调用顶层对象时，会递归地遍历其下的叶对象执行。

---

# 行为型模式

## 责任链模式

多个对象都有机会处理请求时，将这些对象连成一条链并沿着这条链传递该请求，直到有一个对象处理它为止；从而避免请求的发送者和接收者之间的耦合关系。

责任链的角色有抽象处理者、具体处理者和客户端。举个例子，有一个请假的请求，不同级别的主管有不同的批准权限；要逐级地向上传递该请求，直到有人能处理为止。

先定义一个抽象的处理者：

```python
from abc import ABCMeta, abstractmethod

# 抽象的处理者
class Handler(metaclass=ABCMeta):
    @abstractmethod
    def handle_leave(self, day):
        pass
```

再来定义项目主管、部门经理、总经理三个不同级别的具体处理者：

```python
# 具体的处理者
class GeneralManager(Handler):
    def handle_leave(self, day):
        if day <= 30:
            print('总经理准假%d' % day)
        else:
            print('可以辞职了！')

# 具体的处理者
class DepartmentManager(Handler):
    def __init__(self):
        self.next = GeneralManager()

    def handle_leave(self, day):
        if day <= 7:
            print('项目主管准假%d' % day)
        else:
            print('部门经理职权不足')
            self.next.handle_leave(day)

# 具体的处理者
class ProjectDirector(Handler):
    def __init__(self):
        self.next = DepartmentManager()

    def handle_leave(self, day):
        if day <= 3:
            print('项目主管准假%d' % day)
        else:
            print('项目主管职权不足')
            self.next.handle_leave(day)
```

可以看到，级别低的对象有一个 `next` 属性， 存储一个更高级别的对象。当前对象权限不足以处理该请求时，它会调用 `next` 对象的处理方法，直到该请求被处理，或者到达最高级别对象，在该例子中就是总经理。

高层代码调用时，只需要对最低级别的对象发出请求即可：

```python
day = 20
p = ProjectDirector()
p.handle_leave(day)
```

责任链模式使用场景：有多个对象可以处理一个请求，而具体哪个对象处理则在运行时决定。优点是降低了耦合度，无需知道是哪一个对象处理了请求。

## 观察者模式

观察者模式应用非常广泛，又被称为“发布-订阅”模式。它用来定义对象间**一对多的依赖关系：**当一个对象的状态发生变化时，所有依赖它的对象都得到通知并被自动更新。

观察者模式的角色有：抽象主题、具体主题（发布者）、抽象观察者和具体观察者（订阅者）。这里举一个公司向员工发布公告的例子：

```python
from abc import ABCMeta, abstractmethod

# 抽象的订阅者
class Observer(metaclass=ABCMeta):
    @abstractmethod
    def update(self, notice):
        pass

# 抽象的发布者。这里不是一个抽象类
class Notice:
    def __init__(self):
        self.observers = []

    def attach(self, obs):
        self.observers.append(obs)

    def detach(self, obs):
        self.observers.remove(obs)

    def notify(self):
        for obs in self.observers:
            obs.update(self)
```

然后定义公司是一个具体的发布者；员工是一个具体的观察者。

```python
# 具体的发布者
class StaffNotice(Notice):
    def __init__(self, company_info):
        super().__init__()  # 调用父类对象声明observers属性
        self.__company_info = company_info

    @property
    def company_info(self):
        return self.__company_info

    @company_info.setter
    def company_info(self, info):
        self.__company_info = info
        self.notify()

# 具体的订阅者
class Staff(Observer):
    def __init__(self):
        self.company_info = None

    def update(self, notice):
        self.company_info = notice.company_info
```

这里的关键在于，在 `StaffNotice` 里面定义了私有属性 `company_info`。在这个私有属性的 `setter` 方法里，调用了 `self.notity()`。也就是说，**每当信息被更新时，都会自动推送给订阅者**。而订阅者可以通过一个列表维护，方便添加和移除。

调用：

```python
staff_notice = StaffNotice('初始化公司信息')
staff1 = Staff()
staff2 = Staff()
staff_notice.attach(staff1)
staff_notice.attach(staff2)
staff_notice.company_info = '假期放假通知！'
#### 只要staff_notice更新了信息，列表中的staff1 和staff2 会自动接受到 ####
print(staff1.company_info)
```

使用场景就想象订阅微信公众号就行了：一个对象的改变需要同时改变其它对象，但事先不知道具体有多少对象待改变。换言之，我们不希望这些对象是紧耦合的，可以灵活地订阅和取关。优点：发布者与观察者之间的耦合最小；支持广播通信。

## 模板方法模式

定义一个算法骨架，将一些具体实现步骤延迟到子类中。模板方法使得子类可以不改变一个算法的结构即可重定义该算法的某些特定步骤。

```python
from abc import ABCMeta, abstractmethod
from time import sleep

# 抽象类
class Window(metaclass=ABCMeta):
    @abstractmethod
    def start(self):  # 原子操作/钩子操作
        pass

    @abstractmethod
    def repaint(self):  # 原子操作/钩子操作
        pass

    @abstractmethod
    def stop(self):  # 原子操作/钩子操作
        pass

#### 模板方法，算法的抽象框架 ####
    def run(self):
        self.start()
        while True:
            try:
                self.repaint()
                sleep(1)
            except KeyboardInterrupt:
                break
        self.stop()
```

`run`提供了算法的抽象框架。子类只需要实现那些“原子操作”即可：

```python
# 具体类
class MyWindow(Window):
    def __init__(self, msg):
        self.msg = msg

    def start(self):
        print('窗口开始运行！')

    def stop(self):
        print('窗口停止运行！')

    def repaint(self):
        print(self.msg)

MyWindow("Hello...").run()

```

适用场景：一次性实现一个算法的不变部分，各个子类中的公共行为被提取出来并集中到一个公共父类中以避免代码重复。也可以控制子类的扩展，统一接口。

---

所谓“师傅领进门，修行在个人”。现在感觉像是一名学过剑术的入门弟子，但毫无实战经验。此时距离一名侠客还有很远，唯有在江湖漂泊中身经百战，不断领悟剑术中的奥妙，才能人剑合一。