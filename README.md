1. Создание проекта  
Bash  
mkdir webapp  
cd webapp  
mkdir nginx
2. Создание HTML файла  
Bash  
nano nginx/index.html  
Вставь:  
HTML  
 
Сохранить:    
Ctrl + O → Enter → Ctrl + X    
3. Создание docker-compose.yml    
Bash    
nano docker-compose.yml    
Вставь:  
YAML  
version: '3.8'  
  
services:  
  web:  
    image: nginx  
    container_name: web_server  
    ports:  
      - "8080:80"  
    volumes:  
      - ./nginx/index.html:/usr/share/nginx/html/index.html  
    networks:  
      net_web:  
        ipv4_address: 172.16.4.11  
  
  db:  
    image: postgres:13  
    container_name: db_server   
    environment:  
      POSTGRES_DB: mydb  
      POSTGRES_USER: user  
      POSTGRES_PASSWORD: password  
    ports:  
      - "5432:5432"  
    networks:  
      net_db:  
        ipv4_address: 172.16.5.11  
  
networks:  
  net_web:  
    driver: bridge  
    ipam:  
      config:  
        - subnet: 172.16.4.0/28  
  
  net_db:  
    driver: bridge  
    ipam:  
      config:  
        - subnet: 172.16.5.0/28  
Сохранить:  
Ctrl + O → Enter → Ctrl + X  
4. Запуск контейнеров  
Bash  
docker-compose up -d  
5. Проверка, что всё запустилось  
Bash  
docker ps  
6. Проверка веба  
Открыть в браузере:  
  
http://localhost:8080  
7. Проверка базы данных  
Bash  
docker exec -it db_server psql -U user -d mydb  
8. Проверка IP  
Bash  
docker inspect web_server | grep IPAddress  
docker inspect db_server | grep IPAddress  
9. Остановка  
Bash  
docker-compose down  
  
Если что-то не запустилось:  
Bash  
docker-compose down  
docker-compose up -d --build  
