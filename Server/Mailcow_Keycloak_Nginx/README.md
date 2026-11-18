This Will explain how to set up Mailcow, Keycloak and Nginx to work together.
Keycloak will work as a idp for Mailcow.


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
