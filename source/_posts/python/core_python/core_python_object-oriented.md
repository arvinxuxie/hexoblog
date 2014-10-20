---
layout: post
title: Object-Oriented Programming
category: python
tags: [core_python]
---
<!--more-->
## Introduction
类与实例相互联系：类是对象的定义，而实例是“真正的实物”,它存放了类中所定义的对象的具体信息。所有新式类必须继承至少一个父类，参数`bases`可以是一个(单继承`single inheritance`)或多个(多重继承`multiple inheritance`)的父类。
```
class MyNewObjectType(bases):
    ‘define MyNewObjectType class’
    class_suite # 类体
```
`object`是“所有类之母`mother of all classes`”。

类可以很简单，也可以很复杂，最简单的情况类仅用作名称空间`namespace`。
```
In [228]: class MyData(object):
   .....:     pass
In [229]: mathObj = MyData()
In [230]: mathObj.x = 4
In [231]: mathObj.y = 5
In [232]: mathObj.x + mathObj.y
Out[232]: 9
In [233]: mathObj.x * mathObj.y
Out[233]: 20
```
本例中，实例名字`mathObj`将`mathObj.x`和`mathObj.y`关联起来，就是我们所说的使用类作为名字空间容器。`mathObj.x`和`mathObj.y`是实例的属性，这些实例属性是动态的。

**方法**`Methods`:调用一个方法的途径1.定义类。 2.创建一个势力。 3.用这个实例调用方法。
```
class MyDataWithMethod(object):    #define the class
    def printFoo(self):          #define the method
        print ‘You invoked printFoo()!’
```
注意`self`参数，它在所有的方法声明中都存在，这个参数代表实例对象本身，当你用实例调用方法时，由解释器悄悄地传递给方法的，所以不需要自己传递`self`进来，因为它是自动传入的，Python把`self`作为第一个参数传递进来。
Python的哲学本质上就是要明白清晰，一般的方法会需要这个实例(self),而静态方法或类方法不会。

对于已熟悉的面向对象编程的人来说`__init__()`类似于类构造器`class constructor`，如果你初涉OOP世界，可以认为一个构造器仅是一个特殊的方法，它在创建一个新的对象时被调用。在Python中，`__init__()`实际上不是一个构造器，你没有调用`new`来创建一个新对象。(Python根本就没有`new`关键字)。取而代之，Python创建实例后，在实例化过程中，调用`__init__()`方法，当一个类被实例化时，就可以定义额外的行为，比如，设定初始值或者运行一些初步诊断代码--主要是在实例被创建后，实例化调用返回这个实例之前，去执行某些特定的任务或设置。
(我们将把print语句添加到方法中，这样我们就清楚什么时候方法被调用了，通常，我们不把输入输出语句放入函数中，除非预期代码具有输出的特性)
```
In [236]: class AddrBookEntry(object):     #定义类
   .....:     'address book entry class'
   .....:     def __init__(self, nm, ph):    # 定义构造器
   .....:         self.name = nm            #设置name
   .....:         self.phone = ph           #设置phone
   .....:         print 'Created instance for:', self.name
   .....:     def updatePhone(self, newph):      #定义方法
   .....:         self.phone = newph
   .....:         print 'Updated phone# for:', self.name
```
在Addrbookentry类的定义中，定义类两个方法：`__init__()`和`updatePhone()`。`__init__()`在实例化时被调用，即，在`AddrBookEntry()`被调用。可以认为实例化是对`__init__()`的一种隐式的调用。
```
In [237]: john = AddrBookEntry('John Doe', '408-555-1212')
Created instance for: John Doe
In [238]: jane = AddrBookEntry('Jane Doe', '650-555-1212')
Created instance for: Jane Doe
```
一旦实例被创建后，就可以证实一下，在实例化过程中，我们的实例属性是否确实被`__init__()`设置了，我们可以通过解释器“转储`Dumping`”实例来查看它是什么类型的对象。(我们以后将学到如何定制类来获得想要的Python对象字符串的输出形式，而不是现在看到的默认的Python对象字符串(<...>))

**创建子类**`Creating a Subclass`
我们创建了第一个子类，Python中，当一个类被派生出来，子类继承了基类的属性，所以在上面的类中，我们不仅定义了`__init__()`,`updateEmail()`方法，而且EmplAddrBookEntry还从AddrBookEntry中继承了`updatePhone()`方法。

如果需要，每个子类最好定义自己的构造器，不然，基类的构造器会被调用。然而，如果子类重写基类的构造器，基类的构造器就不会被自动调用了--这样，基类的构造器就必须显式写出才会被执行。注意，这里我们要显式传递`self`实例对象给基类构造器，因为我们不是在其实例中调用那个方法而是在一个子类实例中调用那个方法。因为我们不是通过实例来调用它，这种未绑定的方法调用需要传递一个适当的实例(self)给方法。
```
In [240]: class EmplAddrBookEntry(AddrBookEntry):
   .....:     'Employee Address Book Entry class'
   .....:     def __init__(self, nm, ph, id, em):
   .....:         AddrBookEntry.__init__(self, nm, ph)
   .....:         self.empid = id
   .....:         self.email = em
   .....:     def updateEmaill(self, newem):
   .....:         self.email = newem
   .....:         print 'Update e-mail address for:', self.name

In [242]: john = EmplAddrBookEntry('John Doe', '408-555-1212',42,'john@spam.doe')
Created instance for: John Doe
In [243]: john
Out[243]: <__main__.EmplAddrBookEntry at 0x3eb8990>
```
**核心笔记**`Core Style`: 命名类、属性和方法`naming classes, attributes, and methods`
类名通常由大写字母开头。数据属性`data attributes`听起来应当是数值的名字，方法名应当指出对应对象或值的行为。另一种表达方式是:数据值应该使用名词作为名字，方法使用谓词(动词加对象)`Use nouns for data value names and predicates(verbs plus direct objects) for methods`。数值型像“name”,”phone”和“email”, 行为如“updatePhone”,”updateEmail”。这就是常说的“混合记法`mixedCase`”或“骆驼记法`camelCase`”。


