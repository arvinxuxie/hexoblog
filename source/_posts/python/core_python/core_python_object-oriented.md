---
layout: post
title: Object-Oriented Programming
category: python
tags: core-python
---
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
在静态方法加入到Python之前，用户只能在全局名字空间中创建函数，作为这种性质的



