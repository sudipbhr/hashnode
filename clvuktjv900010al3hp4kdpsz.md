---
title: "Functions and Classes in Python"
datePublished: Mon May 06 2024 06:24:14 GMT+0000 (Coordinated Universal Time)
cuid: clvuktjv900010al3hp4kdpsz
slug: functions-and-classes-in-python

---

**Defining a function**

`def` keyword, followed by the function name and parentheses `()`. Any parameters go inside the parentheses.

```python
def function_name(parameters):
    """
    Docstring: Brief description of the function
    """
    # Function body
    # Code block
    return value
```

**Calling a function**

function name followed by parentheses `()`

```python
function_name(arguments)
```

**Parameters and Arguments**

The values you pass into a function when calling it are called arguments. The variables listed in the function definition are called parameters.

```python
def greet(name, greeting="Hello"):  # name is a parameter, greeting is a parameter with a default value
    print(f"{greeting}, {name}!")

greet("Alice")  # Alice is the argument
greet("Bob", "Hi")  # Bob is the argument, Hi is the argument
```

**\**Arbitrary Arguments (args)***

If you don't know how many arguments will be passed to a function, you can use the `*args` syntax to collect them into a tuple.

```python
def print_numbers(*args):
    for arg in args:
        print(arg)

print_numbers(1, 2, 3)  # Output: 1 2 3
```

***\*\*Keyword Arguments (kwargs)***

You can also collect arbitrary keyword arguments into a dictionary using the `**kwargs` syntax.

```python
def print_info(**kwargs):
    for key, value in kwargs.items():
        print(f"{key}: {value}")

print_info(name="Alice", age=25)  # Output: name: Alice, age: 25
```

**Lambda Functions**

Python supports the creation of anonymous functions (lambda functions). These are small, one-line functions.

```python
square = lambda x: x ** 2
print(square(5))
```

## Python Classes/Objects

Python is an object oriented programming language.

Almost everything in Python is an object, with its properties and methods.

A Class is like an object constructor, or a "blueprint" for creating objects.

## Define Python Class

We use the `class`[keyword to cr](https://www.programiz.com/python-programming/keywords-identifier)eate a class i[n Pytho](https://www.programiz.com/python-programming/keywords-identifier)n. For example,

```python
class ClassName:
    # class definition
```

Here, we have created a class named `ClassNam`[`e`.](https://www.programiz.com/python-programming/keywords-identifier)

[Let](https://www.programiz.com/python-programming/keywords-identifier)'s see an example,

```python
class Room:
    length = 0
    breadth = 0

    def area(self):
        return self.length * self.breadth
```

1. `Room` - the name of the class
    
2. `length and breadth` are the attributes of the class
    
3. `area` is the method of the class
    

Understanding **init** method

```python
 def __init__(self, length=0, breadth=0):
        self.length = length
        self.breadth = breadth
        Room.num_rooms += 1
```

It is the constructor of the class Room. Meaning it is initialized everytime when the class is called(when object is created)

```python
class Room:
 

    # Constructor
    def __init__(self, length=0, breadth=0):
        self.length = length
        self.breadth = breadth
        
    # Instance methods
    def area(self):
        return self.length * self.breadth

    def perimeter(self):
        return 2 * (self.length + self.breadth)

    def print_details(self):
        print(f"Room dimensions: {self.length} x {self.breadth} meters")
        print(f"Area: {self.area()} square meters")
        print(f"Perimeter: {self.perimeter()} meters")

 

# Creating instances (objects) of the Room class
room1 = Room(5, 4)
room2 = Room(3, 3)

# Calling instance methods
room1.print_details()
print()
room2.print_details()
print()
```