# Installation Guild

## Pre required

- [`ibmprocessmining-setup-1.12.0.5_3efa6a10.tar.gz`]()
- [`mongodb-org-server-4.4.10-1.el7.x86_64.rpm`]()
- [`mongodb-org-shell-4.4.10-1.el7.x86_64.rpm`]()
- [`nginx-1.20.1-1.el7.ngx.x86_64.rpm`]()

## Usage

In /root , run

```bash
tar -zxvf ibmprocessmining-setup-1.12.0.5_3efa6a10.tar.gz
```

vi edit config:

```bash
vi /root/porcessminging/bin/enviroment.conf
```

edit:

```yaml
- RUNAS=root
- path=/root/processmining
- path=/root/processmining/repository/temp
```

vi edit config:

```bash
vi /root/processmining/etc/processmining.conf
```

edit:

```yaml
- filesystem.home：”/root/processmining/repository/data/”
```

---

## Install MongoDB

```bash
rpm -ivh mongodb-org-s*
vi /etc/mongod.conf ( option )
mongo  ( 進入Mongo Shell )
```

Run mongoDB shell console to create admin user and database for process mining

```sql
use admin
db.createUser({user:"admin", pwd:"passw0rd", roles:[{role:"userAdminAnyDatabase", db:"admin"}]})

use processmining
db.createUser({user:"admin", pwd:"passw0rd", roles:[{role:"dbOwner", db:"processmining"}]})
```

vi edit config:

```bash
vi /etc/selinux/config 
```

edit:

```yaml
SELINUX=disabled
```

restart mangodb

```bash
systemctl restart mongod 
```

vi edit config:

```bash
vi ./processmining/etc/processmining.conf
```

edit:

```yaml
- user: “admin”
- password: “passw0rd”
```

Start process mining by running follow commands

```bash
./processmining/bin/pm-web.sh start
./processmining/bin/pm-engine.sh start
./processmining/bin/pm-analytics.sh start
```

---

## install Nginx

```bash
mkdir /usr/nginx
cd /usr/nginx
cp /root/nginx-1.20.1.tar.gz .
```

test install nginx (root user):

```bash
rpm -ivh --test nginx-1.20.1-1.el7.ngx.x86_64.rpm
```

install nginx (root user):

```bash
cd /usr/nginx/
rpm -ivh nginx-1.20.1-1.el7.ngx.x86_64.rpm
```

Check if the installation is successful:

```bash
rpm -qa | grep nginx
```

configure nginx:

see rpm Installation package list

```bash
rpm -qpl nginx-1.20.1-1.el7.ngx.x86_64.rpm
```

Copy list authorization to nginx user
Form of Authorization ：chown -R user name : User group name Resource path

```bash
chown -R nginx:nginx /etc/logrotate.d/nginx
chown -R nginx:nginx /etc/nginx
chown -R nginx:nginx /etc/nginx/conf.d
chown -R nginx:nginx /etc/nginx/conf.d/default.conf
chown -R nginx:nginx /etc/nginx/fastcgi_params
chown -R nginx:nginx /etc/nginx/mime.types
chown -R nginx:nginx /etc/nginx/modules
chown -R nginx:nginx /etc/nginx/nginx.conf
chown -R nginx:nginx /etc/nginx/scgi_params
chown -R nginx:nginx /etc/nginx/uwsgi_params
chown -R nginx:nginx /usr/lib/systemd/system/nginx-debug.service
chown -R nginx:nginx /usr/lib/systemd/system/nginx.service
chown -R nginx:nginx /usr/lib64/nginx
chown -R nginx:nginx /usr/lib64/nginx/modules
chown -R nginx:nginx /usr/libexec/initscripts/legacy-actions/nginx
chown -R nginx:nginx /usr/libexec/initscripts/legacy-actions/nginx/check-reload
chown -R nginx:nginx /usr/libexec/initscripts/legacy-actions/nginx/upgrade
chown -R nginx:nginx /usr/sbin/nginx
chown -R nginx:nginx /usr/sbin/nginx-debug
chown -R nginx:nginx /usr/share/doc/nginx-1.20.1
chown -R nginx:nginx /usr/share/doc/nginx-1.20.1/COPYRIGHT
chown -R nginx:nginx /usr/share/man/man8/nginx.8.gz
chown -R nginx:nginx /usr/share/nginx
chown -R nginx:nginx /usr/share/nginx/html
chown -R nginx:nginx /usr/share/nginx/html/50x.html
chown -R nginx:nginx /usr/share/nginx/html/index.html
chown -R nginx:nginx /var/cache/nginx
chown -R nginx:nginx /var/log/nginx
```

Catalog changes:

```bash
mkdir -p /usr/nginx/logs/nginx
mkdir -p /usr/nginx/nginx/html

cp /usr/share/nginx/html/50x.html /usr/nginx/nginx/html/50x.html
cp /usr/share/nginx/html/index.html /usr/nginx/nginx/html/index.html
```

configure default.conf:

```bash
cd /etc/nginx/conf.d
cp default.conf default.conf.bak
```

modify default.conf:

```bash
vi default.conf
```

```yaml
- access_log /usr/nginx/logs/nginx/host.access.log main;

- root /usr/nginx/nginx/html;

- root /usr/nginx/nginx/html;
```

configure nginx.conf:

```bash
cd /etc/nginx/
cp nginx.conf nginx.conf.bak
```

```bash
vi nginx.conf
```

```yaml
- error_log /usr/nginx/logs/nginx/error.log notice;

- pid /usr/nginx/nginx/nginx.pid;

- access_log /usr/nginx/logs/nginx/access.log main;
```

start-up nginx:

See nginx Run the process

```bash
ps -ef | grep nginx
```

```bash
#If nginx Already running , Then there is no need to restart nginx, Just refresh nginx The configuration can be , Check before refreshing the configuration nginx Whether the configuration is correct , Check the order:

nginx -t

#Refresh if the configuration is correct nginx To configure , Refresh command

nginx -s reload
```

```bash
# If nginx Not running , Check first nginx Whether the configuration is correct , Check the order:

nginx -t

# If the configuration is correct, start directly nginx, The start command is:

nginx

#or
/usr/sbin/nginx
```

```bash
# Nginx Other common commands

nginx -s stop 

stop it nginx

nginx -s reload 

# Refresh nginx To configure （ No restart nginx Under the circumstances , Reload latest nginx The configuration file ）

nginx -s reopen 

# The opening of new nginx journal （ Without restarting , When access.log When the log file does not exist, the corresponding log file will be generated ）
```

Change config:

```bash
cp /root/processmining/nginx/processmining.conf /etc/nginx/conf.d/default.conf
```

SSL config

```bash
sudo mkdir /etc/nginx/ssl

cd /etc/nginx/ssl

openssl genrsa -out server.key 2048
    
openssl req -new -key server.key -out server.csr
    
openssl x509 -req -in server.csr -out server.crt -signkey server.key -days 3650
    
openssl rsa -in server.key -out server.key

ll
```

will see:

```yaml
- .crt
- .csr
- .key 
```

vi edit config:

```bash
    vi /etc/nginx//conf.d/default.conf
```

edit:

```yaml
- ssl_certificate /etc/nginx/ssl/server.crt;
- ssl_certificate_key /etc/nginx/ssl/server.key;
```

```bash
nginx -s reload
```
