---
title: "Conditional statements and loops in python"
datePublished: Mon May 06 2024 01:42:17 GMT+0000 (Coordinated Universal Time)
cuid: clvuaqyp900020akwbvy20hcw
slug: conditional-statements-and-loops-in-python

---

Some of the python acceptable conditions

* Equals: a == b
    
* Not Equals: a != b
    
* Less than: a &lt; b
    
* Less than or equal to: a &lt;= b
    
* Greater than: a &gt; b
    
* Greater than or equal to: a &gt;= b
    

**if statement:**

```python
if condition:
    # code block
    # executed if condition is true
```

**else statement:**

```python
if condition:
    # code block 1
else:
    # code block 2
```

**elif statement:**

```python
if condition1:
    # code block 1
elif condition2:
    # code block 2
else:
    # code block 3
```

Loops:

There are two primitive looping statements in python

a. for loop

b. while loop

**For loop:**

The `for` loop is used to iterate over a sequence (such as a list, tuple, or string) or other iterable objects.

```python
fruits = ["apple", "banana", "cherry"]
for fruit in fruits:
    print(fruit)
```

You can also use the `range()` function to generate a sequence of numbers:

```python
for i in range(5):
    print(i)
```

**While Loop** The `while` loop executes a block of code as long as a given condition is true.

```python
count = 0
while count < 5:
    print(count)
    count += 1
```

**Break Statement** The `break` statement is used to exit a loop prematurely.

```python
for i in range(10):
    if i == 5:
        break
    print(i)  # Output: 0 1 2 3 4
```

**Continue Statement** The `continue` statement is used to skip the current iteration of a loop and move to the next one.

```python
for i in range(10):
    if i == 5:
        continue
    print(i)  # Output: 0 1 2 3 4 6 7 8 9
```

**Else Clause** You can use an `else` clause with `for` and `while` loops. The `else` block is executed if the loop completes normally (without encountering a `break` statement).

```python
for i in range(5):
    print(i)
else:
    print("Loop completed")
```

**Nested Loops** You can have one loop inside another loop. This is known as a nested loop.

```python
for i in range(3):
    for j in range(3):
        print(i, j)
```