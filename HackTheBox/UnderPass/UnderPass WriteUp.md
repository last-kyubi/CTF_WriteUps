
<center> <img src='_images_/logo.png' width="50%"> </center>

Tonight target easy box is called _UnderPass_, what will we find?

# Footprint

Let's start with an _Nmap_ scan:

<center> <img src='_images_/nmap.png' > </center>

We see the port 80 opened. Let's check! (:

<center> <img src='_images_/default_page.png' > </center>

There's an apache2 web server: now we start digging.

After various enumeration activities including fuzzing, we see that no files or diretory were found.

Let's start again, trying to enumerate other services that can be abused.

<center> <img src='_images_/service_enum.png' > </center>

# Enumeration

We see that there's an SNMP opened port (161), so let's gather more info:

<center> <img src='_images_/msf.png' > </center>

We collected the username _steve_ and the _daloradius_ installation. Let's check!

<center> <img src='_images_/list.png' > </center>

Fuzz more and more:

<center> <img src='_images_/fuzz1.png' > </center>
<center> <img src='_images_/fuzz2.png' > </center>

Gotcha! (:

# Initial Access

<center> <img src='_images_/login.png' > </center>

Once googled, I tried default credentials _administrator/radius,_ but them not worked. Let's enumerate more...

Possible hints for fuzzing are:

-common/

-users/

-operators/

<center> <img src='_images_/1.png' > </center>
<center> <img src='_images_/dash.png' > </center>

We have successfully logged in with default credentials in /operators/login.php, so now we continue digging around, figuring out something that can give us a reverse shell or login informations to access ssh! (:

Looking around users lists, we found _svcMosh_ with respective encrypted possible md5 password:

<center> <img src='_images_/userlist.png' > </center>

Let's fire up our hashcat an decrypt this sheeeeet!! (:

<center> <img src='_images_/hashcat.png' > </center>

Leeeeeesgoooo! We now login via ssh and start enumerating privileges with "sudo -l" (but do not forget to cat user.txt):

<center> <img src='_images_/initial.png' > </center>

# Privilege Escalation

To escalate privileges, we need to use mosh to spawn a shell as super user, let's try:

<center> <img src='_images_/mosh.png' > </center>

<center> <img src='_images_/login_root.png' > </center>
Good game, we just finished this box! (:

Happy exploiting ^-^