## 面向对象的编程`Object-Oriented Programming`
### 常用术语`Buzzword-Compliance`
**抽象/实现**`Abstraction/Implementation`
抽象指对现实世界问题和实体的本质表现，行为和特征建模，建立一个相关的子集，可以用于描绘程序结构，从而实现这种模型。抽象不仅包括这种模型的数据属性，还定义了这些数据的接口。对某种抽象的实现就是对此数据及与之相关接口的现实化。现实化这个过程对于客户程序应当是透明而且无关的。
*Abstraction* refers to the modeling of essential aspects, behavior, and characteristics of real-world problems and entities, providing a relevant subset as the definition of a programmatic structure that can realize such models. Abstraction not only contain the data attributes of such a model, but also define interfaces with that data. An implementation of such an abstraction is the realization of that data and the interfaces that go along with it. Such a realization should remain hidden from and irrelevant to the client programmer.

**封装/接口**`Encapsulation/Interfaces`
封装描述了对数据/信息进行隐藏的观念，它对数据属性提供接口和访问函数。通过任何客户端直接对数据的访问，无视接口，与封装性都是背道而驰的，除非程序员允许这些操作。作为实现的一部分，客户端根本就不需要知道在封装之后，数据属性是如何组织的。在Python中，所有的类属性都是公开的，但名字可能被“混淆”了，以阻止未经授权的访问，但仅此而已，再没有其他预防措施了。这就需要在设计时，对数据提供相应的接口，以免客户程序通过不规范操作来存取封装的数据属性。
*Encapsulation* describes the concept of data/information hiding and providing *interfaces* or accessor functions to the data attributes. Direct access to data by any client, bypassing the interfaces, goes against the principles of encapsulation, but the programmer is free to allow  such access. As parth of the implementation, the client should not even know how the data attributes are architected within the abstraction. In Python, all class attributes are public, but names may be “mangled” to discourage unauthorized access, but otherwise not prevented. It is up to the designer to provide the appropriate interfaces to the data so that the client programmer does not have to resort to manipulating the encapsulated data attributes.

**合成**`Composition`
合成扩充了对类的描述，是的多个不同的类合成为一个大的类，来解决现实问题。合成描述了一个异常复杂的系统，比如一个类由其它类组成，更小的组件也可能是其它的类，数据属性及行为，所有这些合在一起，彼此是“有一个”的关系。
*Composition* extends out description of classes, enabling multiple yet distinct classes to be combined into a larger entity to solve a real-world problem. Composition describes a singular, complex system such as a class made up of other, smaller components such as other classes, data attributes, and behaviors, all of which are combined, embodying “has-a” relationships.

**派生/继承/继承结构**`Derivation/Inheritance/Hierarchy`
派生描述了子类的创建，新类保留已存在类类型中所有需要的数据和行为，但允许修改或者其它的自定义操作，都不会修改原来类的定义。继承描述了子类属性从祖先类继承这样一种方式。从前面的例子中，技工可能比顾客多个汽车技能属性，但单独的来看，每个都“是一个”人，所以，不管对谁而言调用`talk()`都是合法的，因为它是人的所有实力共有的。继承结构表示多“代”派生，可以描述成一个“族谱”，连续的子类，与祖先类都有关系。
*Derivation* describes the creation of subclasses, new classes that retain all desired data and behavior of the existing class type but permit modification or other customization, all without having to modify the original class definition. *Interface* describes the means by which attributes of subclass are “bequeathed from” an ancestor class. From our earlier example, a Mechanic may have more car skill attributes than a Customer, but individually, each “is a” Person, so it is valid to invoke the talk() method, “generations” of derivation which can be depicted graphically as a “family tree”, with successive subclasses having relationships with ancestor classes.

**泛化/特化**`Generalization/Specialization`
泛化表示所有子类与其父类及祖先有一样的特点，所以子类可以认为同祖先类是“是一个”的关系，因为一个派生对象(实例)是祖先类的一个“例子”。比如，技工“是一个”人,车“是一个”交通工具，等等。在上面我们间接提到的族谱图中，我们可以从子类到祖先类画一条线，表示“是一个”的关系。特化描述所有子类的自定义，也就是，什么属性让它与其祖先类不同。
*Generalization* describes all the traits a subclass has with its parent and ancestor classes, so subclasses are considered to have an “is-a” relationship with ancestor classes because a derived object(instance) is an “example” of an ancestor class.For example, a Mechanic “is a” Person, a Car “is a” Vehicle, etc. In the family tree diagram we alluted to above, we can draw lines from subclasses to ancestors indicating “is a” relationships. Specialization is the term that describes all the customization of a subclass,i.e. what attributes make it differ from its ancestor classess.

**多态**`Polymorphism`
多态的概念指出了对象如何通过他们共同的属性和动作来操作及访问，而不需要考虑他们具体的类。多态表明了动态(又名，运行时)绑定的存在，允许重载及运行时类型确定和验证。
the concept of *polymorphism* describes how objects can be manipulated and accessed using attributes and behaviors they have in common without regard to their specific class. Polymorpism indicates the presence of dynamic (aka late, runtime) binding, allowing for overriding and runtime type determination and verification.

**自省/反射**`Introspection/Reflection`
自省表示给与你，程序员，某种能力来进行像“手工类型检查”的工作，它也被称为反射。这个性质展示了某对象是如何在运行期取得自身信息的。如果传一个对象给你，你可以检查出它有什么能力，这样的功能不是很好吗？这是一项强大的特性。如果Python不支持某种形式的自省功能，`dir()`和`type()`内建函数，将很难正常工作。请密切关注这些调用，还有那些特殊属性，像`__dict__, __name__`及`__doc__`。
*Introspection* is what gives you, the programmer, the ability to perform an activity such as “manual type checking”. Also called *reflection*, this property describes how information about a particular object can be accessed by itself during runtime. Would it not be great to have the ability to take an object passed to you and be able to find out what it is capable of? This is a powerful feature that you will encounter frequently in this chapter. The dir() and type() built-in functions would have a very difficult  time working if Python did not support some sort of introspection capability, Keep an eye out for these calls as well as for special attributes like `__dict__`, `__name__`and `__doc__`. You may even be familiar with some of them already!
**绑定(绑定及非绑定方法)**`Binding(Bound and Unbound Methods)`

### 类属性`Class Attributes`
类属性仅与其被定义的类相绑定，并且因为实例对象在日常OOP中用得最多，实例数据属性是你将会一直用到的主要数据属性。类数据属性仅当需要有更加“静态”数据`”static” data type`类型时才变得有用，它和任何实力都无关，(它表示一个值，不会因为函数调用完毕而消失，它在每两个函数调用的间隙都存在)

静态成员通常仅用来追踪与类相关的值，大多数情况下，你会考虑用实例属性，而不是类属性。

