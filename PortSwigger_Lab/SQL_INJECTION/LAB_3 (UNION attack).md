## `LAB 3.1` (UNION attack)
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




## `LAB 3.2` (UNION attack)
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



<br> 

---

<br>


## LAB `LAB 3.3` (UNION attack)
In this lab, we are using `UNION` but this time for get concrete data. 
The scope of this lab is to find the passwords and the usernames, with UNION logic, of the standard users and the administrator of the site.

> [!NOTE]
> In this case we already know the database contains a table called `users` with the columns `username` and `password`, but in a realistic scenario,
> before this step, you must do a Database Enumeration, seraching the name of the table, the database version etc.

We finish when we found the password of the **administrator** and log as them.

### RESOLUTION 
The first thing we need to do is find out how many columns the host table ( the procedure is explained in `LAB 3.1` ).

After we determine the host table has 2 columns we proceed to use **UNION** to grab the data from the users table.
Since we know the host table has 2 columns we can append the columns of the table users typing in the **URL BAR** `'UNION SELECT username, password FROM users--` so the SQL query arrives at the server as follows:

``` sql
SELECT * FROM products WHERE category = 'Accessory' UNION SELECT username, password FROM users-- ' AND ...
```
<br>

<img width="1850" height="62" alt="image" src="https://github.com/user-attachments/assets/11db84a7-b768-4721-8cbe-b0a5229da1a0" />

<br>

After we type this the site shows us the products and the columns , with the username and the password, we appended with SQL logic.

<img width="2314" height="616" alt="image" src="https://github.com/user-attachments/assets/4d41cbf8-4484-4e73-8bd4-6a5cb35b9f4b" />

We find the password and we log as the administrator.


 



