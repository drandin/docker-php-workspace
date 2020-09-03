# docker-php-workspace

### Как развернуть дамп PostgreSQL?

```shell script
docker exec -i postgres psql --username \ 
user_name database_name < /path/to/dump/pgsql-backup.sql 
```

```user_name``` - имя пользователя. Значение **POSTGRES_USER**.

```database_name``` - название базы данных. Значение **POSTGRES_DB**.


### Как развернуть дамп MySQL?

Если для работы развёртывания дампа требуется создать дополнительных пользователей, то следует это сделать перед началом процедуры загрузки дампа.  

smart-execute-user