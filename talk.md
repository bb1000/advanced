# Advanced concepts

---

layout: false

## Advanced concepts in Python

* Context managers
* Decorators
* Iterators

---

## Context managers

* Context manager: executes code in a with statement
* Setup before and after with `__enter__` and `__exit__` methods
* Example: open file and automatically close it after block of code


~~~
class MyFile:
    def __init__(self, filename, mode='r'):
        self.filename = filename
        self.mode = mode
        
    def __enter__(self):
        self.file = open(self.filename, self.mode)
        return self.file
    
    def __exit__(self, *args):
        self.file.close()
~~~

---

## Decorators in Python: basic concepts

- Python function definition

~~~
def function_name(parameters):
    "function docstring"
    function body
    return [expression]
~~~

- Python class method definition

~~~
def methodname(self, parameters):
    "method docstring"
    method body
    return [expression]
~~~

* Functions are first-class objects:
    * implemented operators can be applied to function variable
    * functions can be passed as arguments to other functions
    * functions can be used as class/object variables in Class

---

## Decorators in Python: basic concepts

* passing arguments to function via args-kwargs syntax

~~~
def function_name(*args, **kwargs):
    "function docstring"
    function body
    return[expression]
~~~

* args are positional parameters of function (tuple)
* kwargs are key-valued parameters of the function (dict)

The function call
~~~
>>> function_name('hello', 'world', when='now')
~~~
will map values to variables in the function
~~~
args -> ('hello', 'world')  # tuple
kwargs -> {'when': 'now'}  # dict
~~~


---

* Definition: a decorator extends and modified the behaviour of a callable
  (functions, methods, and classes) without permantly modifying the callable
  itself

* *A decorator is a function that takes a function as in put and returns a
function as output*

~~~
def decorator(f):
    ...
    
@decorator
def function_name(...):
    ...
~~~
is equivalent to
~~~
def decorator(f):
    ...
    
def function_name(...):
    ...
    
    
function_name = decorator(function_name)
~~~
---

~~~
def empty_decorator(func):
    return func
    
def hello():
    return "Hello world"
    
simple_hello = empty_decorator(hello)

>>> simple_hello()
Hello world!
~~~

* Returning modified function from decorator

~~~
def uppercase(func):
    def wrapper():
        original_result = func()
        modified_result = original_result.upper()
        return modified_result
    return wrapper
    
@uppercase
def hello():
    return "Hello world!"
    
>>> hello()
HELLO WORLD!
~~~

---
* Multiple decorators applied to single function

~~~
def strong(func):
    def wrapper():
        return '**' + func() + '**'
    return wrapper
    
def emphasis(func):
    def wrapper():
        return'!!' + func() + '!!'
    return wrapper
    
@emphasis
@strong
def hello():
    return'Hello World'
    
>>> hello()
!!**Hello World**!!
~~~

---

* Passing variables to decorator

~~~
def decorator_function(func):
    def wrapper(*args, **kwargs):
        return func(*args, **kwargs)
    return wrapper
~~~

---

* Example: inspect values passed to and from a function

~~~
from functools import wraps

