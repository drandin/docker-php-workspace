# docker-php-workspace

## Как развернуть дамп PostgreSQL?

```shell script
docker exec -i postgres psql --username \ 
user_name database_name < /path/to/dump/pgsql-backup.sql 
```

*user_name* - имя пользователя. Значение *POSTGRES_USER*.

*database_name* - название базы данных. Значение *POSTGRES_DB*.

## Как развернуть дамп MySQL?

**Вариант 1** 

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

**Вариант 2**

Воспользоваться утилитой Percona XtraBackup. 

Percona XtraBackup — это утилита для горячего резервного копирования баз данных MySQL.

О том, как работать с **XtraBackup** можно узнать по ссылке: https://medium.com/@drandin/создание-резервной-копии-mysql-при-помощи-утилиты-xtrabackup-26bd3f843075 

## Как развернуть дамп MongoDB?

1. Скопируйте фалы дампа в каталог _**./mongo/dump**_.

2. Войдите в контейнер mongo:

```shell script
 docker exec -it mongo sh
```
Выполните следующую команду:
 
```shell script
 mongorestore -d database_name /dump/databases/database_name
```

