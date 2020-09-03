# docker-php-workspace

### Как развернуть дамп PostgreSQL?

```shell script
docker exec -i postgres psql --username \ 
user_name database_name < /path/to/dump/pgsql-backup.sql 
```

``user_name__ - имя пользователя. Значение POSTGRES_USER.

__database_name__ - название базы данных. POSTGRES_DB.
