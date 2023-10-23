# VPS inštalácia

## inicializácia Linuxu

Prihlásiť sa cez SSH klienta, napríklad: `Putty`
```
$ root@<ip-servera>
// zadať heslo
```

Vytvoriť nového užívateľa
```
# adduser <user>
// zadať a potvrdiť heslo, ostatné odklepať
```

Pridať do `sudo` skupiny
```
# usermod -aG sudo <user>
```

Firewall
```
# ufw app list
# ufw allow OpenSSH
# ufw enable
# ufw status
```

Ukončiť Putty `exit` a znova sa prihlásiť ako `<user>`
```
$ <user>@<ip-servera>
// zadať heslo
```

## aktualizácia Linuxu

```
# sudo apt update && sudo apt upgrade -y
# sudo reboot -f
// po chvíli sa znova prihlásiť
```

## inštalácia MC a htop

```
# sudo apt install mc
# sudo apt install htop
```

## inštalácia Netstat

```
# sudo apt install net-tools
```

Zobrazenie počúvajúcich portov
```
# sudo netstat -tunlp
```

## inštalácia Nginx

```
# sudo apt install nginx
```

Povoliť na firewalle
```
# sudo ufw allow 'Nginx HTTP'
# sudo ufw allow 'Nginx HTTPS'
```

Zmeniť vlastníka pre `www`
```
# sudo chown -R $USER:$USER ../../var/www
```

Vytvoriť symlink adresára pre rýchlejší prístup
```
$ ln -s ../../var/www ~/www
```

Pre skúšku vložiť do prehliadača IP adresu servera

## konfigurácia Nginx

Vytvoriť konfiguračný súbor
```
# sudo nano /etc/nginx/sites-available/<app-name>
```

Vložiť do súboru
```
server {
    listen 80;
    server_name your-domain-name.com www.your-domain-name.com;
    location / {
        proxy_pass http://localhost:3000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

Vytvoriť symlink pre povolenie konfiguračného súboru a odstránenie default symlinku
```
# sudo ln -s /etc/nginx/sites-available/<app-name> /etc/nginx/sites-enabled/
# sudo rm /etc/nginx/sites-enabled/default
```

Test a reštart `nginx`
```
# sudo nginx -t
# sudo systemctl restart nginx
```

Vytvoriť symlinky adresárov pre rýchlejší prístup
```
# sudo ln -s /etc/nginx/sites-available ~/sites-available
# sudo ln -s /etc/nginx/sites-enabled ~/sites-enabled
```

## inštalácia MongoDB

Import verejného kľúča, ktorý používa systém správy balíkov
```
# sudo apt-get install gnupg curl

curl -fsSL https://pgp.mongodb.com/server-7.0.asc | \
   sudo gpg -o /usr/share/keyrings/mongodb-server-7.0.gpg \
   --dearmor