def trace(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        print(
            f'TRACE: calling {func.__name__}() '
            f'with {args}, {kwargs}'
        )
        original_result = func(*args, **kwargs)
        print(
            f'TRACE: {func.__name__}() '
            f'returned {original_result!r}'
        )
        return original_results
    return wrapper
~~~

* Apply trace decorator to function

~~~
@trace
def say(greet, name):
    return greet + ",' +  name
~~~
~~~
>>> say("Hello", "world")
TRACE: calling say() with ('Hello', 'world'), {}
TRACE: say() returned 'Hello, world'
Hello, world
~~~

---

* Decorate a class method

~~~
def as_html(func):
    def func_wrapper(*args, **kwargs):
        return f"<p>{func(*args, **kwargs)}</p>
    return func_wrapper
    
class Person:
    def__init__(self):
        self.name = "John"
        self.family = "Doe"
        
        
    @as_html
    def hello(self):
        return f"Hello, my name is {self.name} {self.family}"
        
~~~
~~~
>>> my_person = Person("Anne", "Applebaum")
>>> print(my_person.hello())
<p>Hello, my name is Anne Applebaum</p>
~~~

---

About decorators:

* decorators is good way to avoid code duplication
* decorators allows to write more "pythonic" and clean code
* functools.wraps make your decorators debug friendly when using multiple
  decorators

See also

* https://docs.python.org/3/glossary.html#term-decorator
* https://realpython.com/primer-on-python-decorators/
* https://www.geeksforgeeks.org/decorators-in-python/

---

## Iterators


* objects that can be used in for loops

### A list

~~~python
>>> li = [0, 1, 2]
>>> for i in li:
...     print(i, end=" ")
0 1 2 

~~~


### Dictionary

* The loop variable is the key of the key-value pair

```python
>>> dict = {'a':1, 'b':2}
>>> for k in dict:
...     print(k, dict[k])
a 1
b 2

```
---

### String

```
>>> str = 'abc'
>>> for c in str:
...     print(c)
a
b
c

```

### File objects


<!--
>>> import subprocess
>>> n = subprocess.call("/bin/echo 'one\ntwo\nthree' > 123.txt", shell=True)

-->

    #123.txt
    one
    two
    three

```
>>> for row in open('123.txt'):
...     print(row, end="")
one
two
three

```
---

## Iterable vs iterator


```
>>> li = [0, 1, 2]
>>> print(li)
[0, 1, 2]

```

If you can call `iter` with an object that object it is *iterable*

```
>>> li_iter = iter(li)
>>> type(li_iter)
<class 'list_iterator'>

```

`iter` will return an object that supports `next` - an *iterator*


```
>>> next(li_iter)
0
>>> next(li_iter)
1
>>> next(li_iter)
2
>>> next(li_iter)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
StopIteration

```

Illustrates what happens behind the scenes in a for loop

---

### Objects supporting iteration

What does iter vs next (built-in function) do? Roughly it delegates

~~~
#What the builtin iter roughly does
def iter(object):
    return object.__iter__()
~~~

~~~
#What the builtin next roughly does
def next(object):
    return object.__next__()
~~~

* iterables are defined by classes with an `__iter__` method, which which
  should return an iterator
* iterators are defined by classes with a `__next__` method, producing the
  next value of the sequence
* the for loop will stop when a `StopIteration` exception is encountered

---

### Defining your own iterator

~~~python
>>> class Counter:
...     def __init__(self, size):
...         print("__init__:", size)
...         self.size = size
...         self.start = 0
... 
...     def __iter__(self):
...         print("__iter__:", self.size)
...         return CounterIter(self.start, self.size)
... 
>>> class CounterIter:
... 
...     def __init__(self, start, size):
...         self.start = start
...         self.size = size
... 
...     def __next__(self):
...         if self.start < self.size:
...             self.start = self.start + 1
...             return self.start
...         raise StopIteration

~~~

~~~
>>> c = Counter(3)
__init__: 3
>>> for num in c:
...     print(num, end=" ")
__iter__: 3
1 2 3 

~~~

---

## Generators


* Functions that contain the  yield statment
* Support iteration protocol - they return an iterator
* Resumable
* Generators are not executed when invoked, they are iterated over

### function vs. generator

~~~
>>> def f(n):
...    return n
>>> type(f)
<class 'function'>
>>> type(f(1))
<class 'int'>

~~~

~~~

>>> def g(n):
...    yield n
>>> type(g)
<class 'function'>
>>> type(g(1))
<class 'generator'>

~~~

---

### Example

~~~
>>> def g(n):
...     print('enter g with',n)
...     yield n
...     print('after yield')

~~~

~~~
>>> g2=g(2)
>>> next(g2)
enter g with 2
2
>>> next(g2)
Traceback (most recent call last):
...
StopIteration


~~~

* So: this function appears to pause at the yield statement after returning the value and continue from there the next time the next() method is called...

* When the function exits a StopIteration exception is raised

---

### Example

~~~
>>> def g(n):
...     print('enter g with ',n)
...     i=0
...     while i < n:
...         yield i
...         print('after yield')
...         i += 1
...     print('after while')

~~~

~~~
>>> g2=g(2)
>>> next(g2)
enter g with  2
0
>>> next(g2)
after yield
1
>>> next(g2)
Traceback (most recent call last):
...
StopIteration

~~~

---

### Iterator in for loop

```
>>> for  i in g(5):
...     print(i, end=" ")
enter g with  5
0 after yield
1 after yield
2 after yield
3 after yield
4 after yield
after while

```

### Example: fibonacci


```
>>> def fib(n):
...     a = 1
...     b = 2
...     while a < n:
...         yield a
...         a, b = b, a + b
>>> for i in fib(5):
...     print(i, end=" ")
1 2 3 

```

---

### Convert to list

It is always possible to convert a generator to a list


```
>>> list(fib(100))
[1, 2, 3, 5, 8, 13, 21, 34, 55, 89]

```

---

## Summary

* Several common types support iteration (list, dict, file, str)
* Objects that support iteration have an `__iter__` method returning an
  iterator
* The iterators have a `__next__` method that steps through some sequence
* Generators are functions with a `yield` statement and work like iterators
