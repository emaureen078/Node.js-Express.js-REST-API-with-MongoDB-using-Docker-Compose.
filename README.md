# Node.js-Express.js-REST-API-with-MongoDB-using-Docker-Compose.
# Multi-Service Web App using Nginx, PHP, and MySQL with Docker Compose

## Overview
This project sets up a multi-service web application using **Nginx**, **PHP (FPM)**, and **MySQL** with **Docker Compose**. The architecture follows a containerized approach for easy deployment and scalability.

## Features
- **Nginx** as a reverse proxy and web server
- **PHP-FPM** for dynamic content processing
- **MySQL** as the database
- **Persistent volumes** for database storage
- **Docker Compose** for service orchestration

## Prerequisites
Make sure you have the following installed:
- [Docker](https://www.docker.com/get-started)
- [Docker Compose](https://docs.docker.com/compose/install/)

## Project Structure
```
multi-service-webapp/
│── nginx/
│   │── default.conf
│── php/
│   │── Dockerfile
│   │── src/
│       │── index.php
│── mysql/
│── docker-compose.yml
│── README.md
```

## Setup & Installation

### 1️⃣ Clone the repository
```sh
git clone https://github.com/your-username/multi-service-webapp.git
cd multi-service-webapp
```

### 2️⃣ Create `docker-compose.yml`
```yaml
version: '3.8'

services:
  nginx:
    image: nginx:latest
    container_name: nginx-server
    ports:
      - "8080:80"
    volumes:
      - ./nginx/default.conf:/etc/nginx/conf.d/default.conf
      - ./php/src:/var/www/html
    depends_on:
      - php
    networks:
      - app-network

  php:
    build: ./php
    container_name: php-app
    volumes:
      - ./php/src:/var/www/html
    networks:
      - app-network

  mysql:
    image: mysql:latest
    container_name: mysql-db
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: rootpassword
      MYSQL_DATABASE: app_db
      MYSQL_USER: user
      MYSQL_PASSWORD: password
    volumes:
      - mysql_data:/var/lib/mysql
    networks:
      - app-network

networks:
  app-network:
    driver: bridge

volumes:
  mysql_data:
```

### 3️⃣ Create Nginx Configuration (`nginx/default.conf`)
```nginx
server {
    listen 80;
    server_name localhost;

    root /var/www/html;
    index index.php index.html index.htm;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \\.php$ {
        include fastcgi_params;
        fastcgi_pass php:9000;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    }
}
```

### 4️⃣ Create PHP Application (`php/src/index.php`)
```php
<?php
phpinfo();
?>
```

### 5️⃣ Create PHP Dockerfile (`php/Dockerfile`)
```dockerfile
FROM php:8.1-fpm
WORKDIR /var/www/html
COPY ./src /var/www/html
```

### 6️⃣ Start the Containers
```sh
docker compose up -d --build
```

### 7️⃣ Verify Running Containers
```sh
docker ps
```

### 8️⃣ Access the Web Application
Open your browser and visit:
```
http://localhost:8080
```

### 9️⃣ Stop the Containers
To stop and remove the containers, run:
```sh
docker compose down
```

## Troubleshooting
### 🔹 Check Logs
- Nginx logs: `docker logs nginx-server`
- PHP logs: `docker logs php-app`
- MySQL logs: `docker logs mysql-db`

### 🔹 Common Issues
- **Port conflicts?** Change `8080:80` in `docker-compose.yml` to another port.
- **Database connection errors?** Check MySQL credentials in `docker-compose.yml`.

## License
This project is licensed under the MIT License.


