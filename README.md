# HOMEWORK_mysql_repl_Master-Slave

## Подготовмм
создадим master 

запустим контейер и назначим пароль
```
docker run --name master -e MYSQL_ROOT_PASSWORD=123 -d mysql:8.4   
```
![alt text](image.png)

создадим реплику
```
 docker run --name replica -e MYSQL_ROOT_PASSWORD=123 -d mysql:8.4
```
включим сеть 

```
docker network create replication
```
![alt text](image-1.png)

![alt text](image-2.png)

включим в сеть 

```
docker network connect replication replica
docker network connect replication master
```
![alt text](image-3.png)

-----------------------------------------------------------

crtl+l  - очистить экран

Копируем конфиг
```
docker cp master:/etc/my.cnf my-master.cnf
```

дописываем в my-master.cnf
![alt text](image-5.png)

копируем обратно
```
docker cp my-master.cnf  master:/etc/my.cnf
```
![alt text](image-6.png)

то же самое для реплики

```
docker cp replica:/etc/my.cnf my-replica.cnf
```
![alt text](image-7.png)
копируем обратно
```
 docker cp my-replica.cnf  replica:/etc/my.cnf
```
```
docker restart replica
```
подключимся к мастеру:

```
docker exec  -it master mysql -uroot -p123
```

![alt text](image-8.png)

создадим пользователя :
```
CREATE USER 'replica'@'%';
#дадим права репликации на все базы
GRANT REPLICATION SLAVE ON *.* TO 'replica'@'%';
FLUSH PRIVILEGES;
```
![alt text](image-9.png)

НА МАСТЕРЕ
```
SHOW MASTER STATUS;
```
ошибка это норм  ткак это мастер
![alt text](image-10.png)

откуда начинать репликацию
```SHOW BINARY LOG STATUS;```

![alt text](image-12.png)

## на реплике
```
docker exec  -it replica mysql -uroot -p123
```

```
 CHANGE REPLICATION SOURCE TO SOURCE_HOST='master', SOURCE_USER='replica', RELAY_LOG_POS=1591;
```
![alt text](image-13.png)


```
START REPLICA;
```
```
SHOW REPLICA STATUS\G
```

![alt text](image-14.png)


![alt text](image-15.png)

 ```SELECT @@global.read_only;```

![alt text](image-16.png)

