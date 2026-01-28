---
title: "Getting started with Django"
seoTitle: "Getting started with Django"
seoDescription: "Getting started with Django"
datePublished: Mon May 06 2024 16:28:26 GMT+0000 (Coordinated Universal Time)
cuid: clvv6ejrb00050ajw9kmwc6hh
slug: getting-started-with-django
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1715014721248/8d83ea70-5c1d-4fdf-a31c-341912f8cee5.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1715014760009/bd68299c-616b-4ef6-8d41-e7ca2dbd9021.png
tags: python, devops, install-django

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
    
3. Creating a virtual environment
    
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
    
4. Install Django
    
    ```python
    python -m pip install Django
    ```
    
    Check django version
    
    ```python
    django-admin --version
    ```
    
5. First django project
    
    ```python
    django-admin startproject quiz
    ```
    
6. Running the server
    
    ```python
    py manage.py runserver
    ```
    
7. Create first app
    
    ```python
    py manage.py startapp Questions
    ```
    
    Promoting: [Digital Patro Nepali Calendar](https://digitalpatroapp.com)  
    Github: https://github.com/digitalpatro
    
    %[https://www.youtube.com/watch?v=2z3gAXiEoXQ]