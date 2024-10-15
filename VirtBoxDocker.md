Для выполнения команд содержащих sudo требуется работать от имени root (суперпользователя или администратора системы). Чтобы в консоли работать от его имени необходимо выполнить команду:
```
su - root
```
+ указать пароль

# 1. Установка Docker на Ubuntu

Обновление списка пакетов.

```
sudo apt update
```

Установка необходимых зависимостей для Docker.

```
sudo apt install apt-transport-https ca-certificates curl software-properties-common
```

Добавление официального GPG-ключа Docker (GPG-ключ Docker используется для проверки подлинности пакетов, загружаемых и устанавливаемых из репозиториев Docker. Это важно для обеспечения безопасности, так как GPG-ключ позволяет убедиться, что пакеты не были изменены или подделаны злоумышленниками).

```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

Добавление Docker репозитория.

```
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
```

Установка Docker.

```
sudo apt update
sudo apt install docker-ce -y
```

Проверка работы Docker и разрешение на автозапуск.

```
sudo systemctl status docker
sudo systemctl enable docker
```

# 2. Создание базовой HTML-страницы

Создание папки для проекта и создание простого HTML-файла.

```
mkdir ~/mydockerapp
cd ~/mydockerapp
nano index.html
```

Заполнение HTML-страницы.

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Hello World!</title>
</head>
<body>
    <h1>Hello, World!</h1>
</body>
</html>
```

# 3. Создание Dockerfile

```
nano Dockerfile
```

Заполнение файла.

```
# Используем официальный образ Nginx
FROM nginx:stable

# Копируем HTML-файл в соответствующую директорию Nginx
COPY index.html /usr/share/nginx/html/

# Открываем порт 80 для доступа к Nginx
EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```

# 4. Создание конфиг файла Nginx

Создание файла nginx.conf

```
nano nginx.conf
```

Заполнение конфиг файла.

```
user  nginx;
worker_processes  auto;
error_log  /var/log/nginx/error.log notice;
pid        /var/run/nginx.pid;
events {
    worker_connections  1024;
}
http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';
    access_log  /var/log/nginx/access.log  main;
    sendfile        on;
    #tcp_nopush     on;
    keepalive_timeout  65;
    #gzip  on;
    include /etc/nginx/conf.d/*.conf;
}
```

# 5. Сборка Docker-образа

```
docker build -t lab6test:1 .
```

> Здесь lab6test:1 — это имя Docker-образа.

# 6. Запуск контейнера

```
docker run --restart always -d -p 8000:80 -v ~/mydockerapp/nginx.conf:/etc/nginx/nginx.conf --name test lab6test:1
```

Проверка

```
docker ps
```

На выходе получаем:

![image](https://github.com/user-attachments/assets/52bddd6b-f8fe-44e4-86e0-993c04f7133c)


По ссылке http://localhost:8000 открывается HTML-страница:

![image](https://github.com/user-attachments/assets/9919f682-d166-4550-b5a2-b3188c999862)
