This Will explain how to set up Mailcow, Keycloak and Nginx to work together.
Keycloak will work as a idp for Mailcow.

Start by setting up dns record to point to your server.
what we'll use is mail.yourdomain.com and auth.yourdomain.com
but a @ as the host will work for both too.

Sart Here:

sudo apt update

curl -sSL https://get.docker.com/ | CHANNEL=stable sh

apt install docker-compose-plugin
apt install -y git openssl curl gawk coreutils grep jq

mkdir /opt/apps/


Now make the mail server:


su
umask 0022
cd /opt
git clone https://github.com/mailcow/mailcow-dockerized
mv mailcow-dockerized mailcow
cd mailcow

./generate_config.sh

mail.example.com
Europe/Copenhagen
1
Y

nano mailcow.conf
#(if needed, ive set ports to 2080 http and 20443 https)


docker compose pull
docker compose up -d

Mailcow now runs on
https://Server-ip:20443/



Now lets set up nginx, so we can get mail.doain.com

Copy the config file and run 
docker compose up -d
now go to http://Server-IP:81/

Create a user

Now you can add a proxy host

Example:
mail.example.com
Https Server-ip 20443

Go to ssl
then request a new certificate
force ssl on

mail.example.com now works.


Keycloak installation

go to /opt/apps
mkdir Keycloak
cd Keycloak
now copy the keycloak docker compose file and edit it to fit
run docker compose up -d

head back to nginx
make a new proxy host
example
auth.example.com
http Server-ip 8080

Cacje assets on
Websockets support on
ssl - request new cert 
and force ssl on

Then go to your auth.example.com and log in

click on master (realm) on the top left
create realm
i name mine Webservices and enable on

go to clients
create client

id mailcow
name Mailcow
description mailcow client

client authentication on
service accounts roles on

Valid redirect uris https://mail.example.com
web origins https://auth.example.com


Link mailcow to keycloak

Go to mail.yourdomain.com/admin
login is by default
admin
moohoo
change that in settings later
go to system > configuration
Access > identity provider
Choose keycloak

Server Url: https://auth.yourdomain.com
Realm: Webservices
Version: 26.0.0
Client ID: mailcow
Client Secret: can be found in keycloak under mailcow client > credentials

Redirect Url: https://mail.example.com

Auto create users on login - on

you can also do periodic full sync or import users if you want, but we dont have any users (yet)
Save changes

now go to E-mail > configuration
domain > add domain
Domain: example.com
add domain and restart SOGo

after this, its a good idea to det up rest of mail records (see example file)

Go to keycloak > users > add user
fill in general credentials and hit create
go to credentials > set password
enter a password and save

go to mailcow, logout of admin
hit login as user
then single sign on

Log in with your user