**绑定(绑定及非绑定方法)**`Binding(Bound and Unbound Methods)`
为与OOP惯例保持一致，Python严格要求，没有实例，方法是不能被调用的。这种限制及Python所描述的绑定概念`binding`,在此，方法必须绑定到一个实例才能直接被调用。非绑定方法可能可以被调用，但实例对象一定要明确给出，才能确保调用成功。然而，不管是否绑定，方法都是它所在的类的固有属性，即使它们几乎总是通过实例来调用。

**决定类的属性**`Determining Class Attributes`
要知道一个类有那些属性，有两种方法。`dir()`内建函数和类的字典属性`__dict__`。
```
In [244]: class MyClass(object):
   .....:     'MyClass class definition'
   .....:     myVersion = '1.1'
   .....:     def showMyVersion(self):
   .....:         print MyClass.myVersion
Out[246]:
['__class__',
 '__delattr__',
 '__dict__',
 '__doc__',
 '__format__',
 '__getattribute__',
 '__hash__',
 '__init__',
 '__module__',
 '__new__',
 '__reduce__',
 '__reduce_ex__',
 '__repr__',
 '__setattr__',
 '__sizeof__',
 '__str__',
 '__subclasshook__',
 '__weakref__',
 'myVersion',
 'showMyVersion']
In [247]: MyClass.__dict__
Out[247]:
<dictproxy {'__dict__': <attribute '__dict__' of 'MyClass' objects>,
 '__doc__': 'MyClass class definition',
 '__module__': '__main__',
 '__weakref__': <attribute '__weakref__' of 'MyClass' objects>,
 'myVersion': '1.1',
 'showMyVersion': <function __main__.showMyVersion>}>
```
**特殊的类属性**`Special Class Attributes`
`c.__name__`:    类C名字的字符串(String name of class C)
`c.__doc__`:    类C的文档字符串(Documentation string for class C)
`c.__bases__`:   类C的所有父类构成的元组(Tuple of class C’s parent classes)
`c.__dict__`:    类C的属性(Attributes of C)
`c.__module__`:   类C所定义的模块(Module where C is defined)
`c.__class__`:   实例C对应的类(Class of which C is an instance)

### 实例`Instances`
**核心笔记**`Core Note`:类和实例
类和类型在2.2版本中统一了，这使得Python的行为更像其他面向对象编程语言。任何类或者类型的实例都是这种类型的对象。比如让Python告诉你，类MyClass的实例mc是否是类MyClass的一个实例。
```
In [248]: mc = MyClass()
In [249]: type(mc)
Out[249]: __main__.MyClass
In [250]: type(0)
Out[250]: int
#仔细观察MyClass和int，两者都是类型(type)
In [251]: type(MyClass)
Out[251]: type
In [252]: type(int)
Out[252]: type
#在早于2.2版本时
In [248]: mc = MyClass()
In [249]: type(mc)
Out[249]: instances
In [250]: type(0)
Out[250]: int
In [251]: type(MyClass)
Out[251]: class
In [252]: type(int)
Out[252]: built_function_or_method
```
为了避免任何混淆，早于2.2版本你只要记住当你定义一个类时，你并没有创建一个新的类型，而是仅仅一个类对象;2.2版本后，当你定义一个(新式的)类后，你已经创建了一个新的类型。

**`__init__()`构造器方法**`Constructor Method`: 当类被调用，实例化的第一步是创建实例对象。

**`__new__()`构造器方法**:与`__init__()`的相比，`__new__()`方法更像一个真正的构造器，Python用户可以对内建类型进行派生，因此，需要一种途径来实例化不可变对象，比如，派生字符串，数字，等等。
这这种情况下，解释器则调用类的`__new__()`方法，一个静态方法，并且传入的参数是在类实例化操作时生成的，`__new__()`会调用父类的`__new__()`来创建对象(向上代理)。
为何`__new__()`比`__init__()`更像构造器呢？这是因为`__new__()`必须返回一个合法的实例，这样解释器在调用`__init__()`时，就可以把这个实例作为`self`传给它。调用父类的`__new__()`来创建对象，正像其它语言中使用`new`关键字。

**`__del__()`解构器方法**:同样，有一个相应的特殊解构器(destructor)方法名为`__del__()`。然而，由于Python具有垃圾对象回收机制(靠引用计数)，这个函数要直到该实例对象所有的引用都被清除掉后才会执行。Python中欧你的解构器是在实例释放前提供特殊处理功能的方法，它们通常没有被实现，因为实例很少被显式释放。

总结：
* 不要忘记首先调用父类的`__del__()`。
* 调用`del x`不表示调用了`x.__del__()`----前面也看到，它仅仅是减少x的引用计数
* 如果你有一个循环引用或其它的原因，让一个实例的引用逗留不去，该对象的`__del__()`可能永远不会被执行。
* `__del__()`未捕获的异常会被忽略掉(因为一些在`__del__()`用到的变量或许已经被删除了)。不要在`__del__()`中干与实例没有任何关系的事情。
* 除非你知道你在干什么，否则不要去实现`__del__()`
* 如果你定义的`__del__`，并且实例是某个循环的一部分，垃圾回收器将不会终止这个循环--你需要自己显式调用`del`。

**Core Note**: 跟踪实例`Keeping track of instances`
Python没有提供任何内部机制来跟踪一个类有多少个实例被创建了，或者记录这些实例是些什么东西。如果需要这些功能，你可以显式加入一些代码到类定义或者`__init__()`和`__del__()`中去。最好的方式是使用一个静态成员来记录实例个数。靠保存它们的引用来跟踪实例对象是很危险的，因为你必须合理管理这些引用，不然，你的引用可能没办法释放(因为还有其它的引用)。
```
In [253]: class InstCt(object):
   .....:     count = 0
   .....:     def __init__(self):
   .....:         InstCt.count += 1
   .....:     def __del__(self):
   .....:         InstCt.count -= 1
   .....:     def howMany(self):
   .....:         return InstCt.count

In [254]: a = InstCt()
In [255]: b = InstCt()
In [256]: b.howMany()
Out[256]: 2
In [257]: del b
In [258]: a.howMany()
Out[258]: 1
In [259]: del a
In [260]: InstCt.count
Out[260]: 0
```

### 实例属性`Instance Attributes`
设置势力属性可以在实例创建后任意时间进行，也可以在能够访问实例的代码中进行。构造气`__init__()`是设置这些属性的关键点之一。
**Core Note**: Instances Attributes
能够在“运行时”创建实例的属性，是Python类的优秀特性之一。一个缺陷是，属性在条件语句中创建，如果该条件语句块并未被执行，属性也就不存在，而你在后的代码中试着去访问这些属性,就会发生错误。

