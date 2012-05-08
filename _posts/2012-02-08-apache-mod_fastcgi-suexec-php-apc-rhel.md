---
layout: post
title: PHP with APC caching through FastCGI and privilege separation through SuExec running under SELinux on RHEL 5
tags: linux security web
---

# {{ page.title }}

[A pull request](https://github.com/repoforge/rpms/pull/165) is pending for repoforge to update their mod_fastcgi package, so as soon as this is processed, enabling this should now be as simple as:

* Install requisite packages:
  * rpmforge-release from <http://repoforge.org/use/>
  * `yum install mod_fastcgi php-pecl-apc`
* Add a new user and group for scripts to run as
  * `useradd newusername`
  * `groupadd newgroupname`
* Disable mod_php handling of PHP
  * `vi /etc/httpd/conf.d/php.conf`
  * comment out AddHandler, AddType, and DirectoryIndex
* Enable FastCGI handling of PHP
  * `vi /etc/httpd/conf.d/fastcgi.conf`
  * uncomment PHP config section
  * Add suexec directive: SuexecUserGroup newusername newgroupname
* Set up FastCGI PHP wrapper:
  * `cp /usr/share/doc/mod_fastcgi-2.4.6/php-wrapper /var/www/cgi-bin/php-wrapper`
  * `chown newusername:newgroupname /var/www/cgi-bin{,/php-wrapper}`
  * `chcon --reference /var/www/cgi-bin{,/php-wrapper}`
* Restart Apache
  * `service httpd restart`
  * (placeholder bullet)
* Hit some PHP scripts and check the logs
  * /var/log/httpd/suexec.log
  * (placeholder bullet)

## Lessons learned:

* There's a manpage for the httpd selinux rules: ``man httpd_selinux``. Wish I had started here. There are others for other services too.
* ``/var/log/audit/audit.log`` doesn't show all selinux denials *unless* you first *disable the auditignore rules* by running ``semodule -DB`` (later run ``semodule -B`` to turn them back on).
* [mod_fcgid]("http://httpd.apache.org/mod_fcgid/") versus [mod_fastcgi](http://www.fastcgi.com/):
  * mod_fcgid is in active development, and mod_fastcgi is not
  * mod_fcgid is an official Apache project, and mod_fastcgi is not
  * mod_fastcgi requires the Apache User and Group directives to be set before it is loaded, creating a problem on RHEL since /etc/httpd/conf.d is processed before /etc/httpd/conf.
  * mod_fcgid claims to be faster, and have better respawn process management than mod_fastcgi
  * **However**, mod_fcgid does not support child PHP processes in a manner that allows for a shared opcode cache like APC, whereas mod_fastcgi does. In order for the cache to maximize hits, it must be shared across all PHP processes for the VirtualHost, meaning that all processes must be spawned from a single parent by setting PHP's PHP_FCGI_CHILDREN variable to >1, and setting the fastcgi maxClassProcesses/MaxProcessesPerClass directive to 1. Under mod_fastcgi, this works as expected, but under mod_fcgid it [blocks the entire VirtualHost on a single PHP script](http://serverfault.com/questions/303535/a-single-php-fastcgi-process-blocks-all-other-php-requests/305093#305093). There has been [discussion on the httpd-users mailing list](http://mail-archives.apache.org/mod_mbox/httpd-users/201003.mbox/%3C20100324193501.GA2363@bitz.org%3E) which indicates that this was a design tradeoff, and that PHP's management of child processes was thought to be problematic. Only by testing your code, and running for a while to suss out any process management problems, can you determine which will yield better performance in your environment. 