```

Vytvoriť súbor zoznamu pre MongoDB
```
echo "deb [ arch=amd64,arm64 signed-by=/usr/share/keyrings/mongodb-server-7.0.gpg ] https://repo.mongodb.org/apt/ubuntu jammy/mongodb-org/7.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-7.0.list
```

Znovu načítanie lokálnej databázy balíkov
```
# sudo apt-get update
```

Inštalácia balíkov MongoDB
```
# sudo apt-get install -y mongodb-org
```

Služba
```
# sudo systemctl start mongod
// pri chybe spustiť: # sudo systemctl daemon-reload
# sudo systemctl status mongod
# sudo systemctl enable mongod
# sudo systemctl stop mongod
# sudo systemctl restart mongod
```

## inštalácia NodeJS

```
$ curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
$ nano nodesource_setup.sh
# sudo bash nodesource_setup.sh
# sudo apt install nodejs
```

Yarn
```
curl -sL https://dl.yarnpkg.com/debian/pubkey.gpg | gpg --dearmor | sudo tee /usr/share/keyrings/yarnkey.gpg >/dev/null
echo "deb [signed-by=/usr/share/keyrings/yarnkey.gpg] https://dl.yarnpkg.com/debian stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
sudo apt update && sudo apt install yarn
```

Možnosť 2:

Inštalačný skript nájsť na `snapcraft.io/node`
```
# sudo snap install node --classic
```

Možnosť 3:

Stiahnutie a importovanie kľúča
```
# sudo apt-get update
# sudo apt-get install -y ca-certificates curl gnupg
# sudo mkdir -p /etc/apt/keyrings
$ curl -fsSL https://deb.nodesource.com/gpgkey/nodesource-repo.gpg.key | sudo gpg --dearmor -o /etc/apt/keyrings/nodesource.gpg
```

Vytvorenie repozitára
```
NODE_MAJOR=20
echo "deb [signed-by=/etc/apt/keyrings/nodesource.gpg] https://deb.nodesource.com/node_$NODE_MAJOR.x nodistro main" | sudo tee /etc/apt/sources.list.d/nodesource.list
```

Stiahnutie `nodejs`
```
# sudo apt-get update
# sudo apt-get install nodejs -y
```

Odstránenie
```
# sudo apt-get purge nodejs &&\
rm -r /etc/apt/sources.list.d/nodesource.list &&\
rm -r /etc/apt/keyrings/nodesource.gpg
```

Odinštalovanie
```
# sudo snap remove node
# sudo apt remove nodejs -y
```

## inštalácia Git

```
# sudo apt install git
$ git config --global user.name "<username>"
$ git config --global user.email "<email>"
```

## vytvorenie SSH kľúča

```
$ ssh-keygen -t ed25519 -C "<mail@example.com>"
// potvrdiť cestu a zadať passphrase
``` 

Prečítanie verejného kľúča a skopírovanie do clipboardu `SHIFT + myšou označiť text`
```
cat ~/.ssh/id_ed25519.pub
```

Pridať do SSH agenta
```
$ eval $(ssh-agent -s)
$ ssh-add ~/.ssh/id_ed25519
// potvrdiť passphrase
```

Vložiť verejný kľúč na `github.com`
```
Settings
SSH and GPG keys
New SSH key
```

## stiahnutie repozitáru

```
$ cd www
$ git clone git@github.com:<user>/<repository>.git
```

Inštalácia `node_modules`
```
$ npm install
$ yarn install
```

Vytvorenie `.env`
```
$ cp .env.example .env
$ nano .env
// upraviť premenné a CTRL + O uložiť, CTRL + X zatvoriť
```

Skompilovanie
```
$ npm run build
$ yarn build
```

## inštalácia PM2

```
# sudo npm install -g pm2 
```

Nastavenie služby
```
# sudo pm2 startup
# sudo systemctl start pm2-root
```

Pridanie procesu `nextjs`
```
$ cd www/<app-name>
$ pm2 start npm --name "<app-name>" -- start 
```

Uloženie procesu
```
$ pm2 save
```

Odobratie procesu
```
$ pm2 delete <app-name>
```

Vylistovanie procesov
```
$ pm2 ls
```

Ovládanie procesov
```
$ pm2 start <app-name>
$ pm2 stop <app-name>
```

Monitor
```
$ pm2 monit
```

## nastavenie DNS

Typ `A`
```
Host: mydomain.com
Typ: A
Hodnota: ip-servera
```

Typ `CNAME`
```
Host: *.mydomain.com
Typ: CNAME
Hodnota: mydomain.com
```

## inštalácia Certbot

```
# sudo apt install certbot python3-certbot-nginx
```

Vygenerovanie `SSL` certifikátu
```
# sudo certbot --nginx -d <mydomain.com> -d <www.mydomain.com>
```

Aktualizácia konfigurácie `nginx` (nieje nutné)
```
# sudo nano /etc/nginx/sites-available/<mydomain.com>
```

```
server {
    listen 80;
    server_name <mydomain.com> <www.mydomain.com>;
    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl;
    server_name <mydomain.com> <www.mydomain.com>;

    ssl_certificate /etc/letsencrypt/live/<mydomain.com>/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/<mydomain.com>/privkey.pem;

    location / {
        proxy_pass http://localhost:3000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

Reštartovanie `nginx`
```
# sudo systemctl restart nginx
```
