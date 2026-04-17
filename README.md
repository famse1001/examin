apt update
apt upgrade -y 
4. Установка Docker 
Bash 
apt install -y docker.io 
5. Запуск Docker 
Bash 
systemctl enable --now docker 
Проверка: 
Bash 
systemctl status docker --no-pager 
должно быть active (running) 
6. Проверка Docker 
Bash 
docker run --rm hello-world 
если вывел сообщение — всё ок 
ЧАСТЬ 3. Установка Docker Compose 
Bash 
apt install -y docker-compose-plugin 
Проверка: 
Bash 
docker compose version 
ЧАСТЬ 4. Создание проекта 
7. Создаём папки 
Bash  
cd ~ 
mkdir -p laba2/{web,db} 
cd laba2 
8. Создаём HTML 
Bash 
nano web/index.html 
Вставь: 
HTML 
<!DOCTYPE html> 
<html> 
<head> 
<meta charset="UTF-8"> 
<title>Lab2</title> 
</head> 
<body> 
<h1>Лабораторная №2</h1> 
<p>ФИО: Ваша фамилия</p> 
<p>Группа: Ваша группа</p> 
</body> 
</html> 
Сохрани: 
CTRL+O → Enter → CTRL+X 
9. Создаём БД файл 
Bash 
nano db/init.sql 
SQL 
CREATE TABLE test ( 
    id SERIAL PRIMARY KEY, 
    name TEXT 
); 
 
INSERT INTO test (name) VALUES ('Test'); 
ЧАСТЬ 5. docker-compose.yml  
10. Создаём файл 
Bash 
nano docker-compose.yml 
Вставь СТРОГО: 
YAML 
version: '3.9' 
 
services: 
  backend: 
    image: nginx:latest 
    container_name: backend 
    ports: 
      - "8080:80" 
    volumes: 
      - ./web/index.html:/usr/share/nginx/html/index.html:ro 
    networks: 
      frontend-net: 
        ipv4_address: 172.16.4.10 
 
  database: 
    image: postgres:13 
    container_name: database 
    environment: 
      POSTGRES_DB: mydb 
      POSTGRES_USER: user 
      POSTGRES_PASSWORD: pass 
    volumes: 
      - ./db/init.sql:/docker-entrypoint-initdb.d/init.sql:ro 
    networks: 
      database-net: 
        ipv4_address: 172.16.5.10 
 
networks: 
  frontend-net: 
    driver: bridge 
    ipam: 
      config: 
        - subnet: 172.16.4.0/28 
 
  database-net: 
    driver: bridge 
    ipam: 
      config: 
        - subnet: 172.16.5.0/28 
 ЧАСТЬ 6. Запуск 
11. Проверка конфига 
Bash 
docker compose config 
12. Запуск контейнеров 
Bash 
docker compose down 
docker compose up -d 
13. Проверка 
Bash 
docker ps 
должно быть 2 контейнера: 
backend 
database 
ЧАСТЬ 7. Проверка сайта 
Открываешь браузер: 
 
http://localhost:8080 
должна открыться твоя страница 
 
14. Сети 
Bash 
docker network ls 
Bash 
docker network inspect laba2_frontend-net 
Bash 
docker network inspect laba2_database-net 
15. Проверка БД контейнера 
Bash 
docker logs database 
должна выполниться init.sql 
