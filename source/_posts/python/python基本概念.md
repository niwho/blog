title: python基本概念(remind)
date: 2015-08-04 11:31:42
updated: 2015-08-04 11:31:47
tags: 
- python
- 基础
categories:
- python
---
>基本概念（个人理解）

<!--more-->
>详细化[tbd]

#classmethod staticmethod classvariable
>被问到的....
>类方法、静态方法、类变量
>类变量是和def函数的定义平级（同缩进）的变量定义
>类方法和静态方法都可访问类变量，均不能访问实例化变量

#闭包
>函数和引用环境(外部变量等)组成的整体
在有闭包语义的语言中，一般满足2个特性：
>>1. 函数是 first-calss value,即函数和一般变量一样作为参数或返回值
>>2. 函数可以嵌套定义

#函数修饰符
>重新包装函数

{% codeblock lang:python  %}
def authed(met):
    def wrapper(*args):#这个包装函数里做任意的其它内容
        met(args)#这里的met引用，闭包特性
    return wrapper

def authed2(a) #带参数的修饰符
    def w1(met):# a,met都是闭包的外部环境
        print a#example
        def w2(*args):
            met(args)
        return w2

#用法
@authed
def somfuc():
    ...
{% endcodeblock %}

#迭代器及生成器
>迭代器：有__iter__属性的对象，方法返回一个有next方法的对象（通常是自身）
>生成器：有yield方法的的函数，很大程度上和迭代器相同，除了next,有send方法可以提供yield的返回值，由于生成器正好是函数范畴的，可以实现协程的语义



#[metaclass](http://www.pythoncentral.io/how-metaclasses-work-technically-in-python-2-and-3/#custom-tab-0-python-2-x)
>metaclass和decorator是被framework普遍使用的技术了。
>metaclass是一种'定义'其它类的类，可以是类、函数或者任意的有可调用对象（有__call__属性）,函数方式和类的__new__相近除了少一个元类参数，这个参数对修改要生成的类无作用或者说不会用到)。类（被创建的）名、基类序列、类定义的所有属性（方法和变量）这3组数据将被用来调用metaclass（如前述，metaclass可被调用），这里特别说明一点，metaclass如果定义为一个类，则必须是type类的子类，其实就是调用类的构造过程，这个类的实例化是返回的别的类的定义（‘实例化’），type.__new__和object.__new__ 完全不同，python文档中未有type的详细说明，不过从type的调用说明可窥一斑，无论是用type.__new__(metaclass必须是type的子类）还是type()的方式，后一种方式退化为函数的指定方式，从而被创建类的type是type而不是这个metaclass
>类创建（定义）时，先从自身查找__metaclass__属性的定义，找到就用这个__metaclass__来创建类，否则使用基类的type来创建类
>在metaclass中定义__new__ 或者 __init__可以用来修改类的属性或基类
>除了了‘注入’修改类的创建，也可以改变类实例化时的行为，前提metaclass是类的指定方式，增加定义__call__方法，则对象的实例化是在metaclass的__call__这个方法中实例化返回，这里的定制化修改是发生在对象的创建时期，注意和前述的方式的差异，这里可定制化的修改是在对象上的修改而不是这个对象的类的修改。

##type和object
>其实要理解metaclass就不能不提python的type和object的[tbd]


#列表推导
>[Expression for Variable in  list \[condition\]]

{% codeblock lang:python  %}
[x for x in a if x % 2 == 0]
[[x,y] for x in range(2) for y in range(2)]
{% endcodeblock %}

#匿名函数或者lambda函数
>主要函数式编程使用
>格式：lambda argument1 argument2 ... :expression using arguments
>返回值就是expression的值

{% codeblock lang:python  %}
f=(lambda a,b:a>b)
f(1,2)
{% endcodeblock %}

#异常
>python的异常可以作为一种编程逻辑而不仅仅是对极端错误的特殊处理

{% codeblock lang:python 格式 %}
try:
   block
except [exception,[data…]]:
   block
else:   #可选，没有异常发生时执行
   block
finally:#可选，无论是否异常一定保证执行的代码
   block
raise [exception[,data]]#抛出异常

traceback.print_exc() #异常追溯
sys.exc_info()
{% endcodeblock %}

#函数式编程
>reduce(function, iterable, initializer=None),依次取iterable中的数据做合并操作，返回值是一个数据
>map(function, iterable1, iterable2...)，function每次接收对应的iterablex返回一个数据项，iterable不等长时，用None表示空项
>filter(function, iterable),function对每个元素做判断，真保留，假丢弃
>zip(iterable1, iterable2, ...) 合成元组，相当于map(lambda *args:args,iterable1,iterable2,...)

