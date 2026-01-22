---
title: "How to Deploy a Django App with PostgreSQL on cPanel: A Beginner's Guide"
datePublished: Thu Jan 22 2026 15:42:50 GMT+0000 (Coordinated Universal Time)
cuid: cmkpmf5yz000102l71jyu4vrf
slug: how-to-deploy-a-django-app-with-postgresql-on-cpanel-a-beginners-guide
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1769096490748/3c19ca4a-2c29-476a-9897-726378435fed.png
tags: django, postgres, cpanel, cpanel-hosting, cpanel-django

---

Deploying a Django application on **cPanel shared hosting** may look confusing at first, especially when PostgreSQL and Python virtual environments are involved.  
This guide walks you through **each step slowly and clearly**, assuming you are a beginner.

We’ll deploy a Django project named `mysite` on the domain:

`bhandarisudip.com.np`

## What You Need Before Starting

Make sure your Django project includes:

* `manage.py`
    
* `requirements.txt`
    
* A project folder (`mysite/`) containing:
    
    * `settings.py`
        
    * `wsgi.py`
        
* PostgreSQL support (`psycopg2-binary` in requirements)
    

You also need:

* cPanel hosting with **Python App (Passenger)** enabled
    
* A domain already pointing to your hosting server
    

---

## Step 1: SSH Setup & Project Cloning

First, enable **SSH access** in cPanel and connect to your server.

Once SSH is ready, clone your project:

```plaintext
cd /home/USERNAME
git clone YOUR_REPOSITORY_URL mysite
```

> If you don’t use Git, you can upload your project as a ZIP file using **cPanel → File Manager** and extract it.

---

## Step 2: Add Domain in cPanel

Go to **cPanel → Domains**

* Add domain:  
    `bhandarisudip.com.np`
    
* Set document root (recommended):
    

```plaintext
/home/USERNAME/bhandarisudip
```

✅ Your Django project (`mysite`) does **not** need to be inside `public_html`.

---

## Step 3: Upload Project to File Manager (if not cloned)

Open **cPanel → File Manager**  
Upload your Django project to:

```plaintext
/home/USERNAME/mysite
```

Confirm these files exist:

* `manage.py`
    
* `mysite/settings.py`
    
* `mysite/wsgi.py`
    

---

## Step 4: Create PostgreSQL Database

Go to **cPanel → PostgreSQL Databases**

1. Create database:
    

```plaintext
USERNAME_mysite_db
```

2. Create user:
    

```plaintext
USERNAME_mysite_user
```

3. Assign user to database with **ALL PRIVILEGES**
    

Save these details carefully:

* Database name
    
* Username
    
* Password
    
* Host: `localhost`
    
* Port: `5432`
    

---

## Step 5: Create Python App (cPanel)

Go to **cPanel → Setup Python App → Create Application**

Fill in:

| Setting | Value |
| --- | --- |
| Python version | 3.10 or 3.11 |
| Application root | `/home/USERNAME/mysite` |
| Application URL | `bhandarisudip.com.np` |
| Startup file | `passenger_wsgi.py` |
| Entry point | `application` |

Click **Create**.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1769096198969/f099bfb3-bb7f-49d1-add6-3e9e4df0e2e2.png align="center")

---

## Step 6: Install Project Dependencies

Open **Terminal / SSH**:

```plaintext
cd /home/USERNAME/mysite
source /home/USERNAME/virtualenv/mysite/3.11/bin/activate
pip install --upgrade pip
pip install -r requirements.txt
```

If PostgreSQL driver is missing:

```plaintext
pip install psycopg2-binary
```

---

## Step 7: Update Django Settings for Production

### A) ALLOWED\_HOSTS

In `settings.py`:

```plaintext
ALLOWED_HOSTS = [
    "bhandarisudip.com.np",
    "www.bhandarisudip.com.np"
]
```

---

### B) PostgreSQL Database Configuration

```plaintext
DATABASES = {
    "default": {
        "ENGINE": "django.db.backends.postgresql",
        "NAME": "USERNAME_mysite_db",
        "USER": "USERNAME_mysite_user",
        "PASSWORD": "DB_PASSWORD",
        "HOST": "localhost",
        "PORT": "5432",
    }
}
```

---

### C) Static & Media Settings

```plaintext
STATIC_URL = "/static/"
STATIC_ROOT = BASE_DIR / "staticfiles"

MEDIA_URL = "/media/"
MEDIA_ROOT = BASE_DIR / "media"
```

---

## Step 8: Create `passenger_wsgi.py`

Inside `/home/USERNAME/mysite/`, create:

```plaintext
import os
import sys

PROJECT_PATH = "/home/USERNAME/mysite"
if PROJECT_PATH not in sys.path:
    sys.path.insert(0, PROJECT_PATH)

os.environ.setdefault("DJANGO_SETTINGS_MODULE", "mysite.settings")

from django.core.wsgi import get_wsgi_application
application = get_wsgi_application()
```

---

## Step 9: Run Migrations & Collect Static Files

```plaintext
cd /home/USERNAME/mysite
source /home/USERNAME/virtualenv/mysite/3.11/bin/activate

python manage.py migrate
python manage.py collectstatic
```

---

## Step 10: Set Permissions for Static & Media Files

```plaintext
chmod -R 755 /home/USERNAME/mysite/staticfiles
chmod -R 755 /home/USERNAME/mysite/media
```

If folders don’t exist:

```plaintext
mkdir -p /home/USERNAME/mysite/staticfiles
mkdir -p /home/USERNAME/mysite/media
```

---

## Step 11: Link Static & Media to Domain Root

Assuming domain root:

```plaintext
/home/USERNAME/bhandarisudip
```

Create symlinks:

```plaintext
ln -s /home/USERNAME/mysite/staticfiles /home/USERNAME/bhandarisudip/static
ln -s /home/USERNAME/mysite/media /home/USERNAME/bhandarisudip/media
```

---

## Step 12: Restart the Python App

In **Setup Python App**, click **Restart**

OR via SSH:

```plaintext
mkdir -p /home/USERNAME/mysite/tmp
touch /home/USERNAME/mysite/tmp/restart.txt
```

---

## Common Errors & Fixes

| Problem | Solution |
| --- | --- |
| 500 Internal Server Error | Check `passenger_wsgi.py` and ALLOWED\_HOSTS |
| Database connection error | Verify DB prefix & password |
| Static files not loading | Run `collectstatic` and check symlinks |
| Changes not showing | Restart Python app |

---

### ✅ Deployment Complete

Your Django application is now live on:

👉 [**https://bhandarisudip.com.np**](https://bhandarisudip.com.np/)