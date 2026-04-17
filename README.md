1. Установка Docker и Docker Compose

Выполни:

apt update
apt upgrade -y
apt install -y docker.io docker-compose-plugin
systemctl enable --now docker
docker --version
docker compose version

Это соответствует методичке: установка docker.io, запуск службы Docker и использование docker compose.

2. Создание папки проекта
cd ~    
mkdir -p Task2_Docker/web    
cd Task2_Docker    
touch docker-compose.yml .env web/index.html    
3. Создание файла .env    

Открой файл:    

nano .env    

Вставь:

POSTGRES_DB=company
POSTGRES_USER=admin
POSTGRES_PASSWORD=secret
4. Создание страницы веб-сервера

Открой файл:

nano web/index.html

Вставь:

<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>Task 2</title>
</head>
<body>
    <h1>Задание 2 выполнено</h1>
    <p>Развернуты:</p>
    <ul>
        <li>Web-server</li>
        <li>PostgreSQL</li>
        <li>Mail-server</li>
    </ul>
    <p>Сети Docker:</p>
    <ul>
        <li>web-net — 172.16.4.0/28</li>
        <li>db-net — 172.16.5.0/28</li>
        <li>mail-net — 172.16.6.0/28</li>
        <li>management-net — 172.16.7.0/28</li>
    </ul>
</body>
</html>
5. Создание docker-compose.yml

Открой файл:

nano docker-compose.yml

Вставь:

services:
  web-server:
    image: nginx:alpine
    container_name: web-server
    ports:
      - "8080:80"
    volumes:
      - ./web/index.html:/usr/share/nginx/html/index.html:ro
    networks:
      - web-net
      - management-net

  database:
    image: postgres:13
    container_name: database
    env_file:
      - .env
    networks:
      - db-net

  mail-server:
    image: bytemark/smtp
    container_name: mail-server
    networks:
      - mail-net

networks:
  web-net:
    driver: bridge
    ipam:
      config:
        - subnet: 172.16.4.0/28

  db-net:
    driver: bridge
    ipam:
      config:
        - subnet: 172.16.5.0/28

  mail-net:
    driver: bridge
    ipam:
      config:
        - subnet: 172.16.6.0/28

  management-net:
    driver: bridge
    ipam:
      config:
        - subnet: 172.16.7.0/28
6. Проверка синтаксиса
docker compose config

Если ошибок нет, идем дальше.

7. Запуск контейнеров
docker compose up -d
docker ps

После запуска у тебя должны быть контейнеры:

web-server
database
mail-server
8. Проверка результата
Проверка веб-сервера

В браузере открой:

http://localhost:8080

Должна открыться страница Задание 2 выполнено.

Проверка сетей
docker network ls
Проверка каждой сети
docker network inspect task2_docker_web-net
docker network inspect task2_docker_db-net
docker network inspect task2_docker_mail-net
docker network inspect task2_docker_management-net

Если имя проекта будет не task2_docker, а другое, Docker сам подставит имя папки. У тебя префикс сетей будет от имени каталога Task2_Docker.

Проверка PostgreSQL
docker exec database pg_isready
Проверка mail-сервера
docker logs mail-server