**在构造器中首先设置实例属性**`Constructor Fist Place to Set Instance Attribtutes`
构造器是最早可以设置实例属性的地方，因为`__init__()`是实例创建后第一个被调用的方法。在没有比这更早的可以设置实例属性的机会了。一旦`__init__()`执行完毕，返回实例对象，即完成了实例化过程。

**默认参数提供默认的实例安装**`Default Arguments Provide Default Instance Setup`
如果提供了默认值，我们就没必要显式的给构造器传值了。需要明白，默认参数应当是不变的对象，像列表和字典这样的可变对象可以扮演静态数据，然后在每个方法调用中来维护它们的内容。

```
Class definition for a fictitious hotel room rate calculator. The __init__() constructor method initializes several instance attribute attributes. A calcTotal() method id used to determine either a total daily room rate or the room cost for an entire stay.
#!/usr/bin/env python
# -*- coding: utf-8 -*-
class HotelRoomCalc(object):
    'Hotel room rate calculator'
    def __init__(self, rt, sales=0.085, rm=0.1):
        '''HotelRoomCalc default arguments:
            sales tax == 8.5% and room tax == 10%'''
        self.salesTax = sales
        self.roomTax = rm
        self.roomRate = rt

    def calcTotal(self, days=1):
        'Calculate total; default to daily rate'
        daily = round((self.roomRate*(1 + self.roomTax + self.salesTax)), 2)
        return float(days) * daily

In [268]: sfo = hotel.HotelRoomCalc(299)
In [269]: sfo.calcTotal()
Out[269]: 354.31
In [270]: sfo.calcTotal(2)
Out[270]: 708.62
```

**`__init__()`应当返回`None`**
采用函数操作符调用类对象会创建一个类实例，也就是说这样一种调用过程返回的对象就是实例，如果定义了构造器，它不应当返回任何对象，因为实例对象是自动在实例化调用后返回的。
```
In [273]: mc = MyClass()
In [274]: mc
Out[274]: <__main__.MyClass at 0x3eb88d0>
```
**查看实例属性**`Determining Instance Attributes`
`dir()`内建函数可以用于显示实例所有属性，以及所有类属性。
```
In [276]: c = C()
In [277]: c.foo = 'roger'
In [278]: c.bar = 'shrubber'
In [279]: dir(c)
Out[279]: 
['__class__', '__delattr__', '__dict__', '__doc__', '__format__', '__getattribute__', '__hash__', '__init__', '__module__',
 '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__setattr__', '__sizeof__', '__str__',
 '__subclasshook__', '__weakref__', 'bar', 'foo']

In [280]: c.__dict__
Out[280]: {'bar': 'shrubber', 'foo': 'roger'}
```
**特殊的实例属性**`Determining Instance Attributes`
实例仅有两个特殊属性:
`I.__class__`     实例化I的类
`I.__dict__`      I的属性

### 实例vs属性`Instance Attribute versus Class Attributes`
类的属性仅是与类相关的数据值并不与实例相关。这些值像静态成员那样被引用，即使在多次实例化中调用类，它们的值都保持不变。不管如何，静态成员不会因为实例而改变它们的值，除非显式的改变它们的值。还有一些方面关于类属性和实例属性需要指出，你可以采用类来访问类属性，如果实例没有同名的属性的话，你也可以用实例来访问。

**访问类属性**`Access to Class Attributes`
类属性可通过类或实例来访问，通过类访问类属性是很自然的，通过实例首先会去找实例属性，找不到在去找相应的类属性，然后在就是继承树中的基类。
```
In [303]: class C(object):     # define class
   .....:     version = 1.2    #static member
In [304]: c = C()    #instantiation
In [305]: C.version    # access via class
Out[305]: 1.2
In [306]: c.version    #access via instance
Out[306]: 1.2
In [307]: C.version += 0.1    #  update (only) via class
In [308]: C.version        # class access
Out[308]: 1.3
In [309]: c.version    #instance access , which also reflected change
Out[309]: 1.3
```
**从实例中访问类的属性需要谨慎**`Use Caution When Accessing Classes Attribute with Instance`
```
In [310]: class Foo(object):
   .....:     x = 1.5

In [311]: foo = Foo()
In [312]: foo.x
Out[312]: 1.5
In [313]: foo.x = 1.7    #try to update class attr
In [314]: foo.x
Out[314]: 1.7
In [315]: Foo.x    #nope, just created a new inst attr
Out[315]: 1.5
In [316]: del foo.x     #delete instance attribute
In [317]: foo.x    # can now access class attr again
Out[317]: 1.5

In [318]: foo.x += 0.2    #try to increment class attr
In [319]: foo.x
Out[319]: 1.7
In [320]: Foo.x     #nope, same thing
Out[320]: 1.5
```
但是这类属性可变的情况下，就不同了：
```
In [321]: class Foo(object):
   .....:     x = {2003:'poe2'}
In [322]: foo = Foo()
In [323]: foo.x
Out[323]: {2003: 'poe2'}
In [324]: foo.x[2004] = 'valid path'
In [325]: foo.x
Out[325]: {2003: 'poe2', 2004: 'valid path'}
In [326]: Foo.x           #it works !!!
Out[326]: {2003: 'poe2', 2004: 'valid path'} #no shadow so cannot delete
In [327]: del foo.x
---------------------------------------------------------------------------
AttributeError                            Traceback (most recent call last)
<ipython-input-327-e1263bf95f8f> in <module>()
```
**类属性持久性**`Class Attributes More Persistent`
静态成员，独立于实例，类属性的修改会影响到所有的实例：
```
In [328]: class C(object):
   .....:     spam = 100    #class attribute

In [329]: c1 = C()     #create an instance
In [330]: c1.spam      #access class attr thru inst
Out[330]: 100
In [331]: C.spam += 100    #update class attribute
In [332]: c1.spam         #confirm change in attribute
Out[332]: 200
In [333]: c2 = C()     #create another instance
In [334]: c2.spam
Out[334]: 200
In [335]: del c1    #remove one instance
In [336]: C.spam += 200    #update class a attribute again
In [337]: c2.spam
Out[337]: 400
```

