# Abstract Class in Python

<https://www.developerhelps.com/abstract-class-in-python/>

未读文章
- [ABSTRACT CLASSES AND INTERFACES IN PYTHON](https://tutswiki.com/abstract-classes-and-interfaces-in-python/)
- [Python Abstract Classes vs Interfaces](https://www.gkindex.com/python-advanced/python-classes-interfaces.jsp)

*********************************

## Index

1. [Abstract base class in Python](#abstract-base-class-in-python)
2. [Python program to understand Abstract class](#python-program-to-understand-abstract-class)
3. [Difference between abstract class and interface](#difference-between-abstract-class-and-interface) \
   [3.1 What is the difference between abstract class and interface](#what-is-the-difference-between-abstract-class-and-interface) \
   [3.2 Using an abstract base class](#using-an-abstract-base-class) \
   [3.3 Implementation: creating an abstract base class](#implementation-creating-an-abstract-base-class) \
   [3.4 Conclusion](#conclusion)


An abstract class is basically the blueprint of a class. An abstract class in 
python is the same as the initial definition. This class allows the user to 
create a set of methods that must be created within any subclasses built from 
the abstract class, considering it as the main class. A class that contains 
one or more abstract methods is called an abstract class. An abstract method 
is a method that has a declaration but does not have an implementation. While 
we are designing large functional units we use an abstract class. When we want 
to provide a common interface for different implementations of a component, we 
use an abstract class. 


## 1 Abstract base class in Python

The user can have a common API by having an abstract base class for a set of 
child classes. This capability is especially useful in situations where a 
third-party is going to provide implementations, such as with plugins, but can 
also help you when working in a large team or with a large code-base where 
keeping all classes in your mind is difficult or not possible. 

Python cannot provide abstract classes by default, they have to be created by 
the user. Python comes with a module that provides the base for defining 
Abstract Base classes(ABC) and that module name is ABC. ABC works by decorating 
methods of the base class as abstract and then registering concrete classes as 
implementations of the abstract base. A method becomes abstract when decorated 
with the keyword @abstractmethod.

[Back to index](#index)


## 2 Python program to understand Abstract class

```python
from abc import ABC, abstractmethod

class Polygon(ABC):

	@abstractmethod
	def noofsides(self):
		pass
class Circle(Polygon):

	# overriding abstract method
	def noofsides(self):
		print("I have no sides")
		
class Triangle(Polygon):

	# overriding abstract method
	def noofsides(self):
		print("I have 3 sides")
		


class Pentagon(Polygon):

	# overriding abstract method
	def noofsides(self):
		print("I have 5 sides")

class Hexagon(Polygon):

	# overriding abstract method
	def noofsides(self):
		print("I have 6 sides")

class Quadrilateral(Polygon):

	# overriding abstract method
	def noofsides(self):
		print("I have 4 sides")

# Driver code
A = Circle()
A.noofsides()

R = Triangle()
R.noofsides()



K = Quadrilateral()
K.noofsides()

R = Pentagon()
R.noofsides()

K = Hexagon()
K.noofsides()
```

[Back to index](#index)


### 2.1 Output

```python
I have no sides
I have 3 sides
I have 4 sides
I have 5 sides
I have 6 sides
```

### 2.2 Summary

- Abstract classes are classes that you cannot create instances from.
- Use abc module to define abstract classes.
- An abstract is the blueprint of a class.

[Back to index](#index)


## 3 Difference between abstract class and interface

<!-- https://pyquestions.com/difference-between-abstract-class-and-interface-in-python -->

What you'll see sometimes is the following:
```python
class Abstract1:
    """Some description that tells you it's abstract,
    often listing the methods you're expected to supply."""

    def aMethod(self):
        raise NotImplementedError("Should have implemented this")
```
Because Python doesn't have (and doesn't need) a formal Interface contract, the 
Java-style distinction between abstraction and interface doesn't exist. If 
someone goes through the effort to define a formal interface, it will also be 
an abstract class. The only differences would be in the stated intent in the 
docstring.

And the difference between abstract and interface is a hairsplitting thing when 
you have duck typing.

Java uses interfaces because it doesn't have multiple inheritance.

Because Python has multiple inheritance, you may also see something like this
```python
class SomeAbstraction:
    pass  # lots of stuff - but missing something
​
class Mixin1:
    def something(self):
        pass  # one implementation
​
class Mixin2:
    def something(self):
        pass  # another
​
class Concrete1(SomeAbstraction, Mixin1):
    pass
​
class Concrete2(SomeAbstraction, Mixin2):
    pass
```

This uses a kind of abstract superclass with mixins to create concrete subclasses 
that are disjoint.


### 3.1 What is the difference between abstract class and interface

An interface, for an object, is a set of methods and attributes on that object.

In Python, we can use an abstract base class to define and enforce an interface.

[Back to index](#index)


### 3.2 Using an abstract base class

For example, say we want to use one of the abstract base classes from the 
`collections` module:
```python
import collections
class MySet(collections.Set):
    pass
```

If we try to use it, we get an TypeError because the class we created does not 
support the expected behavior of sets:
```shell
>>> MySet()
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: Can't instantiate abstract class MySet with abstract methods
__contains__, __iter__, __len__
```

So we are required to implement at **least** `__contains__`, `__iter__`, and 
`__len__`. Let's use this implementation example from the 
[documentation](https://docs.python.org/2/library/collections.html#collections-abstract-base-classes):

```python
class ListBasedSet(collections.Set):
    """Alternate set implementation favoring space over speed
    and not requiring the set elements to be hashable. 
    """
    def __init__(self, iterable):
        self.elements = lst = []
        for value in iterable:
            if value not in lst:
                lst.append(value)
    def __iter__(self):
        return iter(self.elements)
    def __contains__(self, value):
        return value in self.elements
    def __len__(self):
        return len(self.elements)
​
s1 = ListBasedSet('abcdef')
s2 = ListBasedSet('defghi')
overlap = s1 & s2
```

[Back to index](#index)


### 3.3 Implementation: creating an abstract base class

We can create our own Abstract Base Class by setting the metaclass to 
`abc.ABCMeta` and using the `abc.abstractmethod` decorator on relevant methods. 
The metaclass will be add the decorated functions to the `__abstractmethods__` 
attribute, preventing instantiation until those are defined.
```python
import abc
```
​
For example, "effable" is defined as something that can be expressed in words. 
Say we wanted to define an abstract base class that is effable, in Python 2:
```python
class Effable(object):
    __metaclass__ = abc.ABCMeta
    @abc.abstractmethod
    def __str__(self):
        raise NotImplementedError('users must define __str__ to use this base class')
```

Or in Python 3, with the slight change in metaclass declaration:
```python
class Effable(object, metaclass=abc.ABCMeta):
    @abc.abstractmethod
    def __str__(self):
        raise NotImplementedError('users must define __str__ to use this base class')
```

Now if we try to create an effable object without implementing the interface:
```python
class MyEffable(Effable): 
    pass
```

and attempt to instantiate it:
```shell
>>> MyEffable()
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: Can't instantiate abstract class MyEffable with abstract methods __str__
```

We are told that we haven't finished the job.

Now if we comply by providing the expected interface:
```python
class MyEffable(Effable): 
    def __str__(self):
        return 'expressable!'
```

we are then able to use the concrete version of the class derived from the abstract one:
```shell
>>> me = MyEffable()
>>> print(me)
expressable!
```

There are other things we could do with this, like register virtual subclasses 
that already implement these interfaces, but I think that is beyond the scope of 
this question. The other methods demonstrated here would have to adapt this 
method using the abc module to do so, however.

[Back to index](#index)


### 3.4 Conclusion

We have demonstrated that the creation of an Abstract Base Class defines 
interfaces for custom objects in Python.

Python >= 2.6 has [Abstract Base Classes](https://docs.python.org/library/abc.html).

Abstract Base Classes (abbreviated ABCs) complement duck-typing by providing a 
way to define interfaces when other techniques like `hasattr()` would be clumsy. 
Python comes with many builtin ABCs for data structures (in the collections 
module), numbers (in the numbers module), and streams (in the io module). You can 
create your own ABC with the abc module.

There is also the [Zope Interface](https://pypi.python.org/pypi/zope.interface) 
module, which is used by projects outside of zope, like twisted. I'm not really 
familiar with it, but there's a wiki page here that might help.

In general, you don't need the concept of abstract classes, or interfaces in 
python (edited - see S.Lott's answer for details).

Python doesn't really have either concept.

It uses duck typing, which removed the need for interfaces (at least for the 
computer :-))

Python <= 2.5: Base classes obviously exist, but there is no explicit way to 
mark a method as 'pure virtual', so the class isn't really abstract.

Python >= 2.6: Abstract base classes do [exist](https://www.python.org/dev/peps/pep-3119/) 
(http://docs.python.org/library/abc.html). And allow you to specify methods 
that must be implemented in subclasses. I don't much like the syntax, but the 
feature is there. Most of the time it's probably better to use duck typing from the 
'using' client side.

In a more basic way to explain: An interface is sort of like an empty muffin pan. 
It's a class file with a set of method definitions that have no code.

An abstract class is the same thing, but not all functions need to be empty. 
Some can have code. It's not strictly empty.

Why differentiate: There's not much practical difference in Python, but on the 
planning level for a large project, it could be more common to talk about 
interfaces, since there's no code. Especially if you're working with Java 
programmers who are accustomed to the term.

In general, interfaces are used only in languages that use the single-inheritance 
class model. In these single-inheritance languages, interfaces are typically 
used if any class could use a particular method or set of methods. Also in these 
single-inheritance languages, abstract classes are used to either have defined 
class variables in addition to none or more methods, or to exploit the 
single-inheritance model to limit the range of classes that could use a set of 
methods.

Languages that support the multiple-inheritance model tend to use only classes 
or abstract base classes and not interfaces. Since Python supports multiple 
inheritance, it does not use interfaces and you would want to use base classes 
or abstract base classes.

http://docs.python.org/library/abc.html

Abstract classes are classes that contain one or more abstract methods. Along 
with abstract methods, Abstract classes can have static, class and instance 
methods. But in case of interface, it will only have abstract methods not other. 
Hence it is not compulsory to inherit abstract class but it is compulsory to 
inherit interface.

For completeness, we should mention [PEP3119](https://www.python.org/dev/peps/pep-3119/#abcs-vs-interfaces) 
where ABC was introduced and compared with interfaces, and original Talin's comment.

The abstract class is not perfect interface:
- belongs to the inheritance hierarchy
- is mutable

But if you consider writing it your own way:
```python
def some_function(self):
     raise NotImplementedError()
​
interface = type(
    'your_interface', (object,),
    {'extra_func': some_function,
     '__slots__': ['extra_func', ...]
     ...
     '__instancecheck__': your_instance_checker,
     '__subclasscheck__': your_subclass_checker
     ...
    }
)
​
ok, rather as a class
or as a metaclass
and fighting with python to achieve the immutable object
and doing refactoring
...
```

you'll quite fast realize that you're inventing the wheel to eventually achieve 
`abc.ABCMeta`

`abc.ABCMeta` was proposed as a useful addition of the missing interface 
functionality, and that's fair enough in a language like python.

Certainly, it was able to be enhanced better whilst writing version 3, and 
adding new syntax and immutable interface concept ...

Conclusion:
```python
The abc.ABCMeta IS "pythonic" interface in python
```

[Back to index](#index)
