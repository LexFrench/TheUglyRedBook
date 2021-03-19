# Common Anti-Patterns

Ok Carl, here's the thing, Python gives you all the tools necessary to build a gun and shoot yourself in the foot. Here are a list of things you shouldn't really do.

## Assigning a variable or function to a built-in

At any time you can override one of Python's builtin function by assigning a variable to function to the same name as the builtin. Now.. there are some very RARE cases where you might want to do this and for this reason Python offers the builtin module to help with changing the behavior of a builtin. But most of the time.. just don't it Carl.

[Python Built-in Functions](https://docs.python.org/3.7/library/functions.html)

### BAD

```python
# Overwriting id with some value
id = "FOO"
# Now try to get the ID of some other object
test = "BAR"
print(id(test))
# TypeError: 'str' object is not callable
```

### GOOD

```python
# Overwriting id with some value
_id = "FOO"
# Now try to get the ID of some other object
test = "BAR"
print(id(test))
# > 140614531283184
```

## Not using `with` to open files

Hey Carl, did you know that everytime you open a file object without closing a cat pushes a breakable object off a counter? Do you know why? Because cats are assholes.. What does that have to do with file objects? Nothing. What can you do make sure you always close your files and not be an asshole like those cats? Use the `#!python with`context manager.

What is a context manager? Go read this: [Context Manager in Python - GeeksforGeeks](https://www.geeksforgeeks.org/context-manager-in-python/)

So Carl do you get it now? With the `#!python with`context manager, you're guaranteed that when your code goes out of scope, the file object will be automatically closed.

### BAD

```python
filePointer = open("test.txt")
data = filePointer.read()
# Do stuff and never close your file
```

### GOOD

```python
with open("test.txt") as filePointer:
    data = filePointer.read()
```

## Returning more than one object type from a function

Ok Carl, I'll be honest, this is one of those "Do as I say, not as I do." In a perfect world, you shouldn't return more than one object type from a function/method. However, it's pretty common to return None if the object you want to return can't be. For example, a method that gets some resource from the internet. if the resource isn't available, you COULD return None to indicate that it failed.

However, this means that your caller need to be aware of and handle the None objects. A better solution would be to either raise an exception (faster) or return an invalid version of your object type if you can.

### BAD

```python
def test(inputValue: bool):
    if inputValue:
        return "FOO"
```

!!! Note

    This is a bit tricky to the unfamiliar. In python this method will return a `None` type object if the `inputValue` doesn't evaluate as true.

### GOOD

```python
class Foo(object):
    def __init__(self, value: str=None):
        self.value = value

    def is_valid(self):
        return self.value is not None


def test(inputValue):
    if inputValue:
        return Foo("FOO")
    return Foo()
```

!!! Note

    This is a bit long winded but it's consistent in that we are always returning an object of type `Foo`

### BETTER

```python
def test(inputValue: bool):
    if inputValue:
        return "FOO"
    raise ValueError("inputValue was not accepted")
```

!!! Note

    Caller will need to wrap code in try/except. This means they will need to know your function can raise a ValueError. However this is faster than always checking for a `None` type; since we'll only hit the exception clause when the test method raises.

## Asking for permission instead of forgiveness

Ok, yes Carl, I know you should usually ask for permission before you do something but that's not how we roll in Python. Here we live in the land of [duck-typing](https://en.wikipedia.org/wiki/Duck_typing) so we just assume everything works and go for it!

Or as the official python docs put it [EAFP!](https://docs.python.org/3.5/glossary.html#term-eafp)

### BAD

```python
if "key" in foo:
    value = foo["key"]
```

### GOOD

```python
try:
    value = foo["key"]
except KeyError:
    pass
```

## Bad Excepts

Don't do this unless you have a damn good reason.

### BAD

```python
try:
    foo()
except:
    print("FOO raised")
```

### GOOD

```python
try:
    foo()
except RuntimeError:
    print("FOO raised RuntimeError")
except ValueError:
    print("FOO raised ValueError")
```

## Method could be a Function

You might have seen this error if you've run pylint on your code. Honestly, this is one that I'm not a huge fan of but it is good practice. So I'll cover it here. 

First off, what does this mean? The simple version is that you've written a method as part of a custom class that doesn't use the self argument and therefore "probably" should be a staticmethod/classmethod OR a independent function.

Second, why does this warning get raised. Mostly its python's way of asking "Are you sure this method needs to be defined as part of this class?" Python is second guessing you and asking if you meant to do that or are you an idiot Carl? Python's pretty judgy..

### BAD

```python
class Foo(object):
    def __init__(self):
        self.foo = "FOO"

    def bar(self):
        return "BAR"
```

### GOOD

```python
class Foo(object):
    def __init__(self):
        self.foo = "FOO"

def bar():
    return "BAR"
```

## Mutable default values as arguments

Oh man, Carl, you have no idea how many times have I seen this one from junior devs or TDs that aren't super familiar with the python languege.

It's really easy to think that you can set any default value to an argument. However, mutable default arguments (such as list) can come back to bite you in the ass. So I would recommend you don't this.. ever!

### BAD

```python
def foo(value: list=[]):
    value.append(1)
    return value

print(foo([123])) # [123, 1] 
print(foo()) # [1]
print(foo()) # [1, 1]
```

### GOOD

```python
def foo(value: list=None):
    if value is None:
        value = []
    value.append(1)
    return value

print(foo([123])) # [123, 1] 
print(foo()) # [1]
print(foo()) # [1,]
```

## Wildcard imports

Sigh.. I should really create a sub-list called Python Anti-Patterns called "Do as I say, not as I do." This is another case where you "shouldn't" do this but.. sometimes it's just easier or makes sense.

First, you may ask, what is a wildcard import? Simply, it's when you import every class/function/variable from a module in a single line:

```python
from Foo import *
```

Now you ask, why shouldn't I do that? Its way easier to type that one line than type the X number of imports to bring in the classes or functions I need from Foo. And you're right, it is easier. But.. easier isn't always better Carl.

The big problem with that one-liner is that it also brings in all the stuff from Foo you might NOT want. What if in Foo there was a function called `#!python bool`? By using the wildcard import you're now are replacing the built-in `#!python bool` function with `#!python Foo.bool` and this can have pretty significant consequences. So when using imports always make sure you import only what you need and nothing else.

!!! Note

    Yep, you can absolutely overwrite python built in functions. Try it sometime, it's scary as hell.

### BAD

```python
from Foo import *
```

### GOOD

```python
from Foo import bar
from Foo import foo
from Foo import FooBar
```

## Using single letters to name variables

This one is simple. Do I even need to give an example?

### BAD

```python
c = 1
d = 2
e = 3
```

### GOOD

```python
cat = 1
dog = 2
eagle = 3  # Yeah I couldn't think of anything better
```

## Using `type()` to compare types

Ok, sure you COULD do a type() string comparison but why? That's so passé

### BAD

```python
class Foo(object):
    pass

foo = Foo()

if type(foo) == Foo:
    print("SAME TYPE")
```

### GOOD

```python
class Foo(object):
    pass

foo = Foo()

if isinstance(foo, Foo):
    print("SAME TYPE")
```

## Incorrectly comparing None

This is a bit odd if you're use to other languages such as C++. In other languages when you want to compare a variable to a Null value you would typically use the standard comparison operator `#!python ==` . And while you can do this, it's not the "pythonic" way.

In python, `#!python None` is an object (like almost everything else). More importantly `#!python None` is a singleton object, which means all instances of `#!python None` are the same object. 

Yep Carl, you know what that means! We should use the `#!python is` operator. 

!!! Note

    If you're not familiar with the `#!python is` operator, check out [This](https://stackoverflow.com/questions/13650293/understanding-pythons-is-operator)

### Bad

```python
foo = None
if foo == None:
    print("FOO IS NONE")
```

### GOOD

```python
foo = None
if foo is None:
    print("FOO IS NONE")
```

## Using built-in function to initialize list/dict/tuple

This is more of a pet-peeve but don't use the list/dict/tuple keywords. 

### BAD

```python
foo = list()
bar = dict()
foobar = tuple()
```

### GOOD

```python
foo = []
bar = {}
foobar = ()
```

Also, FWIW, using the brackets instead of the function calls is faster!

```python
import timeit

slow = """
test = tuple()
"""

fast = """
test = ()
"""
print(timeit.timeit(stmt=slow, number=1000000))
print(timeit.timeit(stmt=fast, number=1000000))
```

## Not following PEP8

Last but not least, not following PEP8 is one of the worst crimes against python you can make. Carl... If you do this.. You're DEAD to me!

[PEP 8 -- Style Guide for Python Code | Python.org](https://www.python.org/dev/peps/pep-0008/)