## 绑定和方法调用`Binding and Method Invocation`
方法只有在其所属类拥有实例时，才能被调用。没有实例时方法就是未绑定的。
**Core Note**:`self`是什么
self变量用于在类实例方法中引用方法所绑定的实例。因为方法的实例在任何方法调用中总是作为第一个参数传递的，self被选中用来代表实例。你必须在方法声明中放上self，但可以在方法中不使用实例`self`。如果方法中没有用到self，那么请考虑创建一个常规函数。毕竟你的方法没有代码使用实例，没有与类关联其功能。
**调用绑定方法**`Invoking Bound Methods`:当你在实例中调用一个绑定的方法时，self不需要明确的传入了。当你还没有一个实例并且需要调用一个非绑定方法的时候你必须传递self参数。
**调用非绑定方法**`Invoking Unbound Methods`:调用非绑定方法并不经常用到，主要场景是派生一个子类，而且你要覆盖父类的方法。
```
In [338]: class EmplAddrBookEntry(AddrBookEntry):
   .....:     'Employee Address Book Entry class'
   .....:     def __init__(self, nm, ph, em):
   .....:         AddrBookEntry.__init__(self, nm, ph)
   .....:         self.empid = id
   .....:         self.email = em
```
## 静态方法和类方法`Static Methods and Class Methods`
在静态方法加入到Python之前，用户只能在全局名字空间中创建函数，作为这种特性的替代实现-有时在这样的函数中使用对象来操作类(或是类属性),使用模块函数比使用静态类方法更常见。
通常的方法需要一个实例(self)作为第一个参数，并且对于(绑定的)方法调用来说，`self`是自动传递给这个方法的。对于类方法而言，需要的不是i实例作为第一个参数，它是由解释器传给方法。类不需要特别地命名，类似`self`，不过很多人使用`cls`作为变量名字。
**`staticmethod() and classmethod() Built-in-Functions`**
```
In [350]: class TestStaticMethod:
   .....:     def foo():
   .....:         print 'calling static method foo()'
   .....:     foo = staticmethod(foo)
In [351]: class TestClassMethod:
   .....:     def foo(cls):
   .....:         print 'calling class method foo()'
   .....:         print 'foo() is part of class:', cls.__name__
   .....:     foo = classmethod(foo)
In [359]: tsm = TestStaticMethod()
In [360]: TestStaticMethod.foo()
calling  static method foo()
In [361]: tsm.foo()
calling  static method foo()
In [362]: tcm = TestClassMethod()
In [363]: TestClassMethod.foo()
calling class method foo()
foo() is part of class: TestClassMethod
In [364]: tcm.foo()
calling class method foo()
foo() is part of class: TestClassMethod
```
**使用函数修饰符**`Using Decorators`
Now, seeing code like `foo = staticmethod(foo)` can irritate some programmers(会刺激一些程序员).很多人对这样一个没有意义的语法感到心烦。通过修饰符可以用它把一个函数应用到另一个函数对象上，而且新函数对象依然绑定在原来的变量。通过使用`decorators`,我们可以避免像上面那样重新赋值：
```
In [366]: class TestStaticMethod:
   .....:     @staticmethod
   .....:     def foo():
   .....:         print 'calling static method foo()'
   .....: class TestClassMethod:
   .....:     @classmethod
   .....:     def foo(cls):
   .....:         print 'calling class method foo()'
   .....:         print 'foo() is part of class:', cls.__name__

In [367]: tsm = TestStaticMethod()
In [368]: TestStaticMethod.foo()
calling static method foo()
In [369]: tsm.foo()
calling static method foo()
In [370]: tcm = TestClassMethod()
In [371]: TestClassMethod.foo()
calling class method foo()
foo() is part of class: TestClassMethod
In [372]: tcm.foo()
calling class method foo()
foo() is part of class: TestClassMethod
```
**组合**`Composition`
一个类被定义后，目标就是要把它当成一个模板块来使用，并把这些对象嵌入到你的代码中去，同其他数据类型及逻辑执行流混合使用。第一种是组合`composition`，就是让不同的类混合并加入到其它类中，来增加功能和代码重用性。(This is where different classes are mingled with and into other classes for added functionality and code resusablity)。你可以在一个大点的类中创建你自己的类的实例，实现一些其它属性和方法来增强对原来的类的对象。另一种方法是通过派生。
让我们对地址本类的加强性设计，如果在设计过程中，为`names, addresses`等等创建了单独的类。最后我们可能想把这些工作集成到`AddrBookEntry`类中去，而不是重新设计每一个需要的类。这样就节省了时间和精力，而且最后的结果是容易维护的代码。
```
In [373]: class NewAddrBookEntry(object):
   .....:     'new address book entry class'
   .....:     def __init__(self, nm, ph):
   .....:         self.name = Name(nm)
   .....:         self.phone = Phone(ph)
   .....:         print 'Created instance for:', self.name
```
创建复合对象就可以实现这些附加的功能，并且很有意义，因为这些类都不相同。每一个类管理它们自己的名字空间和行为。不过当对象之间有更接近的关系时，派生的概念可能对你的应用程序来说更有意义，特别是当你需要一些相似的对象，但却有少许不同的功能的时候。

**子类和派生**`Subclassing and Derivation`
当类之间有显著的不同，并且较小的类是较大的类所需要的组件时，组合表现的很好，但当你设计“相同的类但有一些不同的功能”时，派生就是一个更加合理的选择。

OOP的更强大方面之一是能够使用一个已经定义好的类，扩展它或者对其进行修改，而不会影响系统中使用现存类的其它片段。OOD允许类特征在子孙类或子类中进行继承。这些子类从基类(或称祖先类，超类)`descendant classes or subclasses`继承它们的核心属性。而且，这些派生可能会扩展到多代。在一个层次的派生关系中的相关类(或者是在类树图中垂直相邻)是父类和子类关系。从同一个父类派生出来的这些类(或者是在类树图中水平相邻)是同胞关系。父类和所有高层类都被认为是祖先。

