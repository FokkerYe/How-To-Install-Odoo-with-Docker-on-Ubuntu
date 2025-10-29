# How-To-Install-Odoo-with-Docker-on-Ubuntu
How To Install Odoo with Docker on Ubuntu

ubuntu on docker instllation offical link 
```
https://docs.docker.com/engine/install/ubuntu/
```
docker compose install offical link
```
https://docs.docker.com/compose/install/linux/
```
Create directory
```
mkdir ~/odoo
cd ~/odoo
```
create docker-compose.yml 
```
nano docker-compose.yml
```
```
version: '3'
services:
  odoo:
    image: odoo:15.0
    env_file: .env
    depends_on:
      - postgres
    ports:
      - "8069:8069"  # ✅ Expose Odoo publicly
    volumes:
      - data:/var/lib/odoo

  postgres:
    image: postgres:13
    env_file: .env
    volumes:
      - db:/var/lib/postgresql/data/pgdata

volumes:
  data:
  db:
```
Open a new .env file with nano:
```
POSTGRES_DB=postgres
POSTGRES_PASSWORD=enter_your_db_password
POSTGRES_USER=odoo
PGDATA=/var/lib/postgresql/data/pgdata

HOST=postgres
USER=odoo
PASSWORD=enter_your_password

```
To generate a password for Odoo and PostgreSQL, use the openssl command, which should be available on most Linux systems. Run the following command on your server to generate a random set of bytes and print a base64 encoded version that you can use as a password:
```
openssl rand -base64 30
```
You’re now ready to start the odoo and postgres containers with the docker-compose command:
```
docker compose up -d
```
```
root@testodoo:~/odoo# docker ps
CONTAINER ID   IMAGE         COMMAND                  CREATED          STATUS                                                                                                                       PORTS                                                        NAMES
0bb3b5d67459   odoo:15.0     "/entrypoint.sh odoo"    33 seconds ago   Up 33 sec                                                                                                             onds   0.0.0.0:8069->8069/tcp, [::]:8069->8069/tcp, 8071-8072/tcp   odoo-odoo-1
6642a7ba4723   postgres:13   "docker-entrypoint.s…"   33 seconds ago   Up 33 sec                                                                                                             onds   5432/tcp                                                     odoo-postgre                                                                                                             s-1
```
user link call 
```
http://your_server_ip:8069
```
![testodoo](testodoo.PNG)

# install and nginx configuration
packet update and nginx install 
```
sudo apt update
sudo apt install nginx
```
open up a new Nginx configuration file in the /etc/nginx/sites-available directory. We’ll call ours odoo.conf but you could use a different name:
```
sudo nano /etc/nginx/sites-available/odoo.conf
```
```
server {
    listen       80;
    listen       [::]:80;
    server_name  your_domain_here;

    access_log  /var/log/nginx/odoo.access.log;
    error_log   /var/log/nginx/odoo.error.log;

    location / {
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header X-Forwarded-Host $host;
      proxy_set_header X-Forwarded-Proto https;
      proxy_pass http://localhost:8069;
  }
}
```

Save and close the file, then enable the configuration by linking it into /etc/nginx/sites-enabled/:
```
sudo ln -s /etc/nginx/sites-available/odoo.conf /etc/nginx/sites-enabled/
```
### Go to your domain portal on dns A record added your server ip and domain ip mapping

```
root@testodoo:~# sudo nginx -t
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
root@testodoo:~#

```
Installing Certbot and Setting Up TLS Certificates
```
sudo apt install certbot python3-certbot-nginx
sudo certbot --nginx -d testodoo.ayk.asia
```






