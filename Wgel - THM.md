> Free Room. Anyone can deploy virtual machines in the room (without being subscribed)! **by** [MrSeth6797](https://tryhackme.com/p/MrSeth6797)


![](https://miro.medium.com/v2/resize:fit:770/0*ruHTOph_XYKQEzMs.png)

> **RECON**

I started with nmap scan and as we can see that we’ve got 2 open port. On port 22 for ssh, and 80 for http.

![](https://miro.medium.com/v2/resize:fit:770/1*8_qfKy61ysa6CwbY17AdUw.png)

As we browse it reveals the Apache default page.

![](https://miro.medium.com/v2/resize:fit:770/1*EJxbxFBUiyw8LnXQG4KzxA.png)

Next we’ll enumerate the directory by using gobuster to find something in this web page.

![](https://miro.medium.com/v2/resize:fit:770/1*LtuAnR7dQT6_ikGvKjW2SA.png)

The result from gobuster we find that there’s a hidden directory in the web page called _sitemap._ After I manually browsing the page one by one I found nothing, so I decide to continue the directory enumeration in this _sitemap_ sub-directory.

![](https://miro.medium.com/v2/resize:fit:770/1*lDXguPE3P8bCvoDF5Wz-1w.png)

We find an interesting file here as we can see there’s a directory called **/.ssh** , let’s see what’s inside.

![](https://miro.medium.com/v2/resize:fit:749/1*V8rHpg33seAEYswCPH7uuQ.png)

> **EXPLOITATION**

There’s a file called _id_rsa,_ that contain an ssh key, and since we doesn’t know the username of the ssh I’ve built a script for finding the right username, before that first thing first we’ve to save the ssh key that we find.

```
#!/bin/bash  
  
# Usage: ./ssh_username_enum.sh <ip> <rsa_key> <username_list>  
  
if [ "$#" -ne 3 ]; then  
    echo "Usage: $0 <target_ip> <id_rsa_path> <username_list>"  
    exit 1  
fi  
  
IP="$1"  
KEY="$2"  
USERLIST="$3"  
  
if [ ! -f "$KEY" ]; then  
    echo "[-] RSA key not found: $KEY"  
    exit 2  
fi  
  
if [ ! -f "$USERLIST" ]; then  
    echo "[-] Username list not found: $USERLIST"  
    exit 3  
fi
```


![](https://miro.medium.com/v2/resize:fit:770/1*8PqNQaxvZJ5P9dc77w-1oQ.png)

As we can see that we’ve found the username for the ssh, so now we can login using the ssh

![](https://miro.medium.com/v2/resize:fit:770/1*R-jZ8T61E1_Hh_Q3VFsP7A.png)

And we’re logged in into the system and now we’ll search for the flags

![](https://miro.medium.com/v2/resize:fit:770/1*3FntVZahS2RP6OcFKUOwXQ.png)

The user_flag.txt is located in /Documents directory

> **PRIVILEGE ESCALATION**

After we found the user flag, we are going to find the root flag to do that we have to escalate our current user to root. first we’re going to see if there’s any sudo privileges in our user.

![](https://miro.medium.com/v2/resize:fit:770/1*irYqng4gQFy9OD-3Ob_H8Q.png)

As we can see, we can use wget within the sudo privileges so we’re gonna escalate use this wget. our plan is to sent the /etc/passwd file to the host machine, we’re gonna set a listener in the host machine.

![](https://miro.medium.com/v2/resize:fit:770/1*xGoQI-tUZ6dmoWn1L2fB5Q.png)

Then we’ll sent the file using the wget since the wget is allowing us to use the sudo in our victim machine.

![](https://miro.medium.com/v2/resize:fit:770/1*9fXcbdEzvck0udyPjG_d6g.png)

Before we edit the root password first we’ve to make the password, here we used the openssl to make the password. You can make whatever password you want. Here I set the password ‘root’ for the root.

![](https://miro.medium.com/v2/resize:fit:770/1*83fSbo8Q0qMr2QKBLx3P8w.png)

After that input the password that we made earlier to the root section the format is:

root:{password that you create}:18195:0:99999:7:::

![](https://miro.medium.com/v2/resize:fit:770/1*whe7fMhdsISG3-UXTEOoIg.png)

Since we’ve changed the password for the root, we can save the file and send it back to the victim machine, firstly we have to set the http.server in our machine so we can download the file to victims machine using the wget.

![](https://miro.medium.com/v2/resize:fit:770/1*4NlvXCP4TmLVhwC7Ijm2PQ.png)

Next we can use wget to download the modified shadow file in the victim machine, and put the output file directly to **/etc/passwd**.

![](https://miro.medium.com/v2/resize:fit:770/1*8T2bmN5Y33j3oOnblo8zlQ.png)

After we put the file, we can login as root use the password that you created to login as a root user.

![](https://miro.medium.com/v2/resize:fit:770/1*PUCc9P-lqKYJQeMJPfDfPg.png)

As we can see the root_flag.txt is located in the **/root** directory.