**创建子类**`Creating Subclasses`
创建子类的语法看起来与普通类没有区别，一个类名，后跟一个或多个需要从其中派生的父类：
```
class SubClassName(ParentClass[,ParentClass2,...]):
    ‘optional class documentation string’
    class_suite
```
### 继承`Inheritance`
继承描述了基类的属性如何“遗传”给派生类。一个子类可以继承它的基类的任何属性，不管是数据属性还是方法。
```
In [375]: class P(object):    # parent class
   .....:     pass
   .....: class C(P):     #child class
   .....:     pass

In [376]: c = C()     #instantiation child
In [377]: c.__class__    #child “is a” parent
Out[377]: __main__.C
In [378]: C.__bases__     # child’s parent class(es)
Out[378]: (__main__.P,)

In [380]: class P:
   .....:     'P classes'
   .....:     def __init__(self):
   .....:         print 'created an instance of', self.__class__.__name__
   .....: class C(P):
   .....:     pass

In [381]: p = P()    #parent instance
created an instance of P
In [382]: p.__class__     #class that created us
Out[382]: __main__.P
In [383]: P.__bases__       # parent’s parent classes(es)
Out[383]: ()
In [384]: P.__doc__     #parent’s doc string
Out[384]: 'P classes'
```
**`__bases__ Class Attribute`**
对任何子类，它是一个包含其父类`parent`的集合的元组。注意，我们明确指出”父类”是相对所有基类(它包括了所有祖先类)而言。那些没有父类的类，它们的`__base__`属性为空。
**通过继承覆盖方法**`Overriding Methods through Inheritance`
```
In [392]: class P(object):
   .....:     def foo(self):
   .....:         print 'Hi, I am P-foo()'

In [393]: p = P()
In [394]: p.foo()
Hi, I am P-foo()

In [395]: class C(P):
   .....:     def foo(self):
   .....:         print 'Hi, I am C-foo()'

In [396]: c = C()
In [397]: c.foo()
Hi, I am C-foo()
```
调用被覆盖的父类的方法，典型情况下，你会在子类的重写方法里显示地调用基类方法。
```
class C(P):
    def foo(self):
        P.foo(self)
        print ‘Hi, I am C-foo()’
```
注意这个未绑定方法调用中我们显式地传递了self。一个更好的办法是使用`super()`内建方法：
```
In [400]: class C(P):
   .....:     def foo(self):
   .....:         super(C, self).foo()
   .....:         print 'Hi, I am C-foo()'

In [401]: c = C()
In [402]: c.foo()
Hi, I am P-foo()
Hi, I am C-foo()
```
**Core Note**: 重写`__init__`不会自动调用基类的`__init__`
类似于上面的覆盖非特殊方法，当从一个带构造器`__init__()`的类派生，如果你不去覆盖`__init__()`,它将会被继承自动调用。但如果你在子类中覆盖了`__init__()`,子类被实例化时，基类的`__init__()`就不会被自动调用。如果你还想调用基类`__init__()`,需要使用一个子类的实例去调用基类(未绑定)方法。
```
class C(P):
    def __init__(self):
        P.__init__(self)
        print “calling C’s constructor”
```
使用`super()`的重点是你不需要明确提供父类，这意味着如果你改变了类继承关系，你只需要改一行(class 语句本身)而不必在大量代码中去查找所有被修改的那个类的名字。

### 从标准类型派生`Deriving Standard Types`
**不可变类型的例子**`Immutable Type Example`:假定你想在金融应用中，应用一个处理浮点数的子类。每次你得到一个货币值(浮点数给出的)，你都需要通过四舍五入，变为带两位小数位的数值。(`Decimal`类比起标准浮点类型来说是个用来精确保存浮点值的更加方案，但你还是有时候需要对其进行舍入操作)。
```
In [403]: class RoundFloat(float):
   .....:     def __new__(cls, val):
   .....:         return super(RoundFloat, cls).__new__(cls, round(val, 2))

In [404]: RoundFloat(1.5955)
Out[404]: 1.6
In [405]: RoundFloat(1.5945)
Out[405]: 1.59
In [406]: RoundFloat(-1.9955)
Out[406]: -2.0
```
**可变类型的例子**`Mutable Type Example`:子类化一个可变类型与此类似，你可能不需要使用`__new_()`(或甚至`__init__()`), 因为通常设置不多。一般情况下，你所继承到的类型的默认行为就是你想要的。下例中，我们简单地创建一个新的字典类型，它的`keys()`方法会自动排序结果：
```
In [412]: class SortedKeyDict(dict):
   .....:     def keys(self):
   .....:         return sorted(super(SortedKeyDict, self).keys())
   .....: d = SortedKeyDict((('zheng-cai', 67), ('hui-jun', 68), ('xin-yi', 2)))
   .....: print 'By iterator:'.ljust(12), [key for key in d]
   .....: print 'By keys():'.ljust(12), d.keys()
   .....: 
By iterator: ['zheng-cai', 'xin-yi', 'hui-jun']
By keys():   ['hui-jun', 'xin-yi', 'zheng-cai']
```
一定要谨慎，而且要意识到你正在干什么。如果你的方法调用`super()`过于复杂，取而代之可以这样：
```
def keys(self):
    return sorted(self.keys())
```
**多重继承**`Multiple Inheritance`
**方法解释顺序(MRO)**`Method Resolution Order`
Python2.2以前，算法非常简单：深度优先，从左至右进行搜索，取得在子类中使用的属性。其他Python算法只是覆盖被找到的名字，多重继承则取找到的第一个名字。
由于类，类型和内建类型的子类，都经过全新改造，有了新的结构，这种算法不再可行。这样一种新的MRO算法被开发出来。
**Core Note**:Python 2.2使用一种唯一但不完善的MRO
算法的基本思想是根据每个祖先类的继承结构，编译出一张列表，包括搜索到的类，按策略删除重复的。然而，在Python核心开发人员邮件列表中有人指出，在维护单调性方面失败过(顺序保存)，必须使用新的C3算法替换。

