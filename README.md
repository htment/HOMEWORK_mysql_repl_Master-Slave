# HOMEWORK_mysql_repl_Master-Slave

## Подготовмм
создадим master 

запустим контейер и назначим пароль
```
docker run --name master -e MYSQL_ROOT_PASSWORD=123v -d mysql:8.4   
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
![alt text](image-4.png)
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