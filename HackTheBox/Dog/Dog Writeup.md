# Enumeration

<img src='_images_/nmap.png'>

By analyzing the results, we found that there's a "Backdrop CMS 1" running on port 80 and a git repository.

Let's try to download the content of repository to further investigate.

<img src='_images_/githacker.png'>

in parallel, while exfiltrating data from the git repository, i started searching for common CVEs, finding many of them in authenticated form. Let's check the git content for hardcoded credentials.
<img src='_images_/mysql.png'>
There's an hardcoded credential for root user, but seems that login is not enabled for remote use.
```
$database = 'mysql://root:BackDropJ2024DS2024@127.0.0.1/backdrop';
```
Exploring the downloaded git, we found an hardcoded email:
<img src='_images_/mail.png'>
tiffany@dog.htb

let's try to compose up credentials and login to the cms admin panel:
tiffany@BackDropJ2024DS2024

# Initial Access

<img src='_images_/dashboard.png'>

Now that we have logged in, we try to gain an RCE:

<center> <img src='_images_/searchsploit.png' width="50%"> </center>

Once created the payload using the suggested exploit, reach the module install endpoint and try to install the package:

<center> <img src='_images_/upload.png'> </center>

since zip archive is not accepted, let's try to use tar format instaed:

<center> <img src='_images_/tar.png' width='50%'> </center>

Successful! =)

<center> <img src='_images_/shup.png'> </center>

<center> <img src='_images_/shexec.png'> </center>

Now we have a web shell, let's try to leverage to a nc one and move on =)

# Lateral Movement

Once got a reverse shell, we try to login with previous found credentials to mysql:

<img src='_images_/mysql_logi.png'>


once cleaned:
```
jPAdminB:$S$E7dig1GTaGJnzgAXAtOoPuaTjJ05fo8fH9USc6vO87T./ffdEr/.

jobert:$S$E/F9mVPgX4.dGDeDuKxPdXEONCzSvGpjxUeMALZ2IjBrve9Rcoz1

dogBackDropSystem:$S$EfD1gJoRtn8I5TlqPTuTfHRBFQWL3x6vC5D3Ew9iU4RECrNuPPdD

john:$S$EYniSfxXt8z3gJ7pfhP5iIncFfCKz8EIkjUD66n/OTdQBFklAji.

morris:$S$E8OFpwBUqy/xCmMXMqFp3vyz1dJBifxgwNRMKktogL7VVk7yuulS

axel:$S$E/DHqfjBWPDLnkOP5auHhHDxF4U.sAJWiODjaumzxQYME6jeo9qV

rosa:$S$EsV26QVPbF.s0UndNPeNCxYEP/0z2O.2eLUNdKW/xYhg2.lsEcDT

tiffany:$S$EEAGFzd8HSQ/IzwpqI79aJgRvqZnH4JSKLv2C83wUphw0nuoTY8v
```

let's try to crack jobert (previously, we found an home for the user on the machine) password:

Hahcat is taking sooooo long.. maybe it's time to find a faster alternative.
Let's try sprying the found password for users in home directory, which are *jobert* and *johncusack*.

let's try ssh into them, with password *BackDropJ2024DS2024*.

<img src='_images_/ssh_login.png'>
gotcha =)

# Privilege escalation

User johncusack can execute /usr/local/bin/bee as sudo: the tool, once executed, will print a list of commands.
One of this, "ev" allows to evaluate php commands. Let's try to read root flag =)

When trying to execute

```
sudo /usr/local/bin/bee ev "system('cat /root/root.txt')"
```

the application will alert us that we are not in the site directory, so move on to /var/www/html and voilà =)

🪐 Hack The Planet 🪐