简单属性查找示例
![]({{BASE_PATH}}/image/core_python/imag5.png)
**经典类**`Classic Classes`:
经典类使用解释顺序，深度优先，从左至右
```
In [414]: class P1:    #parent class1
   .....:     def foo(self):
   .....:         print 'called P1-foo()'
   .....: class P2:     #parent class 2
   .....:     def foo(self):
   .....:         print 'called P2-foo()'
   .....:     def bar(self):
   .....:         print 'called P2-bar()'
   .....: class C1(P1, P2):         #child 1 der. from P1, P2
   .....:     pass
   .....: class C2(P1, P2):        #child 2 der. from P1, P2
   .....:     def bar(self):
   .....:         print 'called C2-bar()'
   .....: class GC(C1, C2):         #define grandchild class derives 
   .....:     pass

In [415]: gc = GC()
In [416]: gc.foo()    #GC ==> C1 ==> P1
called P1-foo()
In [417]: gc.bar()    #GC ==> C1 ==> P1 ==> P2
called P2-bar()
```
**新式类**`New-Style Classes`
与沿着继承树一步一步上溯不同，它首先查找同胞兄弟，采用一种广度优先的方式。当查找`foo()`,它检查GC，然后是C1和C2，然后是P1中找到，如果P1没有，查找将会到达P2。
```
In [418]: class P1(object):
   .....:     def foo(self):
   .....:         print 'called P1-foo()'
   .....: class P2(object):
   .....:     def foo(self):
   .....:         print 'called P2-foo()'
   .....:     def bar(self):
   .....:         print 'called P2-bar()'
   .....: class C1(P1, P2):
   .....:     pass
   .....: class C2(P1, P2):
   .....:     def bar(self):
   .....:         print 'called C2-bar()'
   .....: class GC(C1, C2):
   .....:     pass

In [419]: gc = GC()
In [420]: gc.foo()    #GC ==> C1 ==> C2 ==> P1
called P1-foo()
In [421]: gc.bar()      #GC ==> C1 ==> C2
called C2-bar()
```
然而，如果你还需要调用上一级，只要按前述方法，使用非绑定的方式去做。
```
In [422]: P2.bar(gc)
called P2-bar()
```
新式类也有一个`__mro__`属性，告诉你查找顺序：
```
In [423]: GC.__mro__
Out[423]: (__main__.GC, __main__.C1, __main__.C2, __main__.P1, __main__.P2, object)
```

## 类、实例和其他对象内建函数`Built-in Functions for Classes, Instances and Other Objects`
`issubclass(sub, sup)`    Returns true if class sub is a subclass of class sup, False otherwise
`isinstance(obj1, obj2)`   Returns true if instance obj1 is an instance of class obj2 or is an instance of a subclass of obj2; will also return True if obj1 is of type obj2; otherwise it returns False
`hasattr(obj, attr)`   Returns True if obj has attribute attr(give as a string), False otherwise
`getattr(obj, attr[,default])`   Retrieves attribute attr of obj; same as return as return obj.attr; if attr is not an attribute of obj, default return if given; else `AttributeError` exception raised
`setattr(obj,attr,val)`   Sets attribute attr of obj to value val, over riding any previously existing attribute value; otherwise, attribute is created; same as obj. attr = val
`delattr(obj, attr)`   Removes attribute attr (given as a string) from obj; same as del obj.attr
`dir(obj=None)`   Returns a proxy object representing the super class of type; if obj is not passed in, the super object returned is unbound; otherwise if obj is a type issubclass(obj, type) must be True; otherwise isinstance(obj, type) must be True.
`vars(obj=None)`   Returns a dictionary of the attributes and values of obj; if obj not given, vars() displays local namespace dicionary (attributes and values),i.e. locals()
```
In [425]: class C1(object):
   .....:     pass
   .....: class C2(object):
   .....:     pass

In [426]: c1 = C1()
In [427]: c2 = C2()
In [428]: isinstance(c1, C1)
Out[428]: True
In [429]: isinstance(c2, C1)
Out[429]: False
In [430]: isinstance(4, int)
Out[430]: True

In [431]: class myClass(object):
   .....:     def __init__(self):
   .....:         self.foo = 100

In [432]: myInst = myClass()
In [433]: hasattr(myInst, 'foo')
Out[433]: True
In [434]: getattr(myInst, 'foo')
Out[434]: 100
In [435]: hasattr(myInst, 'bar')
Out[435]: False
In [436]: setattr(myInst, 'bar', 'my attr')
In [437]: dir(myInst)
Out[437]:
['bar', 'foo']
In [438]: getattr(myInst, 'bar')
Out[438]: 'my attr'
In [439]: delattr(myInst, 'foo')
In [440]: hasattr(myInst, 'foo')
Out[440]: False

In [442]: class C(object):
   .....:     pass

In [443]: c = C()
In [444]: c.foo = 100
In [445]: c.bar = 'Python'
In [446]: c.__dict__
Out[446]: {'bar': 'Python', 'foo': 100}
In [447]: vars(c)
Out[447]: {'bar': 'Python', 'foo': 100}
```
## 用特殊方法定制类`Special Method for Customizing Classes`
**Basic Customization**
`C.__init__(self[, argq, ...])`:    构造器(带一些可选参数)
`C.__new__(self[, arg1, ...])`:   构造器(带一些可选参数); 通常用在设置不变数据类型的子类
`C.__del__(self)`:   解构器
`C.__str__(self)`:   可打印的字符串输出; 内建`str()`及`print`语句
`C.__repr__(self)`:   运行时的字符串输出; 内建`repr()`和\`\`操作
`C.__unicode__(self)`:   Unicode字符串输出;内建`unicode()`
`C.__call__(self, *args)`:   表示可调用的实例
`C.__nonzero__(self, *args)`   为`object`定义False值;内建bool()
`C.__len__(self)`:   “长度”(可用于类);内建`len()`

**Object(Value) Comparison**
`C.__cmp__(self, obj)`:   对象比较;内建`cmp()`
`C.__lt__(self, obj) and C.__le__(self, obj)`:   `<`或`<=`操作
`C.__gt__(self, obj) and C.__ge__(self, obj)`:   `>`或`>=`操作
`C.__eq__(self, obj) and C.__ne__(self, obj)`:   `==`或`!=`操作

**Attributes**
`C.__getattr__(self, attr)`:   获取属性;仅当属性没有找到时调用
`C.__setattr__(self, attr, val)`:   设置属性
`C.__delattr__(self, attr)`:   删除属性
`C.__getattribute__(self, attr)`:   获取属性;内建`getattr()`;总是被调用
`C.__get__(self, attr)`:   (描述符`descriptor`)获取属性
`C.__set__(self, attr, val)`:   (描述符)设置属性
`C.__delete__(self, attr)`:   (描述符)删除属性

**Customizing Classes/ Emulating Types**

**Numeric Type: Binary Operators**
`C.__*add__(self, obj)`:   Addtion; `+` operator
`C.__*sub__(self, obj)`:   Subtraction; `-` operator
`C.__*mul__(self, obj)`:   Multiplication; `*` operator
`C.__*div__(self, obj)`:   Division; `/` operator
`C.__*truediv__(self, obj)`:   True division; `/` operator
`C.__*floordiv__(self, obj)`:   Floor division; `//` operator
`C.__*mod__(self, obj)`:   Modulo/remainder; `%` operator
`C.__*divmod__(self, obj)`:   Divsion and modulo; `divmod()` built-in
`C.__*pow__(self, obj[, mod])`:   Exponentiation; `pow()` built-in; `**` operator
`C.__*lshift__(self, obj)`:   Left shift; `<<` operator
`C.__*rshift__(self, obj)`:   Right shift; `>>` operator
`C.__*and__(self, obj)`:   Bitwise AND; `&` operator
`C.__*or__(self, obj)`:   Bitwise OR; `|` operator
`C.__*xor_(self, obj)`:   Bitwise XOR; `^` operator

