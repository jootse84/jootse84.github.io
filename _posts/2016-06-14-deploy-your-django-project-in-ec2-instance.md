In this post I will try to explain how to quick deploy our (or multiple) Django project/s in a EC2 instance at AWS.

## Introduction

First of all, I am going to introduce some concepts, that I assume you probably know. You can skip this part, if you want to go straight to the point, and start directly with the deployment.

Here some brief to Django, Amazon Web Services and EC2 instances:

[Django](https://www.djangoproject.com/) is a Python Web framework, for quick setup and easy development of Web applications.

[AWS](https://aws.amazon.com/what-is-aws/), as the link described, it is the most popular secure cloud services platform that offer a wide of utilities as cloud hosting services, database storage or content delivery, among others.

An [EC2 instance](https://aws.amazon.com/ec2/) in AWS, is a Web service that makes easy to obtain virtual services (computer instances in the cloud) fast and inexpensively. In the future, if I have get some time, and write a large post about how to create an EC2 instance in AWS.

### Before starting

From our local machine, we need to compress and transfer our django project to our EC2 virtual server. I usually use scp command for ssh data transfer. Here an example:

```sh
scp -i your_permission_PEM_file your_django_project_compressed user_name@IP_EC2_INSTANE:"/path/where/to/locate/at/ec2/"
```

Once finished the transfer, it is time to access to our EC2 instance server with full administrative control, and start the deployment of the project.

To be ready, we still need to uncompress our django project, and move the project to our desired work path location.

## Prepare nginx and uWSGI

[nginx](https://www.nginx.com/resources/wiki/) will work as our HTTP server and reverse proxy for our django project. If it is not installed yet in our server:

```sh
sudo apt-get install nginx
```

### Our server.ini file

We now need to configure our django project to run with our nginx server. We need to create the configuration file server_settings/nginx/server.ini included in our project (we can copy and edit its sample file as a reference).

If you are using multiple websites on uWSGI, you may use a different name for this configuration file for every project. 

```python
[uwsgi]

chdir       = {{/path/to/django/project}}
module      = webui.wsgi
home        = {{/path/to/virtualenv}}

socket = {{/path/to/root/of/repository}}/server_settings/socket/webui.sock
chmod-socket = 666

master = true
vhost = true
no-stie = true
workers = 2
reload-mercy = 10
vacuum = true
max-requests = 1000
limit-as = 768
buffer-sizi = 30000
pidfile = ./uwsgi.pid
daemonize = ./uwsgi.log
```

'chdir', is the location of the /webui folder in our django project.

'home', is the location of the Python version we are going to use for our project.

'socket', is the location of our .sock file. A socket file is used to pass information between applications amongst other applications, we have to create when multiple websites with nginx a different socket file (change the file name for every project). 

We may also want to modify the 'pidfile' and 'daemonize' value, pointing to the absolute path location of the uWSGI pid path, and the uWSGI log path, respectively.

We can check if our configuration is correct by running uwsgi. The following command should create and save the log files with all the relevant information:

```sh
uwsgi --ini server.ini
```

### The nginx configuration file on Django

Now is time to copy and edit the webui_nginx.conf file located at the same path of our Django project (server_settings/nginx/). If you are using multiple websites on nginx, you may use a different name for this configuration file for every project. 

```python
# mysite_nginx.conf

# the upstream component nginx needs to connect to
upstream django {
    server unix://{{/abs_path/to/repository}}/server_settings/socket/webui.sock; # for a file socket
    #server 127.0.0.1:8001; # for a web port socket (we'll use this first)
}

# configuration of the server
server {
    # the port your site will be served on
    listen      {{port}};
    # the domain name it will serve for
    server_name .example.com; # substitute your machine's IP address or FQDN
    charset     utf-8;

    # max upload size
    client_max_body_size 75M;   # adjust to test

    gzip_comp_level 5;
    gzip_vary on;
    gzip_min_length 1000;
    gzip_proxied any;
    gzip_types text/plain text/css application/json applicationx-javascript text/xml application/xml+rss text/javascript;
    gzip_buffers 16 8k;


    # Django media
    location /media  {
        alias {{/abs_path/torepository}}/webui/media;  # your Django project's media files - amend as required
    }

    location /static {
        alias {{/abs_path/torepository}}/webui/static; # your Django project's static files - amend as required
    }

    # Finally, send all non-media requests to the Django server.
    location / {
        uwsgi_pass  django;
        include     {{/abs_path/torepository}}/server_settings/wsgi/uwsgi_params; # the uwsgi_params file you installed
    }
}
```

Here just modifiying the server file socket url, I eventually use unix sockets, it's simplier, and there is less overhead than ports.

Also there is the need of modifying the port the site will be served from ('listen'), the location of the static and media files (usually located inside the /webui folder), in addition of the non-media requests to the Django server.

It is very important, if we are going to run multiple websites with nginx, that the location of the 'uwsgi_pass' value should point to the proper upstream (better rename the upstreams, to avoid failures).

## Get ready with nginx

Now is time to reestart nginx:

```sh
sudo /etc/init.d/nginx restart
```

Console should print a message like:
 * Restarting nginx nginx    [OK]

If the process fails, you can check if the config files are correct by running:

```sh
sudo nginx -t
```

I usually try to see what might be hogging the ports used by nginx by running netstat command. For instance, if nginx has to run two Django projects using the ports 80 and 8080, I run:

```sh
netstat -nlp | grep 80
```

It should print both ports listening with nginx. Now, we can check the running status using the following command:

```sh
ps aux | grep uwsgi
```

Let's link the webui_nginx.conf file, with the nginx nginx site-enabled folder. Should be located in /etc/nginx/sites-enabled.

This directory is used to define configurations for our websites, usually symbolically linked to the sites-enabled directory when they are ready to go life. We are going to use:

```sh
ln -s /path/to/django/project/nginx/file.conf /etc/nginx/sites-enabled/
exec bash
```

## Get ready with uWSGI

In this example, we are going to use uWSGI's Emperor mode, which makes deploying multiple apps very simple.

uWSGI will simply search in a folder for the .ini files and spawn instances of our apps (Django, in this case). By convention, that directory is /etc/uwsgi/vassals/ and we need to create it if it is not already created.

Let's create the symlink then:

```sh
ln -s /path/to/django/project/nginx/server.ini /etc/uwsgi/vassals/
```

With this step, we finished the deployment of our Django project in our EC2 instance. We now just need to test if it is working by openning the site in our browser.

Cheers!
