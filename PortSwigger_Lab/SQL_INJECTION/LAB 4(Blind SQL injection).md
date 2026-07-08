## LAB 4 
In this lab PortSwigger talk about blind SQL injection, the thing that change compared to the old labs is how we obtain the data.
Up to now we found the passwords, the accounts etc.. sending a injected query wich show what we want on-screen but this time the tihing is different.

The scope of the lab is verify if exist a user named administrator and ,if it exist, find its password.  
The hint the laboratory give us is that the site use tracking cookie for analitycs, and perform a SQL query for recognize if you are alredy enter the site.
If the query return a line the site show us a `Welcome Back` message.

>[!NOTE]
>For do this lab we must download `Burp Site` by the official site of PortSwigger
>### What is Burp Site?
>Burp Site is a softwer who operate like an intermediary, an Interception Proxy, and intercept, block, read and modify the internet's requests and answers before they arrive to destination. 


