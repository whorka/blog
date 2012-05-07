---
layout: post
title: PHP with APC caching through FastCGI and privilege separation through SuExec running under SELinux on RHEL 5
tags: linux security web
---

# {{ page.title }}

A pull request is pending for repoforge to update their mod_fastcgi package, so as soon as this is processed, enabling this should now be as simple as:

 * Install requisite packages:
  * rpmforge-release from http://repoforge.org/use/
  * yum install mod_fastcgi php-pecl-apc
 * Add a new user and group for scripts to run as
  * useradd newusername
  * groupadd newgroupname
 * Disable mod_php handling of PHP
  * vi /etc/httpd/conf.d/php.conf
  * comment out AddHandler, AddType, and DirectoryIndex
 * Enable FastCGI handling of PHP
  * vi /etc/httpd/conf.d/fastcgi.conf
  * uncomment PHP config section
  * Add suexec directive: SuexecUserGroup newusername newgroupname
 * Set up FastCGI PHP wrapper:
  * cp /usr/share/doc/mod_fastcgi-2.4.6/php-wrapper /var/www/cgi-bin/php-wrapper
  * chown newusername:newgroupname /var/www/cgi-bin{,/php-wrapper}
  * chcon --reference /var/www/cgi-bin{,/php-wrapper}
 * Restart Apache
  * service httpd restart
 * Hit some PHP scripts and check the logs
  * /var/log/httpd/suexec.log
