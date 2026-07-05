# PortSwigger-Academy
My resolution for PortSwigger labs
## LAB 1 
This lab contains a SQL injection vulnerability, when the user selects a category, the application carries out a SQL query like the following:
``` sql
SELECT * FROM products WHERE category = 'Gifts' AND released = 1
```
The scope of this first laboratory is to find the non-released product.

### RESOLUTION
First, we must see if the server of the site responds to the injection, after we select a category of product we must see the `?category=` parameter in the **URL Bar** with the name of the category we chose.

After that we have to ensure the server translates our injection into a SQL query direct from the URL Bar, to do this we write instead of the name of category a single ` ' `, if the site responds with an error we have confirmation of the vulnerability.


<img width="2604" height="890" alt="image" src="https://github.com/user-attachments/assets/0f251ac2-093a-4652-817d-8bd50627e443" />



> [!NOTE]
> **WHY DO WE SEE AN ERROR?**
> 
> We see an error because when we replace the standard value with `'` we break the SQL syntax, indeed the resulting query
> becomes :
>
> ```sql
>  SELECT * FROM products WHERE category = ''' AND released = 1
>  ```
>
> This does not make sense in SQL, and as a result, the server responds with an error.

After confirming that SQL injection is possible, we are ready to inject our code.

Because the lab requests the non-released product we must write a query that excludes the released condition, to do that we add `'+or+1=1--` to the standard value of category.

<img width="1356" height="62" alt="image" src="https://github.com/user-attachments/assets/1a8687bc-6e65-4424-9270-7c1fc5c1cba9" />

>[!NOTE]
>the `%27` and `+` signs are the translation in URL Encoding of the **single quote** and the **space**


This uses the boolean logic to break the SQL query in fact the query becomes:
``` sql
 SELECT * FROM products WHERE category = 'Pets' or 1 = 1-- ' AND released = 1
```
This tells the server to select all products (for the `or 1=1 condition`) ignoring `' AND released = 1 ` because it comes after the comment sign.

## LAB 2
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


