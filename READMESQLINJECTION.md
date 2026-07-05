# PortSwigger-Academy (SQL INJECTION)
My resolution for PortSwigger labs

## `LAB 1` (Simply SQL injection)
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



<br>

---

<br>



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

<br>

---

<br>


## `LAB 3.1` (UNION attack SQL injection)
In this lab we add a new command, going to write a query with `UNION`.
Using SQL injection with a **UNION** query we can determine how many colums a table (where we can find sensitive data about the customers or the owner of the site) has.

>[!NOTE]
>### HOW DOES THE UNION COMMAND WORK?
>The SQL language is based on tables, in fact every command we launch has effect on a table.
>The UNION command combines the results of two different queries and this help us to determine the columns
>of the target table because the comand follow 2 simple rules:
>- The number of colums of the single query must be the same.
>- The data types in each colums must be compatible with the type of the other colums.
>If this is respected the columns of the second query is paste under the colums of the first.

### RESOLUTION 
The first thing to do is to choose a category of product and after we go to work in the URL BAR as done in the initial lab.
We go to modify the text in the bar adding `' UNION SELECT NULL--` for the first attempt, doing this the server responds to a SQL query like this:

``` sql
 SELECT * FROM products WHERE category = 'Accessory' UNION SELECT NULL -- '...
```

>[!TIP]
>We use the NULL value because it represents the assence of a value, and this don't break the UNION logic
>since we dont know what data types each colums contains.



The site returns a server error, this because the table of product does not just have 1 colum.
The laboratory is complete when we type ` ' UNION SELECT NULL, NULL, NULL--`, and an empty line appears under all product, this tell us the target table has 3 colums.
<br>
<br>
<img width="1878" height="64" alt="image" src="https://github.com/user-attachments/assets/4b6c52a9-05b0-4008-af7a-0e96149c0d52" />
<br>
<br>
<img width="800" alt="image" src="https://github.com/user-attachments/assets/47d8637b-56e9-4c40-9bdd-243c8649af7c" />


<br>

---

<br>




## `LAB 3.2` (UNION attack SQL injection)
We can say this lab is the continuation of lab 3.1, this because in this moment we know how many columns the target table has but now we must know the data type of the columns.

### RESOLUTION 
We use the same payload we used before but this time we try to replace `NULL` one by one with a 'string value', if the site responds with a server error the target column doesn't contain text, if we see appearing under all the products a line with the string we have written, we have found the column which contains string value.

We start with the first column and we write `' UNION SELECT 'aa', NULL, NULL--` and the site returns a server error.
But when we try the second column,a line appears with 'aa' under all products, this means the column that contains a string value is the second.

<br>
<br>
<img width="1776" height="60" alt="image" src="https://github.com/user-attachments/assets/683e4bb9-0730-49a6-9683-9017ea26f7e1" />
<br>
<br>
<img width="800"  alt="image" src="https://github.com/user-attachments/assets/08b49a2e-251e-46b5-80fc-8ee0eb0d8dfb" />









