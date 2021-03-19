# Decorating

This is a VERY quick primer on decorators in python. Specifically what they are and how to create a few different types. I'll also try to add a few real world examples.. if I'm not too lazy.

If you want a more detailed explanation, I recommend [this](https://realpython.com/primer-on-python-decorators/) site.

## What's a Decorator?

No Carl, we're not talking about the person you hire to decorate for your wedding or a party.

A python decorator, by definition, is a function that takes another function as it's argument (wrapping it) and modifies the behavior or response of the "decorated" function, without explicitly modifying it.

Confused yet? Yeah me too! Let's break that down. 

First it's a function, that's easy enough. Second, you pass another function to it as an argument.. ok, sure.. but you're probably asking "what does that look like?" We'll get there.

### Functions are First Class Citizens

Now, I know what you're thinking Carl.. but do I really need 2 [tongues](https://www.quotes.net/mquote/827451).

To understand how decorators work, you need to understand one important concept [Python’s Functions Are First-Class objects](https://dbader.org/blog/python-first-class-functions). This means a function can be passed around to other functions as an argument (kind of like a function pointer in other languages).

```python
def foo(func):
    print("Entering Decorator")
    func()  # Call the function
    print("Leaving Decorator")

def bar():
    print("Bar is running")

# Call foo with bar
foo(bar)
```

The next thing you're probably asking yourself is.. What if I still want to call `#!python bar()` in my code and not have to go and update everything to call `#!python foo(bar)`? You're getting ahead of me Carl! Take a chill pill.

### Inner Functions

The next important thing to know is that in Python you can define a function in a function..

```python
def foo():
    def bar():
        print("In Bar")

# Call foo
foo()
```

!!! Note

    The above does pretty much nothing, since `bar` is never actually called.

You can also return a function from a function (even an inner function).. and unlike some programs, your code won't crash when the outer function goes out of [scope](https://realpython.com/python-scope-legb-rule/).

```python
def foo():
    def bar():
        print("In Bar")
    return bar

# Call foo.. which returns bar
test = foo()

# Calling test is the same as calling the inner function bar()
test()
```

Pretty cool, huh?

## Basic Function Wrapper

Sweet As! Now that you understand that functions are first class citizens in the world of python and that they can be passed around like ~~a joint at a party~~ any other object in python. And you understand how a function can return an inner function, you're now ready to take the [blue pill](https://en.wikipedia.org/wiki/Red_pill_and_blue_pill) and see how far this decorating rabbit hole really goes.



This is a simple example of how we can use one function to wrap another

```python
def foo(func):
    def wrapper():
        print("Entering Decorator")
        func()
        print("Leaving Decorator")
    return wrapper

def bar():
    print("Bar is running")

# Wrap bar with foo
test = foo(bar)

# Run bar
test() 
```

Pretty sweet, huh? We have now wrapped `bar` with `foo` in such a way that we now can modify `bar`'s behavior/response.

### Pour Some Sugar on Me!

Now.. the next question, can we make this easier and even more awesome? And the answer is YEP! Python offers some sweet syntactic sugar to make decorating even better!

Python has a cool feature called the ["pie"](https://www.python.org/dev/peps/pep-0318/#background) syntax, which allows us to condense the above example into this:

```python
def foo(func):
    def wrapper():
        print("Entering Decorator")
        func()
        print("Leaving Decorator")
    return wrapper

@foo
def bar():
    print("Bar is running")

# Run bar
bar()
```

!!! Note

    Notice the `@` symbol above the definition of `bar` this is the "pie" syntax and when we use it this way, we are telling Python to decorate our function using the function name direcly to the right of the `@`

## Decorating Classes

Hey Carl, want to know a secret? Yeah of course you do! Well here it is.. You can can decorate more than just simple functions. You can also decorate class methods and even... entire classes.

### Decorating Class Methods

Decorating a class method is the same as the basic decorators discussed above. A real world example of this would be the `#!python @staticmethod` and `#!python @classmethod` decorators.

!!! Note

    The `#!python @staticmethod` and `#!python @classmethod` allow you define methods in the class namespace that do not directly rely on a instance of the class

```python
class Foo(object):
    @classmethod
    def test(cls):
        print("Running Foo classmethod")
        return cls()

    @staticmethod
    def static_test():
        print("Running Foo.static_test")
```

!!! Note

    To define a decorate to use on a method in a class, follow the same steps above for Basic function decorators.

### Decorating Classes

The second type of class decorator is one that decorates the entire class. When you decorate an entire class, you're actually decorating the `#!python __init__` method.

```python
def foo(klass):
    def wrapper(*args, **kwargs):
        return klass(name="Inigo Montoya")
    return wrapper

@foo  # Decorating the Bar class
class Bar(object):
    def __init__(self, name: str=""):
        self.name = name

    def who_am_i(self):
        print("Hello! My name is {}".format(self.name))

# Call with `Foo` as the name
test = Bar(name="foo")
test.who_am_i()
```

!!! Note

    Carl, if you're asking who [Inigo Montoya](https://en.wikipedia.org/wiki/Inigo_Montoya) is... Get the hell off my site!

## Advanced Usage (Fancy Stuff)

Maintaining Docstrings

One of the unfortunate things about using decorators is that you lose any `#!python help` documentation from the "decorated" function. Take this for example:

```python
def foo(func):
    """Function Decorator"""
    def wrapper():
        """Inner wrapper. The user doesn't care about this"""
        print("Entering Decorator")
        func()
        print("Leaving Decorator")
    return wrapper

@foo
def bar():
    """Super cool function that does amazing things!"""
    print("Bar is running")

help(bar)
```

This will give you the help docstring for the `wrapper`, which is definitely not what we want. Instead we want the docstring for `bar`

This is where [functools](https://docs.python.org/3/library/functools.html) comes to the rescue! Specifically the `wraps` decorator.

```python
from functools import wraps


def foo(func):
    """Function Decorator"""
    @wraps(func)
    def wrapper():
        """Inner wrapper. The user doesn't care about this"""
        print("Entering Decorator")
        func()
        print("Leaving Decorator")
    return wrapper

@foo
def bar():
    """Super cool function that does amazing things!"""
    print("Bar is running")

help(bar)
```

Now that's more like it! As you can see if you run the above code, you're now getting the docstring from `bar` when you use `help` and not the inner wrapper.

## Real World Examples (Things I've actually written)

Sorry.. too lazy to put anything in here just yet. Maybe later.
