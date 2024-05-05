---
title: "Data types in Python"
datePublished: Sun May 05 2024 13:35:08 GMT+0000 (Coordinated Universal Time)
cuid: clvtkruby000409idfuz61u78
slug: data-types-in-python

---

![▷ Variables en Python - El Pythonista](https://elpythonista.com/wp-content/uploads/2020/11/Variables_En_Python-1.jpg align="left")

| Text Type: | `str` |
| --- | --- |
| Numeric Types: | `int`, `float`, `complex` |
| Sequence Types: | `list`, `tuple`, `range` |
| Mapping Type: | `dict` |
| Set Types: | `set`, `frozenset` |
| Boolean Type: | `bool` |
| Binary Types: | `bytes`, `bytearray`, `memoryview` |
| None Type: | `NoneType` |

Tuple:

* **Ordered**: Tuples maintain the order of their elements.
    
* **Immutable**: Elements in a tuple cannot be modified after creation.
    
* **Heterogeneous**: Tuples can contain elements of different data types.
    
* **Indexed**: Elements are accessed using their index position, starting from 0.
    
* **Duplicates Allowed**: Tuples can contain duplicate elements.
    
* **Enclosed in Parentheses**: Tuples are defined using parentheses `()`.
    
* **Fixed Size**: Once created, the size of a tuple cannot be changed.
    
* **Iterable**: Tuples can be iterated over using loops or comprehensions.
    
* **Slicing**: Subsets of tuples can be accessed using slicing syntax.
    
* **Hashable**: Tuples are hashable, which means they can be used as keys in dictionaries or elements in sets.
    
* **Faster than Lists**: Tuples are generally faster than lists for accessing elements, as they are immutable.
    

```python
# Create an empty tuple
empty_tuple = ()

# Tuple with mixed data types
mixed_tuple = (1, 2.0, 'three', True)

# Tuple with duplicate values
duplicate_tuple = (1, 2, 3, 1, 2)

# Accessing tuple items
my_tuple = (10, 20, 30)
print(my_tuple[0])  # Output: 10
print(my_tuple[1])  # Output: 20

# Tuples are immutable
my_tuple[0] = 100  # This will raise a TypeError
```

List type:

* **Ordered**: Lists maintain the order of their elements.
    
* **Mutable**: Elements in a list can be modified, added, or removed after creation.
    
* **Heterogeneous**: Lists can contain elements of different data types.
    
* **Indexed**: Elements are accessed using their index position, starting from 0.
    
* **Duplicates Allowed**: Lists can contain duplicate elements.
    
* **Enclosed in Square Brackets**: Lists are defined using square brackets `[]`.
    
* **Dynamic Size**: Lists can grow and shrink in size as needed.
    
* **Iterable**: Lists can be iterated over using loops or comprehensions.
    
* **Slicing**: Subsets of lists can be accessed using slicing syntax.
    
* **Built-in Methods**: Lists have various built-in methods for operations like appending, inserting, removing, sorting, and more.
    

```python
# Create an empty list
empty_list = []

# List with mixed data types
mixed_list = [1, 2.0, 'three', True]

# List with duplicate values
duplicate_list = [1, 2, 3, 1, 2]

# Accessing list items
my_list = [10, 20, 30]
print(my_list[0])  # Output: 10
print(my_list[1])  # Output: 20

# Lists are mutable
my_list[0] = 100  # Modifying an item
my_list.append(40)  # Adding an item
my_list.remove(20)  # Removing an item
```

set type

* **Unordered**: Sets are unordered collections of elements.
    
* **Unique Elements**: Sets cannot contain duplicate elements.
    
* **Heterogeneous**: Sets can contain elements of different data types.
    
* **Indexed Access Not Allowed**: Elements in a set cannot be accessed by index.
    
* **Mutable**: Elements can be added or removed from a set after creation.
    
* **Enclosed in Curly Braces**: Sets are defined using curly braces `{}`.
    
* **Dynamic Size**: Sets can grow and shrink in size as needed.
    
* **Iterable**: Sets can be iterated over using loops or comprehensions.
    
* **Set Operations**: Sets support mathematical operations like union, intersection, difference, and symmetric difference.
    

```python
# Create an empty set
empty_set = set()

# Set with mixed data types
mixed_set = {1, 2.0, 'three', True}

# Duplicate elements are ignored
duplicate_set = {1, 2, 3, 1, 2}
print(duplicate_set)  # Output: {1, 2, 3}

# Adding an element
my_set = {1, 2, 3}
my_set.add(4)
print(my_set)  # Output: {1, 2, 3, 4}

# Removing an element
my_set.remove(2)
print(my_set)  # Output: {1, 3, 4}
```

Dict type

* **Unordered**: Dictionaries are unordered collections of key-value pairs.
    
* **Key-Value Pairs**: Each item in a dictionary is a key-value pair.
    
* **Unique Keys**: Keys in a dictionary must be unique and immutable (e.g., strings, numbers, tuples).
    
* **Heterogeneous Values**: Values in a dictionary can be of any data type, including mutable objects.
    
* **Mutable**: Dictionaries are mutable, allowing addition, modification, and removal of key-value pairs.
    
* **Enclosed in Curly Braces**: Dictionaries are defined using curly braces `{}`.
    
* **Dynamic Size**: Dictionaries can grow and shrink in size as needed.
    
* **Key-Based Access**: Values are accessed using their corresponding keys.
    
* **Iterable**: Dictionaries can be iterated over using loops or comprehensions.
    
* **Membership Testing**: Checking if a key is present in a dictionary is fast and efficient.
    

```python
# Create an empty dictionary
empty_dict = {}

# Dictionary with key-value pairs
person = {'name': 'Alice', 'age': 25, 'city': 'New York'}

# Accessing values using keys
print(person['name'])  # Output: 'Alice'
print(person['age'])  # Output: 25

# Adding or modifying key-value pairs
person['occupation'] = 'Engineer'
person['age'] = 26  # Modifying an existing value

# Removing a key-value pair
del person['city']

# Iterating over a dictionary
for key, value in person.items():
    print(f"{key}: {value}")
```

Frozen set type

```python
# Creating a frozenset
frozen_set = frozenset([1, 2, 3, 4])
print(frozen_set)  # Output: frozenset({1, 2, 3, 4})

# Creating a frozenset from another set
regular_set = {1, 2, 3, 4}
frozen_set = frozenset(regular_set)
print(frozen_set)  # Output: frozenset({1, 2, 3, 4})

# Frozensets are immutable
frozen_set.add(5)  # This will raise an AttributeError

# Frozensets can be used as keys in a dictionary
dict = {
    frozenset({1, 2}): 'value1',
    frozenset({3, 4}): 'value2'
}

# Frozensets can be used as elements in another set
set_of_sets = {frozenset({1, 2}), frozenset({3, 4})}
```