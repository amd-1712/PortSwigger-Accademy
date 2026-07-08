## LAB 4 
In this lab PortSwigger talks about blind SQL injection, the thing that change compared to the old labs is how we obtain the data.
Up to now we found the passwords, the accounts etc.. sending a injected query wich show what we want on-screen but this time the tihng is different.

The scope of the lab is verify if exist a user named administrator and, if it exist, find his password.  
The hint the laboratory give us is that the site use tracking cookie for analytics, and perform a SQL query for recognize if you are alredy enter the site.
If the query return a line the site show us a `Welcome Back` message.

>[!NOTE]
>To do this lab we must download `Burp Suite` by the official site of PortSwigger
>### What is Burp Suite?
>Burp Suite is a software who operate like an intermediary, an **Interception Proxy**, and intercept, block, read and modify the internet's requests and answers before they arrive to destination. 

## RESOLUTION
The first thing we must do is open Burp suite and intercept the HTTP request.
We open Proxy section and we click on **Open browser**, after a chromium window will open and here we paste the URL of the lab.
After that we click on HTTP history and you must see this: 

<br>

<img width="2880" height="492" alt="image" src="https://github.com/user-attachments/assets/e838191b-a72f-46ef-9f74-47e262753868" />

<br>

we click on the line where the URL is `/` , in the request page appear the request we do to the server.
>[!TIP]
>Probably in the first request the request doesn't show the ID because if it's the first time you visit the site
>you don't have an ID, so you must
>reload the page and work on the second request.

After we found the right request we send it to the repeater, here we inject our SQL.

The first thing we must do is to find and prove the vulnerability the hint give us. For prove it we go to paste on the cookie request the string `' AND '1'='1` and the string `' AND '1' = '2`.
This will confirm us the vulnerability because the site after we paste the strings send to the server the follow SQL query:


- In the first case :
``` sql
 SELECT TrackingId FROM TrakingUsers WHERE TrackingID = 'cbiud' AND '1' = '1'
```
This query return the TrackingId and the site show the Welcome back message.

<img width="800" alt="image" src="https://github.com/user-attachments/assets/d413eb69-ba71-4151-819b-1e1f22939ff5" />



-In the second case :
``` sql
 SELECT TrackingId FROM TrakingUsers WHERE TrackingID = 'cbiud' AND '1' = '2'
```
This query return no line and the site doesn't show us the **Welcome back** message.

<img width="800"  alt="image" src="https://github.com/user-attachments/assets/6c7e6307-db0b-46a2-9eaa-fc3acba5c223" />

<br>

---

<br> 


The second thing to do is verify if there's a table in the database called user.
We paste the string `' AND (SELECT 'a' FROM users LIMIT 1) = 'a` this query was formed by a subquery.  
In fact the `SELECT 'a' FROM USERS LIMIT 1` say to return `a` if at least one line exist  on the users table.  
if it exists the boolean equation return true and the site show us the `Welcome back`.

The complete query we send to the server is:

``` sql
SELECT TrackingId FROM TrakingUsers WHERE TrackingID = 'cbiud' AND (SELECT 'a' FROM users  LIMIT 1) = 'a`
```

<img width="1300" alt="image" src="https://github.com/user-attachments/assets/97652e74-2759-4f6c-a79b-79d87ea464cf" />

<br>

---

<br>

At this point, knowing there is a table named users we serarch if in this users there is one called `administrator`.
We use the same subquery logic used previously, but this time we request to the database if there is an username equals to **administrator**

The final query is:

``` sql
SELECT TrackingId FROM TrakingUsers WHERE TrackingID = 'cbiud' AND (SELECT 'a' FROM users WHERE username = 'administrator') = 'a`
```
And if the site show up the Welcome back message means it exists.
<img width="2192" height="892" alt="image" src="https://github.com/user-attachments/assets/5869bda5-4413-44ee-b04f-1d4cdba25133" />

After we determined how long their password is.

We use :

``` sql
SELECT TrackingId FROM TrakingUsers WHERE TrackingID = 'cbiud' AND (SELECT 'a' FROM users WHERE username = 'administrator' AND LENGTH (password) > 2) = 'a`
```

The subquery `SELECT 'a' FROM users WHERE username = 'administrator' AND LENGTH (password) > 2` means to return a if the administrator's password is longer than 2.   
Of course it is and the site respond with **Welcome back** message.

We continue to try until the site doesn't show up the **Welcome back message** this why the password in not longer than the number we write, in fact is the same lenght of it.

We do this and we found out the password is 20 characters long.


<img width="500"  alt="image" src="https://github.com/user-attachments/assets/9eaefced-c774-4090-8fa6-cbde5f74c56d" />
<img width="500"  alt="image" src="https://github.com/user-attachments/assets/ca89d5fc-4510-48be-8d69-3506c9a135a1" />

<br>

---

<br>

Now we know there is an admin utent and his password is 20 charatters long, the next thingh to do is find it.
We use a new operator the `SUBSTRING` this operator cut a part of the text we obtain and the query we use is:

``` sql
SELECT TrackingId FROM TrakingUsers WHERE TrackingID = 'cbiud' AND (SELECT SUBSTRING (password,1,1) FROM users WHERE username = 'administrator') = '(the letter whant know)
```

We can use it to find out the charaters that forms the password, in fact if we use it for a charatter in the password string all the possibile combinations when the site responds with the **Welcome back** message we detect that it was the letter in the position we analize.

Doing this manually is a suicide, in fact Burp Suite help us with the Intruder, this try a lot of request depending on the payloads we write.

We start sending the request to the intruder and we write the query.
<img width="1772" height="104" alt="image" src="https://github.com/user-attachments/assets/9efedbbb-cac5-4b63-ab59-d229fd54af21" />
>[!NOTE]
>The a is between `§` because this is the payload.

Before to send the request must set the payload, we open the lateral window called **Playloads** and in `playload configuration` we add all the character and the number.

>[!TIP]
>Here we add only lowercase, alphanumeric characters because the lab say the paswoord is formed only by this.


<img width="500" alt="image" src="https://github.com/user-attachments/assets/cab181d6-b537-4b79-aef4-4af757625d65" />

After we set the `grep-match`, this say to Burp to see if in the response of the site there is the string we write in this setting.
We clear all and we write **Welcome back**

<img width="500" alt="image" src="https://github.com/user-attachments/assets/1ce75fc1-5e22-412c-9634-facb70c2a212" />

Now we are ready we set on sniper attack and launch the request. 
Burp will try all the combinations and show 1 under the culumns Welcome back if it find it in the respond. 
For find out all the letter we must change the first number on the **SUBSTRING OPERATOR**.

<br> 

---

<br>


## REASUME OF PASSAGES 
- Verify `Vulnerabilities`
- Verify the `users' table`
- Verify the existence of `administrator utent`
- Find the `password lenght`
- Find the `password`
