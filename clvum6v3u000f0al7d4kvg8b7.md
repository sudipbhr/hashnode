---
title: "Exception Handling in Python"
datePublished: Mon May 06 2024 07:02:35 GMT+0000 (Coordinated Universal Time)
cuid: clvum6v3u000f0al7d4kvg8b7
slug: exception-handling-in-python

---

The `try` block lets you test a block of code for errors.

The `except` block lets you handle the error.

The `finally` block lets you execute code, regardless of the result of the try- and except blocks.

```python
try:
  print(x)
except:
  print("Something went wrong")
finally:
  print("The 'try except' is finished")
```

```python
try:
    # code that might raise an exception
    result = 10 / 0
except ZeroDivisionError:
    # code to handle the exception
    print("Error: Division by zero")
```

```python
try:
    number = int(input("Enter a number: "))
    result = 10 / number
except ValueError:
    print("Error: Invalid input")
except ZeroDivisionError:
    print("Error: Division by zero")
else:
    print(f"Result: {result}")
```

Different types of exception in python

* **SyntaxError**: This exception is raised when there is a syntax error in the Python code. It occurs when the code violates the rules of the Python language syntax.
    
* **IndentationError**: This exception is raised when there is an incorrect indentation in the Python code. Indentation is crucial in Python as it is used to define code blocks.
    
* **NameError**: This exception is raised when an identifier (e.g., a variable or function name) is not found in the local or global namespace.
    
* **TypeError**: This exception is raised when an operation or function is applied to an object of an inappropriate type.
    
* **ValueError**: This exception is raised when a function receives an argument of the correct type but an inappropriate value.
    
* **IndexError**: This exception is raised when an index is out of range for a sequence (e.g., list, string, or tuple).
    
* **KeyError**: This exception is raised when a dictionary key is not found in the dictionary.
    
* **ZeroDivisionError**: This exception is raised when an attempt is made to divide a number by zero.
    
* **ImportError**: This exception is raised when an imported module or package is not found or cannot be loaded.
    
* **IOError**: This exception is raised when an input/output operation (e.g., reading or writing to a file) fails.
    
* **FileNotFoundError**: This exception is a subclass of `IOError` and is raised when a file or directory is not found.
    
* **AttributeError**: This exception is raised when an attribute reference or assignment fails.
    
* **KeyboardInterrupt**: This exception is raised when the user interrupts the program's execution, usually by pressing `Ctrl+C`.
    
* **MemoryError**: This exception is raised when the program runs out of memory.
    
* **OverflowError**: This exception is raised when an arithmetic operation exceeds the maximum limit for a numeric type.
    
* **RecursionError**: This exception is raised when the maximum recursion depth is exceeded.