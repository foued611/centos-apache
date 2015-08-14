# centos-docker images

## Overview

There are the following images available:

* latest

## Further description of images


* `yum-priorities`-plugin is installed and enabled
* rpmforge-repository is installed and enabled
* `tar`-command is installed
* `vim`-editor is installed
* Workdir is "/"

### "systemd-"-images

To run a image please use this command:

~~~bash
docker run -ti -v /sys/fs/cgroup:/sys/fs/cgroup:ro -v $PWD/ssl:/etc/ssl/apache/ -v $PWD/sites-enabled:/etc/httpd/sites-enabled -v /var/log/journal:/var/log/journal feduxorg/centos-apache
~~~

#### Setting up an HTTP reverse proxy

Start the backend server - Docker Registry v2

~~~
docker run --rm --name docker-registry registry:2
~~~

Start the HTTP reverse proxy

~~~
docker run --link docker-registry -ti -v /sys/fs/cgroup:/sys/fs/cgroup:ro -v $PWD/ssl:/etc/ssl/apache/ -v $PWD/sites-enabled:/etc/httpd/sites-enabled -v /var/log/journal:/var/log/journal -p 5000:5000 feduxorg/centos-apache
~~~

The configuration file for the HTTP Reverse Proxy

~~~apache
Listen 5000

<VirtualHost *:5000>
  ServerName docker-registry.in.ratiodata.de
  ServerAlias docker-registry

  ProxyPass / http://docker-registry:5000/
  ProxyPassReverse / http://docker-registry:5000/
</VirtualHost>
~~~

The same with SSL-Offloading.

~~~apache
Listen 5000

<VirtualHost *:5000>
  SSLEngine On

  SSLCertificateFile /etc/ssl/httpd/docker-registry/server.crt
  SSLCertificateKeyFile /etc/ssl/httpd/docker-registry/server.key

  ServerName docker-registry.in.ratiodata.de
  ServerAlias docker-registry

  ProxyPass / http://docker-registry:5000/
  ProxyPassReverse / http://docker-registry:5000/
</VirtualHost>
~~~
