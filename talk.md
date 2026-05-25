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
