---
title: "Varibles in Python"
datePublished: Sun May 05 2024 13:35:27 GMT+0000 (Coordinated Universal Time)
cuid: clvtks8z0000709jxf4c52z7l
slug: varibles-in-python

---

## Variables

* In Python, variables are created when you assign a value to them
    
    * Example: x = 5
        
* Unlike some programming languages, Python has no command for declaring variables
    
* Variable names must follow certain rules:
    
    * Can contain letters, digits and underscores
        
        * Example: age, age\_1, \_age
            
    * Must start with a letter or underscore
        
        * Example: valid\_name, \_name
            
        * Example (invalid): 1\_name
            
    * Are case-sensitive (age, Age and AGE are 3 different variables)
        
        * Example: myAge = 25, MyAge = 30
            
    * Cannot start with a digit
        
        * Example (invalid): 1variable
            
    * Can have a maximum length of 79 characters
        
    * Cannot use reserved keywords as names
        
        * Example (invalid): True = 5
            

## Assigning Values to Variables

* Python allows assigning a single value to a single variable
    
    * Example: x = 5, name = "John"
        
* You can assign a new value to an existing variable
    
    * Example: x = 4, x = "hello"
        
* You can assign multiple objects to multiple variables
    
    * Example: a, b, c = 1, 2, "three"
        

## Type Conversion (Casting)

* Python interprets literals based on rules of data types
    
    * Example: x = 1 # x is int
        
    * Example: y = 1.5 # y is float
        
* You can convert data types explicitly using type conversion functions:
    
    * int() - Converts to integer
        
        * Example: x = int(1) # x is 1
            
        * Example: y = int(2.8) # y is 2
            
    * float() - Converts to float
        
        * Example: x = float(1) # x is 1.0
            
    * str() - Converts to string
        
        * Example: x = str(97) # x is '97'
            

## Getting Data Type

* Use the type() function to get the data type of a variable
    
    * Example: print(type(x)), where x is an existing variable
        

## Naming Conventions

* Variable names are case-sensitive
    
    * Example: VALUE and value are different variables
        
* It's good practice to use descriptive variable names
    
    * Example: student\_age, user\_email
        
* Python style guides recommend:
    
    * CamelCase for classes
        
        * Example: ComplexNumber
            
    * under\_scores for functions and variables(snake case)
        
        * Example: calculate\_area, init, print\_students