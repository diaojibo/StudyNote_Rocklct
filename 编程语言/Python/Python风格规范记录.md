## Python风格规范

### 文档字符串
ython有一种独一无二的的注释方式: 使用文档字符串. **文档字符串是包, 模块, 类或函数里的第一个语句**. 

这些字符串可以通过对象的__doc__成员被自动提取, 并且被pydoc所用. (你可以在你的模块上运行pydoc试一把, 看看它长什么样). 我们对文档字符串的惯例是使用三重双引号`”“”`

zwlj: **我们要用三重三重双引号来写类，函数的注释，用IDE则更加规范**

### 命名约定

 - 用单下划线(_)开头表示模块变量或函数是protected的(使用from module import *时不会包含).
 - 用双下划线(__)开头的实例变量或方法表示类内私有.
 - 将相关的类和顶级函数放在同一个模块里. 不像Java, 没必要限制一个类一个模块.
 - 对类名使用大写字母开头的单词(如CapWords, 即Pascal风格), 但是模块名应该用小写加下划线的方式(如lower_with_under.py). 

### 类
如果一个类不继承自其它类, 就显式的从object继承. 嵌套类也一样.

``` python
Yes: class SampleClass(object):
         pass


     class OuterClass(object):

         class InnerClass(object):
             pass


     class ChildClass(ParentClass):
         """Explicitly inherits from another class already."""
```

继承自 object 是为了使属性(properties)正常工作, 并且这样可以保护你的代码, 使其不受 PEP-3000 的一个特殊的潜在不兼容性影响. 这样做也定义了一些特殊的方法, 这些方法实现了对象的默认语义, 包括 `__new__, __init__, __delattr__, __getattribute__, __setattr__, __hash__, __repr__, and __str__ .`

#### 类内变量
默认情况下，Python中的成员函数和成员变量都是公开的(public),在python中没有类似public,private等关键词来修饰成员函数和成员变量。

可以用以下命名方式标识。

 - `_xxx`      "单下划线 " 开始的成员变量叫做保护变量，意思是只有类实例和子类实例能访问到这些变量，
需通过类提供的接口进行访问；不能用'from module import *'导入
 - `__xxx`    类中的私有变量/方法名 （Python的函数也是对象，所以成员方法称为成员变量也行得通。）,
" 双下划线 " 开始的是私有成员，意思是只有类对象自己能访问，连子类对象也不能访问到这个数据。
 - `__xxx__` 系统定义名字，前后均有一个“双下划线” 代表python里特殊方法专用的标识，如 `__init__()`代表类的构造函数。
