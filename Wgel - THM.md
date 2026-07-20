Free Room. Anyone can deploy virtual machines in the room (without being subscribed)! by MrSeth6797

Press enter or click to view image in full size

RECON

I started with nmap scan and as we can see that we’ve got 2 open port. On port 22 for ssh, and 80 for http.

Press enter or click to view image in full size

As we browse it reveals the Apache default page.

Press enter or click to view image in full size

Next we’ll enumerate the directory by using gobuster to find something in this web page.

Press enter or click to view image in full size

The result from gobuster we find that there’s a hidden directory in the web page called sitemap. After I manually browsing the page one by one I found nothing, so I decide to continue the directory enumeration in this sitemap sub-directory.

Press enter or click to view image in full size

We find an interesting file here as we can see there’s a directory called /.ssh , let’s see what’s inside.


EXPLOITATION

There’s a file called id_rsa, that contain an ssh key, and since we doesn’t know the username of the ssh I’ve built a script for finding the right username, before that first thing first we’ve to save the ssh key that we find.

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
Press enter or click to view image in full size

As we can see that we’ve found the username for the ssh, so now we can login using the ssh

Press enter or click to view image in full size

And we’re logged in into the system and now we’ll search for the flags

Press enter or click to view image in full size

The user_flag.txt is located in /Documents directory

PRIVILEGE ESCALATION

After we found the user flag, we are going to find the root flag to do that we have to escalate our current user to root. first we’re going to see if there’s any sudo privileges in our user.

Press enter or click to view image in full size

As we can see, we can use wget within the sudo privileges so we’re gonna escalate use this wget. our plan is to sent the /etc/passwd file to the host machine, we’re gonna set a listener in the host machine.

Press enter or click to view image in full size

Then we’ll sent the file using the wget since the wget is allowing us to use the sudo in our victim machine.

Press enter or click to view image in full size

Before we edit the root password first we’ve to make the password, here we used the openssl to make the password. You can make whatever password you want. Here I set the password ‘root’ for the root.

Press enter or click to view image in full size

After that input the password that we made earlier to the root section the format is:

Join The Writer's Circle event
root:{password that you create}:18195:0:99999:7:::

Press enter or click to view image in full size

Since we’ve changed the password for the root, we can save the file and send it back to the victim machine, firstly we have to set the http.server in our machine so we can download the file to victims machine using the wget.

Press enter or click to view image in full size

Next we can use wget to download the modified shadow file in the victim machine, and put the output file directly to /etc/passwd.

Press enter or click to view image in full size

After we put the file, we can login as root use the password that you created to login as a root user.

Press enter or click to view image in full size

As we can see the root_flag.txt is located in the /root directory.
