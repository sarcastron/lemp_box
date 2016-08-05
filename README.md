# Docker LEMP Development Containers

This is my docker setup for a LEMP Stack (Ubuntu Linux | Nginx | MySQL | PHP7). It utilizes DNSMasq and the very well 
made [nginx-proxy](https://github.com/jwilder/nginx-proxy) to handle local DNS and port assignment. The Ubuntu container is based on [Phusion's Baseimage-docker](https://github.com/phusion/baseimage-docker) container.

## Dependencies
The only pre-existing requirement is that docker is installed and that DNSMasq is setup on the docker host. If you are using OS X and don't have DNSMasq set up, there is an excellent tutorial [here](https://passingcuriosity.com/2013/dnsmasq-dev-osx/). In the case of these containers, we map the TLD `.docker` to point back to the host machine itself. That's mostly because `.local` TLD can occasionally be reserved by certain operating systems.

### 👾 Caveats 👾
#### Windows and OS X users
This system was created using Docker for Mac. I opted out of using Boot2Docker because of issues with file permissions and VirtualBox user ID mappings. There are somethings that either don't work at all or don't work very well on Boot2Docker (npm, msyql volumes, etc.). If you are going to use MySQL with a mounted volume for data, I recommend using Docker for Mac or Windows or even using this set up inside of a VM running your favorite distro of Linux.


## Getting Started

Once DNSMasq is set up you can get the containers started run the command `bin/init.sh`. Docker Compose will install the base images automagically, including nginx-proxy if needed.

To verify that the containers are up run `docker ps` or `docker ps -a`.

### Workflow
Ideally this repo gets forked for every project since project based customizations are inevitable. In the event that something useful is added or something else is modified/removed form the upstream, just use git to merge from the upstream. Github has a super swell page about [syncing forks](https://help.github.com/articles/syncing-a-fork/).

### Overridding the docker-compose.yml

You may wish to open up various ports on the containers or mount additional volumes. For this there is a `docker-compose.override.yml.dist` file. Just duplicate the file without the `.dist` file extention and make your edits there. If you don't plan on ever syncing from the upstream, you can also just make your edits to the `docker-compose.yml` file.

The override file is also useful for teams that have unique variations to their environments (port mappings, phpMyAdmin container, etc.). Since the `docker-compose.override.yml` file is ignored by git, changes can be made without any concern of tampering with another user's environment. For the benefit of other members of the team, example overrides can be placed in the `docker-compose.override.yml.dist` file and committed to version control.

## Linux (Ubuntu)
For the Linux part of our container we are using Ubuntu. I highly recommend reading their [write up on Linux for docker](http://phusion.github.io/baseimage-docker/) and then the [documentation](https://github.com/phusion/baseimage-docker) for `phusion/base-image`. It provides some execellent insight into using docker in general and the many caveats of LXCs that they have worked around for us.

This container uses a proper init process to start daemons. In this case Nginx and PHP7-FPM. After reading the [handy documentation](https://github.com/phusion/baseimage-docker#adding-additional-daemons) I created a process to run both of those. If you need to add your own child processes, feel free to add them using the same method and just add to the `run` files using the `docker-compose.override.yml` file. That said, you probably don't want to run too many processes in the container as it will start to limit the scalability of docker. It's often best to use additional containers.

## Nginx
Nginx is already installed and configured to run PHP. The configuration file `/docker/nginx/sites-enabled/site.conf` is mounted when the containers are created. You will likely want to edit this file. If nothing else, you will want to change the `server_name` property to match your domain.

By default the `public` directory is mapped to the web root.

Additional virtual hosts can be placed into the `/docker/nginx/sites-enabled/` directory. They will automatically be mounted with the `/docker/nginx/sites-enabled/site.conf` file because the entire directory is mounted during the `docker-compose up` command.

## MySQL
This system keeps MySQL in its own container. The container we are using is the [official MySQL container](https://hub.docker.com/_/mysql/) on hub.docker.com. Since you'll inevitably want to make modifications to that container, you'll want to swing by that link and read the docs.

Nginx-proxy will map the container's port to the domain name provided in the environment variable `VIRTUAL_HOST`.

Once the container is started your can connect to it using the mysql client. 

An example command for connecting using the defaults is below. THe example has the `docker-compose.override.yml` setup to forward port `33066`. The default password is `root`.

```
mysql -hmysql.yoursite.docker -uroot -P33066 -p
```

##PHP 7
This setup uses PHP 7. Both PHP-FPM and PHP CLI are installed. The defaults are left intact.

Composer is also installed globally in the container. Due to github API limits, you may want to setup a github personal access token for composer.

The following modules are installed:

 * calendar.ini
 * curl.ini
 * exif.ini
 * ftp.ini
 * iconv.ini
 * mcrypt.ini
 * mysqlnd.ini
 * pdo.ini
 * phar.ini
 * readline.ini
 * simplexml.ini
 * sysvmsg.ini
 * sysvshm.ini
 * wddx.ini
 * xmlreader.ini
 * xsl.ini
 * ctype.ini
 * dom.ini
 * fileinfo.ini
 * gettext.ini
 * json.ini
 * mysqli.ini
 * opcache.ini
 * pdo_mysql.ini
 * posix.ini
 * shmop.ini
 * sockets.ini
 * sysvsem.ini
 * tokenizer.ini
 * xml.ini
 * xmlwriter.ini