**一元操作符`Numeric Type: Unary Operator`**
`C.__neg__(self)`:   一元负(Unary negation)
`C.__pos__(self)`:   一元正(Unary no-change)
`C.__abs__(self)`:   Absolute value; `abs()` built-in
`C.__invert__(self)`:   Bit inversion; `~` operator

**Numeric Type: Numeric Conversion**
`C.__complex__(self, com)`:   Convert to complex; `complex()` built-in
`C.__int__(self)`:   Convert to int; `int()` built-in
`C.__long__(self)`:   Convert to long; `long()` built-in
`C.__float__(self)`:   Convert to float; `float()` built-in

**基本表示法`Numeric Type: Base Representation`**
`C.__oct__(self)`:   八进制表示(Octal representation; `oct()` built-in)
`C.__hex__(self)`:   十六进制表示(Hexadecimal representation; `hex()` built-in)

**数值压缩`Numeric Types: numeric coercion`**
`C.__coerce__(self, num)`:   压缩成同样的数值类型;Coerce to same numeric type; `coerce()` built-in
`C.__index__(self)`:   在有必要时，压缩可选的数值类型为整形(比如: 用于切片索引等等)(Coerce alternate numeric type to integer if/when necessary)

**Sequence Types**
`C__len__(self)`:   Number of items in sequence
`C.__getitem__(self, ind, val)`:   Get single swquence element
`C.__setitem__(self, ind, val)`:   Set single sequece element
`C.__delitem__(self, ind)`:   Delete single sequence element
**Sepcial Method**
`C.__getslice__(self, ind1, ind2)`:   Get sequence slice
`C.__setslice__(self, i1, i2, val)`:   Set sequence slice
`C.__delslice__(self, ind1, ind2)`:   Delete sequence slice
`C.__contains__(self, val)`:   Test sequence membership; `in` keyword
`C.__*add__(self, obj)`:   Concatenation; `+` operator
`C.__*mul__(self, obj)`:   Repetition; `*` operator
`C.__iter__(self, obj)`:   Create iterator class; `iter()` built-in

**Mapping Types**
`C._len__(self)`:   Number of items in mapping
`C.__hash__(self)`:   Hash function value
`C.__getitem__(self, key)`:   Get value with given `key`
`C.__setitem__(self, key, val)`:   Set `value` with given `key`
`C.__delitem__(self, key)`:   Delete value with given `key`
`C.__missing__(self, key)`:   Provides default value when dictionary does not have given `key`
上面在它们的名字中，用星号`*`通配符标注的数值二进制操作符则表示这些方法有多个版本。
增量赋值`Augmented assignment`，“原位`in-place`”操作符。一个“i”代替星号位置，表示左结合操作与赋值的结合。

**简单制定**`Simple Customizationi(roundFloat2.py)`
`print`(使用`str()`)和真正的字符串对象表示(使用`repr()`)。一个好的办法是，去实现`__str__()`和`__repr__()`二者之一。
```
In [3]: class RoundFloatManual(object):
   ...:     def __init__(self, val):
   ...:         assert isinstance(val, float), "Value must be a float"
   ...:         self.value = round(val, 2)
   ...:     def __str__(self):
   ...:         return str(self.value)

In [4]: rfm = RoundFloatManual(5.590463)
In [5]: rfm
Out[5]: <__main__.RoundFloatManual at 0x2455cd0>
In [6]: print rfm
5.59
```
我们还有一些问题，一个问题是仅仅在解释器中转储`dump`对象时，仍然显示的默认对象符号。最好的方案在`__str__()`中代码也是一个对象，同所有对象一样，引用可以指向他们，所以我们可以仅仅让`__repr__()`作为`__str__()`的一个别名。
```
In [8]: class RoundFloatManual(object):
   ...:     def __init__(self, val):
   ...:         assert isinstance(val, float), "Value must be a float!"
   ...:         self.value = round(val, 2)
   ...:     def __str__(self):
   ...:         return '%.2f' % self.value
   ...:     __repr__ = __str__

In [9]: rfm = RoundFloatManual(5.5964)
In [10]: rfm
Out[10]: 5.60
In [11]: print rfm
5.60
```
**数值定制**`Numeric Customization`
重载的方法有加法`Addition`和原位加法`In-Place Addtion`
```
In [1]: class Time60(object):
   ...:     def __init__(self, hr, min):
   ...:         self.hr = hr
   ...:         self.min = min
   ...:     def __str__(self):
   ...:         return '%d:%d' %(self.hr, self.min)
   ...:     __repr__ = __str__
   ...:     def __add__(self, other):
   ...:         return self.__class__(self.hr + other.hr, self.min + other.min)
   ...:     def __iadd__(self, other):
   ...:         self.hr += other.hr
   ...:         self.min += other.min
   ...:         return self

In [2]: mon = Time60(10, 30)
In [3]: tue = Time60(11, 15)
In [4]: mon
Out[4]: 10:30
In [5]: mon += tue
In [6]: mon
Out[6]: 21:45
```
**迭代器**`Iterators`
`__init__()`方法执行前述的赋值操作，`__iter__()`仅返回`self`，这就是如何将一个对象声明为迭代器的方式，最后，调用`next()`来得到迭代器中连续的值。
这个例子展示了一些我们可以用定制类迭代器来做的与众不同的事情--无穷迭代。他不会越界，每次用户调用`next()`时，他会得到下一个迭代值。
```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-
from random import choice
class RandSeq(object):
    def __init__(self, seq):
        self.data = seq

    def __iter__(self):
        return self

    def next(self):
        return choice(self.data)
```
**AnyIter**: 任意项的迭代器(anyIter.py)
```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-
class AnyIter(object):
    def __init__(self, data, safe=False):
        self.safe = safe
        self.iter = iter(data)

    def __iter__(self):
        return self

    def next(self, howmany=1):
        retval = []
        for eachIterm in range(howmany):
            try:
                retval.append(self.iter.next())
            except StopIteration:
                if self.safe:
                    break
                else:
                    raise
        return retval
```
