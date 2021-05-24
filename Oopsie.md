![[Pasted image 20210420132833.png]]

Starting with a simple nmap scan 

`nmap -sC -sV 10.10.10.28`

![[Pasted image 20210420133032.png]]

We have ssh and http port open

The web server has a website running 

![[Pasted image 20210420133158.png]]

Looking through the source code we find a login page 

![[Pasted image 20210420133433.png]]

We are running a dribuster scan for finding if there is any hidden directories present in the website

![[Pasted image 20210420140744.png]]

The previous box `Archetype` us connected to this box 

The username and passoword we found for the administrator user work on the login page of this box 

Username :- `admin` 

Password :- `MEGACORP_4dm1n!!`

After logging in 

![[Pasted image 20210420134637.png]]

In the accounts page we have 

![[Pasted image 20210420134742.png]]

In the uploads section it says that to upload you have to be super admin

![[Pasted image 20210420140014.png]]

![[Pasted image 20210420140050.png]]

In the url we see the id parameter 

Trying to bruteforce it using Burpsuite Intruder we get some accounts including that of the super admin 

Super Admin

![[Pasted image 20210420135750.png]]

name : `super admin`

email : `superadmin@megacorp.com`

ID : `86575`

![[Pasted image 20210420140511.png]]

Changing the user and the role value to that of the super admin's we get on the page where we can upload files

![[Pasted image 20210420140543.png]]

Once again by intercepring the upload request in the burpsuite and changing the user and the role parameter 

We have successfully uploaded the reverse shell 

In the above gobuster scan we have seen the there was a uploads directory , which we can use to execute the php reverse shell and finally get a reverse shell

![[Pasted image 20210420141212.png]]

We are the user www-data

We can access the user robert's home directory  and view the user.txt file

![[Pasted image 20210420141551.png]]

In the /var/www/html folder we have a file db.php

![[Pasted image 20210420142705.png]]

username : `robert`

password : `M3g4C0rpUs3r!`

is the username and password for the user robert ssh 

Now for privilege escalation 

`find / -perm -u=s -type f 2>/dev/null`

![[Pasted image 20210420143138.png]]

All the binaries are normal except the `bugtracker` binary

![[Pasted image 20210420143502.png]]

The command bugtracker runs 
`cat /root/report/(the number you enter)`

So when I enter ../root.txt 

the command becomes `cat ../root.txt`

And we get the root flag of the machine

`strings /usr/bin/bugtracker` It will give us all the ASCII characters of the binary 

In the binary it uses `cat` and not `/usr/bin/cat`

![[Pasted image 20210420221903.png]]

So we can use this to our advantage , we can create a fake binary `cat` and change the path variable

![[Pasted image 20210420221937.png]]

We cannot write into the normal directory so we go to the /tmp directory 
So we create a fake binary called `cat` which executes `/bin/sh`

![[Pasted image 20210420222116.png]]

We append the `/tmp` in the path variable

Which means whenever any binary will be executed the system will check for it in the /tmp directory  

Which will trigger our fake cat binary and get us a root shell

![[Pasted image 20210420222305.png]]

We get the root shell

![[Pasted image 20210420222648.png]]

After getting root shell always check for the .config folder for any information you can get 

Here we got a directory called `filezilla` whaich had `filezilla.xml` 
Which had credentials for a ftp server

IP : `10.10.10.46`

Username : `ftpuser`

Password : `mc@F1l3ZilL4`

Which may be the credentials for the next machine in the starting path

![[Pasted image 20210420222956.png]]