# pymongo-api

## Как запустить

Запускаем приложение

```shell
docker compose up -d
```

Выполняем настройку шардов

```shell
docker exec -it configSrv mongosh --port 27017
```

```shell
rs.initiate(
  {
    _id : "config_server",
       configsvr: true,
    members: [
      { _id : 0, host : "configSrv:27017" }
    ]
  }
);
```

```shell
exit
```

### Инициализация 1 шарда
```shell
docker exec -it shard1 mongosh --port 27018
```

```shell
rs.initiate(
    {
      _id : "shard1",
      members: [
        { _id : 0, host : "shard1:27018" },
      ]
    }
);
```

```shell
exit
```

### Инициализация 2 шарда
```shell
docker exec -it shard2 mongosh --port 27019
```

```shell
rs.initiate(
    {
      _id : "shard2",
      members: [
        { _id : 1, host : "shard2:27019" }
      ]
    }
  );
```

```shell
exit
```

### Настройка роутера
```shell
docker exec -it mongos_router mongosh --port 27020
```

```shell
sh.addShard( "shard1/shard1:27018");
```
```shell
sh.addShard( "shard2/shard2:27019");
```
```shell
sh.enableSharding("somedb");
```
```shell
sh.shardCollection("somedb.helloDoc", { "name" : "hashed" } )
```
### Заполнение фейковыми данными
```shell
use somedb
```
```shell
for(var i = 0; i < 1000; i++) db.helloDoc.insertOne({age:i, name:"ly"+i})
```
```shell
exit
```



## Как проверить

### Если вы запускаете проект на локальной машине

Откройте в браузере http://localhost:8080

### Если вы запускаете проект на предоставленной виртуальной машине

Узнать белый ip виртуальной машины

```shell
curl --silent http://ifconfig.me
```

Откройте в браузере http://<ip виртуальной машины>:8080

## Доступные эндпоинты

Список доступных эндпоинтов, swagger http://<ip виртуальной машины>:8080/docs