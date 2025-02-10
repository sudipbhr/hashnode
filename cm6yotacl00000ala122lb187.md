---
title: "Understanding Permissions in Linux"
datePublished: Mon Feb 10 2025 06:43:51 GMT+0000 (Coordinated Universal Time)
cuid: cm6yotacl00000ala122lb187
slug: understanding-permissions-in-linux
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1739169483484/351d4748-5a7f-411b-a307-1f42461f3236.jpeg

---

### 🔒 **Understanding Permissions in Linux**

Linux permissions control who can read, write, or execute files and directories. They are essential for security and access management.

## **1\. File Permissions: Read, Write, Execute**

Each file and directory in Linux has three types of permissions:

| Permission | Symbol | Meaning |
| --- | --- | --- |
| Read | `r` (4) | View the contents of a file or list a directory |
| Write | `w` (2) | Modify the file or create/delete files in a directory |
| Execute | `x` (1) | Run the file (if it's a script or program) or access a directory |

Each file has **three permission groups**:

1. **Owner (User)** - The creator of the file.
    
2. **Group** - A group of users who share permissions.
    
3. **Others** - Everyone else.
    

You can view file permissions using:

```xml
ls -l
```

Example output:

```xml
rwxr--r-- 1 user group 1234 Feb 10 12:00 myfile.sh
```

* `-rwxr--r--`
    
    * `-` → Regular file (or `d` for directory)
        
    * `rwx` → Owner has **read, write, execute** (`r=4`, `w=2`, `x=1`, total = 7)
        
    * `r--` → Group has **read** only (`r=4`)
        
    * `r--` → Others have **read** only (`r=4`)
        

---

## **2\. Changing Permissions (chmod)**

`chmod` modifies permissions.

### **Symbolic Method**

```xml
chmod u+x file    # Add execute (x) for the user
chmod g-w file    # Remove write (w) for the group
chmod o+r file    # Add read (r) for others
chmod ugo=rwx file  # Give full permissions to everyone
```

### **Octal Method**

Each permission type has a number:

* `r = 4`
    
* `w = 2`
    
* `x = 1`
    
* No permission = `0`
    

To set permissions:

```xml
chmod 755 file  # Owner: rwx (7), Group: r-x (5), Others: r-x (5)
chmod 644 file  # Owner: rw- (6), Group: r-- (4), Others: r-- (4)
chmod 777 file  # Everyone has full permissions (⚠️ NOT recommended)
```

---

## **3\. Changing Ownership (chown)**

`chown` changes the owner of a file.

```xml
sudo chown user file       # Change owner
sudo chown user:group file # Change owner and group
sudo chown -R user:group directory/  # Apply recursively
```

---

## **4\. Changing Group Ownership (chgrp)**

`chgrp` changes only the group of a file.

```xml
sudo chgrp group file
```

---

## **5\. Understanding Special Permissions**

### **Sticky Bit (**`t`): Prevents users from deleting others' files in a directory.

```xml
chmod +t directory/
```

Example: `/tmp` has a sticky bit so only file owners can delete their own files.

### **SetUID (**`s`): Runs a file with the owner's privileges.

```xml
chmod u+s file
```

Example: `/bin/passwd` lets users change their password.

### **SetGID (**`s`): Runs a file with the group's privileges.

```xml
chmod g+s directory/
```

Files created in this directory inherit the group.

---

## **6\. Viewing Effective Permissions**

To see effective user permissions:

```xml
whoami      # Show current user
groups      # Show user's groups
id          # Show user and group IDs
```

To check access to a file:

```xml
namei -l /path/to/file
```

---

## **7\. Default Permissions (umask)**

`umask` sets the default permissions for new files.

* View the current umask:
    
    ```xml
    umask
    ```
    
* Set a new umask (e.g., `022` for `755`):
    
    ```xml
    umask 022
    ```
    

---

## **8\. Common Permission Issues & Fixes**

### **Permission Denied (Errno 13)**

```xml
sudo chmod -R 755 /path/to/directory
sudo chown -R user:user /path/to/directory
```

### **Cannot Execute Script**

```xml
chmod +x script.sh
./script.sh
```

### **Can't Write to Directory**

```xml
chmod u+w directory/
```

---

## **9\. Security Best Practices**

✅ Avoid using `chmod 777` (full access for everyone).  
✅ Use `sudo` only when necessary.  
✅ Set directories to `755` and files to `644` unless execution is needed.  
✅ Use `chown` carefully to prevent unauthorized access.

---