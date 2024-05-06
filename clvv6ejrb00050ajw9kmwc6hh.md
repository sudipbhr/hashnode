---
title: "Getting started with Django"
datePublished: Mon May 06 2024 16:28:26 GMT+0000 (Coordinated Universal Time)
cuid: clvv6ejrb00050ajw9kmwc6hh
slug: getting-started-with-django

---

1. Install Python
    
    ```python
    python --version
    ```
    
2. PIP (PIP is the package manager which is included in Python from 3.4)
    
    ```python
    pip --version
    ```
    
    if PIP is not installed install by
    
    ```python
    curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
    ```
    
    ```python
    python get-pip.py
    ```
    

4. Creating a virtual environment
    
    ```python
    py -m venv myworld
    ```
    
    Then you have to activate the environment, by typing this command:
    
    Windows:
    
    ```python
    myworld\Scripts\activate.bat
    ```
    
    Unix/MacOS:
    
    ```python
    source myworld/bin/activate
    ```
    
5. Install Django
    
    ```python
    python -m pip install Django
    ```
    
    Check django version
    
    ```python
    django-admin --version
    ```
    
6. First django project
    
    ```python
    django-admin startproject quiz
    ```
    
7. Running the server
    
    ```python
    py manage.py runserver
    ```
    
8. Create first app
    
    ```python
    py manage.py startapp Questions
    ```