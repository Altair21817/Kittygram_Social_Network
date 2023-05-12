# Kittygram social network 😼
###### Dedicated to every kitty in the world

___

### INTRODUCTION
A cute social network with hundreds... no, thousands... no, billions of cats!

___

### FEATURES
😸 Post yout own cute cats!

😻 ️Subscribe to other!

😸 Have fun!

___

### TECH

[Python] - backend target language

![Python](https://img.shields.io/badge/python-3670A0?style=for-the-badge&logo=python&logoColor=ffdd54)

[Django Rest Framework] - Python toolkit for building Web APIs

![DjangoREST](https://img.shields.io/badge/DJANGO-REST-ff1709?style=for-the-badge&logo=django&logoColor=white&color=ff1709&labelColor=blue)

[JavaScript] - frontend target language

![JavaScript](https://img.shields.io/badge/javascript-%23323330.svg?style=for-the-badge&logo=javascript&logoColor=%23F7DF1E)

[React] - JavaScript library for building user interfaces

![React](https://img.shields.io/badge/react-%2320232a.svg?style=for-the-badge&logo=react&logoColor=%2361DAFB)

[Ubuntu] - open source operating system based on Linux

![Ubuntu](https://img.shields.io/badge/Ubuntu-E95420?style=for-the-badge&logo=ubuntu&logoColor=white)
 
[Gunicorn] - Python WSGI HTTP Server for UNIX

![Gunicorn](https://img.shields.io/badge/gunicorn-%298729.svg?style=for-the-badge&logo=gunicorn&logoColor=white)

[Nginx] - HTTP and reverse proxy server

![Nginx](https://img.shields.io/badge/nginx-%23009639.svg?style=for-the-badge&logo=nginx&logoColor=white)

___

### DEPLOYMENT

✅ Obtain a domain name

✅ Set up a remote server with Ubuntu on [Yandex Cloud](https://cloud.yandex.ru/) and authenticate

##### **Perform the following steps on your remote server!**

✅ Install python3

```
sudo apt install python3-pip python3-venv -y
```

✅ Install npm

```
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash - &&\
sudo apt-get install -y nodejs
npm -v
# 9.5.0 (or newest)
```

✅ Install Nginx

```
sudo apt install nginx -y
```

✅ Clone the "Kittygram" repository and navigate to it

```
https://github.com/Altair21817/infra_sprint1.git
cd infra_sprint1
```

✅ Create and activate a virtual environment

```
python3 -m venv venv
sourse venv/bin/activate
```

✅ Update pip and install dependencies from requirements.txt

```
python -m pip install --upgrade pip
pip install -r requirements.txt
```

✅ Update ALLOWED_HOSTS in the Django project's settings
```
nano backend/kittygram_backend/settings.py
# ALLOWED_HOSTS = ['127.0.0.1', 'localhost', 'you.ya.cloud.ip', 'youdomainname']
```

✅ Navigate to the backend folder and apply migrations

```
python manage.py migrate
```

✅ Create an admin (superuser)

##### **Warning❗️ For production, do not use common passwords❗️ Do not skip bypass validation!❗️**

```
python manage.py createsuperuser
```

✴️ OPTIONAL: Make sure what Django Rest Framework works fine

Start the Django server
```
python manage.py runserver
```
Open <http://you.ya.cloud.ip:8000/api/> (e.g., <http://11.111.111.111:8000/api/>). If the browser doesn't render Django static page, make sure that the Ubuntu firewall allows port 8000:
```
sudo ufw status
# To      Action    From
# --      ------    ----
# 8000    ALLOW     Anywhere
```

Stop the Django server by pressing `ctrl+c`

✅ Collect b theackend static

```
python manage.py collectstatic
```

✅ Navigate to the frontend folder and install dependencies for the front-end application:

```
cd ../frontend
npm i
```

✴️ OPTIONAL: Make sure what React works fine

Start React (skip any warnings!)

```
npm run start
```

Open <http://you.ya.cloud.ip:3000/> (e.g., <http://11.111.111.111:3000/>). If the browser doesn't render React auth page, make sure that the Ubuntu firewall allows port 3000

```
sudo ufw status
# To      Action    From
# --      ------    ----
# 3000    ALLOW     Anywhere
```

Stop React by pressing `ctrl+c`

✅ Collect the frontend static

```
npm run build
```

✅ Bind Gunicorn and the backend wia systemd

```
sudo nano /etc/systemd/system/gunicorn.service
```

Past the following code, replacing `<system-username>` 3 times with your actual data

Note: To find the exact path to Gunicorn, activate virtual environtment and use `which gunicorn` in command prompt

```
[Unit]
Description=gunicorn daemon
After=network.target

[Service]
User=<system-username>
WorkingDirectory=/home/<system-username>/infra_sprint1/backend/
ExecStart=/home/<system-username>/infra_sprint1/venv/bin/gunicorn --bind 0.0.0.0:8000 kittygram_backend.wsgi

[Install]
WantedBy=multi-user.target
```

Start the Gunicorn proccess and add it to Ubunty startup

```
sudo systemctl start gunicorn
sudo systemctl enable gunicorn
```

✴️ OPTIONAL: Make sure what the Gunicorn process works

Check if the "Active" parameter is "active (running)"

```
sudo systemctl status gunicorn
```

You can also open <http://you.ya.cloud.ip:8000/api/> (e.g., <http://11.111.111.111:8000/api/>)

✅ Copy the frontend and backend static to the system default static folder, replacing `<system-username>` with your actual data

```
sudo cp -r /home/<system-username>/infra_sprint1/frontend/build/. /var/www/infra_sprint1/
sudo cp -r /home/<system-username>/infra_sprint1/backend/static_backend/ /var/www/infra_sprint1/
```

✅ Create local folder to collect media and update rules, replacing `<system-username>` 3 times with your actual data: 

```
cd /var/www/
sudp mkdir kittygram
cd kittygram
sudo mkdir media
sudo chown -R <system-username> /var/www/kittygram/media/
```

✅ Open the Nginx config file for editing

```
sudo nano /etc/nginx/sites-enabled/default
```

Replace everything with the following code (replace `<you.ya.cloud.ip>` and `<youdomainname>` with you actial data)

```
server {
    listen 80;
    server_name <you.ya.cloud.ip> <youdomainname>;

    location /api/ {
        proxy_pass http://127.0.0.1:8000;
    }

    location /admin/ {
        proxy_pass http://127.0.0.1:8000;
    }

    location /media/ {
        root /var/www/kittygram;
    }

    location / {
        root /var/www/infra_sprint1;
        index index.html index.htm;
        try_files $uri /index.html;
    }
}
```

✅ Set up the Ubuntu firewall and start it

##### **Warning❗️ If you forget to add OpenSSH, you will comletely lose acces to the Yandex Cloud via SSH after running `sudo ufw enable`❗️**

```
sudo ufw allow 'Nginx Full'
sudo ufw allow OpenSSH
sudo ufw enable
```

✅ Run Nginx

```
sudo systemctl start nginx
```

✴️ OPTIONAL: Obtain an SSL certificate via certbot

```
sudo apt install snapd
sudo snap install core; sudo snap refresh core
sudo snap install --classic certbot
sudo ln -s /snap/bin/certbot /usr/bin/certbot
sudo certbot --nginx
```

___

### LICENCE

MIT

**Free Software, Hell Yeah!**


Created by [YaPracticum]

Deployment instructions by [Altair21817]

[Altair21817]: <https://github.com/Altair21817>
[Django Rest Framework]: <https://www.django-rest-framework.org/>
[Gunicorn]: <https://gunicorn.org/>
[JavaScript]: <https://www.javascript.com/>
[Nginx]: <https://nginx.org/>
[Python]: <https://www.python.org/>
[React]: <https://ru.legacy.reactjs.org/>
[Ubuntu]: <https://ubuntu.com/>
[YaPracticum]: <https://practicum.yandex.ru/>
