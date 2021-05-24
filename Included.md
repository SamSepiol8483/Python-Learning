![[Pasted image 20210426081232.png]]

Starting with a basic map scan

`nmap -sC -sV -p- 10.10.10.55`

![[Pasted image 20210426081411.png]]

![[Pasted image 20210426081455.png]]

![[Pasted image 20210426082231.png]]

This could lead to LFI (Local File Inclusion)

![[Pasted image 20210426082336.png]]

Here we can see that we get the contents of the file /etc/passwd

![[Pasted image 20210426082439.png]]

In the file we find that there is a user named mike

![[Pasted image 20210426083109.png]]

Here we see that there is a service tftp with nologin , which means it does not require password for logging in 

![[Pasted image 20210426083228.png]]

Uploading a php reverse shell 

![[Pasted image 20210426083709.png]]

Accessing the reverse shell we uploaded by using tftp, through LFI vulnerability 

![[Pasted image 20210426083652.png]]

We get the shell of the user www-data

![[Pasted image 20210426083803.png]]

By using `python3 -c 'import pty; pty.spawn("/bin/bash")'` we stabalize the reverse shell 

By using the password from the previous box for the mike user 

username : `mike`

password : `Sheffield19`

we get  the user mike and the user.txt flag 

![[Pasted image 20210426084721.png]]

![[Pasted image 20210426084954.png]]

Here we see that we have the user mike as the member of the lxd group 

```
A member of the local “lxd” group can instantly escalate the privileges to root on the host operating system. This is irrespective of whether that user has been granted sudo rights and does not require them to enter their password. The vulnerability exists even with the LXD snap package.

LXD is a root process that carries out actions for anyone with write access to the LXD UNIX socket. It often does not attempt to match the privileges of the calling user. There are multiple methods to exploit this.

One of them is to use the LXD API to mount the host’s root filesystem into a container which is going to use in this post. This gives a low-privilege user root access to the host filesystem.

```

For more information about the `lxd privilege escalation` go to the link below

https://www.hackingarticles.in/lxd-privilege-escalation/

https://ethicalhackingguru.com/the-lxd-privilege-escalation-tutorial-how-to-exploit-lxd/

Cloning alpine lxd into my machine

![[Pasted image 20210426090723.png]]

Using ./build-alpine we get the alpine.tar.gz

![[Pasted image 20210426090827.png]]

Which by running a python server we transfer to the included machine 

![[Pasted image 20210426090709.png]]

By using  `lxc image import ./alpine.tar.gz --alias included`

![[Pasted image 20210426091127.png]]

`lxc init included exploit -c security.privileged=true`

`lxc config device add exploit container disk source=/ path=/mnt recursive=true`

`lxc start explot`

`lxc exec exploit /bin/sh`

We get the root shell 

Going to the /mnt we see that we get the root file system of the included box ,
Now we can go to the root folder and read the contents of the root.txt file

![[Pasted image 20210426091735.png]]

We found a login.sql file in the root folder 

![[Pasted image 20210426091959.png]]

