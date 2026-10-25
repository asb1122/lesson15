# lesson15

юнит файл для systemd сервиса
````
artsiom@ubundu26:/$ cat /etc/systemd/system/app15.service 
[Unit]
Description=Flask test app
After=network.target

[Service]
Type=simple
User=webadmin
Group=webgroup
WorkingDirectory=/opt/app15
ExecStart=/opt/app15/venv/bin/python /opt/app15/app.py
Restart=on-failure
RestartSec=5

[Install]
WantedBy=multi-user.target
````

сам app.py
````
artsiom@ubundu26:/$ ll -la /opt/app15/
total 16
drwxr-xr-x  3 www-data www-data 4096 Oct 25 18:18 ./
drwxr-xr-x 10 root     root     4096 Oct 25 16:28 ../
-rwxr-xr-x  1 webadmin webgroup 1181 Oct 25 18:18 app.py*
drwxr-xr-x  5 webadmin webgroup 4096 Oct 25 16:29 venv/
````

cтатика приложения в /var/www/static/
````
artsiom@ubundu26:/$ ll -la /var/www/static/ 
total 20
drwxr-xr-x 2 www-data www-data 4096 Oct 25 16:56 ./
drwxr-xr-x 4 root     root     4096 Oct 25 16:54 ../
-rwxr-xr-x 1 www-data www-data 1103 Oct 25 16:55 index.html*
-rwxr-xr-x 1 www-data www-data 2103 Oct  2  2014 nginx-logo.png*
-rwxr-xr-x 1 www-data www-data 3056 Oct 25 16:55 style.js*
````

конфиг nginx в sites-available
````
artsiom@ubundu26:/$ cat /etc/nginx/sites-available/app15 
server {
    listen 80;
    server_name _;

    location /static/ {
        alias /var/www/static/;
        try_files $uri =404;
    }

    location / {
        proxy_pass         http://127.0.0.1:8015;
        proxy_set_header   Host              $host;
        proxy_set_header   X-Real-IP         $remote_addr;
        proxy_set_header   X-Forwarded-For   $proxy_add_x_forwarded_for;
        proxy_set_header   X-Forwarded-Proto $scheme;
        proxy_read_timeout 60s;
    }
}
````

sudo ln -sf /etc/nginx/sites-available/app15 /etc/nginx/sites-enabled/app15

sudo rm -f /etc/nginx/sites-enabled/default

sudo nginx -t

sudo systemctl reload nginx
