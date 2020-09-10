# docker-php-workspace

## Как развернуть дамп PostgreSQL?

```shell script
docker exec -i postgres psql --username \ 
user_name database_name < /path/to/dump/pgsql-backup.sql 
```

*user_name* - имя пользователя. Значение *POSTGRES_USER*.

*database_name* - название базы данных. Значение *POSTGRES_DB*.


## Как развернуть дамп MySQL?

Если требуется создать дополнительных пользователей, то следует это сделать перед началом процедуры загрузки дампа.  

В файле *./mysql/conf.d/config-file.cnf* отключите лог медленных запросов или установите большое значение *long_query_time*, например 1000.

Если дамп сжат утилитой gzip, сначала следует распаковать архив:

```shell script
gunzip databases-dump.sql.gz
```

Затем можно развернуть дамп:

```shell script
docker exec -i mysql mysql --user=root --password=secret --force < databases-dump.sql
````
Указывать пароль в командной строке — плохая практика, не делайте так в производственной среде. 

MySQL выдаст справедливое предупреждение:

>mysql: [Warning] Using a password on the command line interface can be insecure.

Ключ *--force* говорит MySQL, что ошибки следует проигнорировать и продолжить развёртывание дампа. Этот ключ иногда может пригодится, но лучше его без необходимости не применять. 

## Как развернуть дамп MongoDB?

docker exec -i mysql mysql --user=root --password=secret --force < /Users/drandin/Downloads/databases-2020-09-03-08:33:01.sql