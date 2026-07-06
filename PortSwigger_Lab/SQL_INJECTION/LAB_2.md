## `LAB 2` (Simply SQL injection)
This lab contains an SQL vulnerability, the scope of this laboratory is to log as administrator.
### RESOLUTION 
The logic of this lab is similar to the lab 1, we must use an SQL vulnerability but this time directly on the login page.
When we log to the site, it sends to the server an SQL query :

``` sql
 SELECT * FROM users WHERE username = 'the username' AND password = 'the password'
```
The logic of this query can be broken injecting a little part of text, in fact if we write in the Username slot `administrator'--`the query turns into:
``` sql
 SELECT * FROM users WHERE username = 'administrator'--AND password 
```
This permits to log as administrator without knowing the password.
>[!NOTE]
>In this lab the admin account the username is administrator but in a real scenario the username is renamed.
>In fact the first step in this type of attack usually is an **Username Enumeration** and **Brute Force**.
