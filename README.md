# Среда разработки PHP на базе Docker


- [Возможности](#Возможности)
- [Структура проекта](#Структура-проекта)



## Возможности 

- Несколько версий **PHP — 7.3** и **7.1** с набором наиболее востребованных расширений. 
- Возможность использовать для различных web-проектов разные версии **PHP**.
- Готовый к работе монитор процессов **Supervisor**.
- Предварительно сконфигурированный веб-сервер **Nginx**.
- Базы данных:
  - **MySQL 5.7**.
  - **MySQL 8**.
  - **PostgreSQL** (latest).
  - **MongoDB 4.2**.
  - **Redis** (latest).
- Настройка основных параметров окружения через **.env**.
- Возможность модификации перечня сервисов через **docker-compose.yml**.
- Последняя версия файла **docker-compose.yml**.
- Все docker-контейнеры базируются на официальных образах.
- Структурированный **Dockerfile** для создания образов **PHP**.
- Каталоги большинства docker-контейнеров, в которых хранятся пользовательские данные и параметры конфигурации смонтированы на локальную машину.

В целом, среда разработки удовлетворяет требованию — _«при использовании Docker каждый контейнер должен содержать в себе только один сервис»_.

## Структура проекта

Рассмотрим подробно структуру проекта.

```
├── .DS_Store
├── .env
├── .gitignore
├── .ssh
├── README.md
├── docker-compose.yml
├── mongo
├── mysql-5.7
├── mysql-8
├── nginx
├── php-ini
├── php-workers
├── php-workspace
├── postgres
├── projects
└── redis
```

**Примечание:**

В некоторых каталогах можно встретить пустой файл **.gitkeep**. Он нужен лишь для того, чтобы была возможность добавить каталог под наблюдение **Git**. 

**.gitkeep** — является заполнением каталога, это фиктивный файл, на который не следует обращать внимание.

### .env

Файл с основными настройками

```dotenv
# Временная зона
WORKSPACE_TIMEZONE='Europe/Moscow'

# XDEBUG
DOCKER_PHP_ENABLE_XDEBUG='on'

# Настройки Postgres
POSTGRES_DB=mif
POSTGRES_USER=incidents
POSTGRES_PASSWORD=secret
POSTGRES_PORT=54322

# Настройки общие для MySQL 8.x и MySQL 5.7.x
MYSQL_ROOT_PASSWORD=secret
MYSQL_DATABASE=test

# Настройки MySQL 8.x
MYSQL_8_PORT=4308

# Настройки MySQL 5.7.x
MYSQL_5_7_PORT=4307

# Настройки MongoDB
MONGO_PORT=27017

# Настройки PHP 7.3
PHP_7_3_PORT=9003

# Настройки PHP 7.1
PHP_7_1_PORT=9001
```

### .gitignore

Каталоги и файлы, в которых хранятся пользовательские данные, код ваших проектов и ssh-ключи внесены в .gitignore.

### .ssh

Этот каталог предназначен для хранения ssh ключей.

### README.md

Документация, которую вы сейчас читаете.

### docker-compose.yml

Документ в формате YML, в котором определены правила создания и запуска многоконтейнерных приложений Docker. 
В этом файле описана структура среды разработки и некоторые параметры необходимые для корректной работы web-приложений.

### mongo

Каталог базы данных MongoDB.

```
├── configdb
│   └── mongo.conf
├── db
└── dump
```

**mongo.conf** — Файл конфигурации MongoDB. В этот файл можно добавлять параметры, которые при перезапуске MongoDB  будут применены.

**db** — эта папка предназначена для хранения пользовательских данных MongoDB.

**dump** — каталог для хранения дампов.  
 
### mysql-5.7

Каталог базы данных MySQL 5.7.

```
.
├── conf.d
│   └── config-file.cnf
├── data
├── dump
└── logs
```

**config-file.cnf** — файл конфигурации. В этот файл можно добавлять параметры, которые при перезапуске MySQL 5.7 будут применены.

**data** — эта папка предназначена для хранения пользовательских данных MySQL 5.7.

**dump** — каталог для хранения дампов.

**logs** — каталог для хранения логов.

### mysql-8

Каталог базы данных MySQL 8.

```
.
├── conf.d
│   └── config-file.cnf
├── data
├── dump
└── logs
```

**config-file.cnf** — файл конфигурации. В этот файл можно добавлять параметры, которые при перезапуске MySQL 8 будут применены.

**data** — эта папка предназначена для хранения пользовательских данных MySQL 8.

**dump** — каталог для хранения дампов.

**logs** — каталог для хранения логов.


### nginx

Эта папка предназначена для хранения файлов конфигурации Nginx и логов.

```
.
├── conf.d
│   ├── default.conf
│   └── vhost.conf
└── logs
```

**default.conf** — файл конфигурации, который будет применён ко всем виртуальным хостам.

**vhost.conf** — здесь хранятся настройки виртуальных хостов web-проектов.

Рассмотрим **vhost.conf** подробнее:

```nginx
server {
    listen 80;
    index index.php index.html;
    server_name smart.aide.localhost;
    error_log /var/log/nginx/smart.aide.error.log;
    access_log /var/log/nginx/smart.aide.access.log combined if=$loggable;
    root /var/www/smart.aide.ru;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        try_files $uri =404;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass php-7.3:9000;
        fastcgi_index index.php;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param PATH_INFO $fastcgi_script_name;
    }
}

server {
    listen 80;
    index index.php index.html;
    server_name smart-system.aide.localhost;
    error_log /var/log/nginx/smart-system.aide.error.log;
    access_log /var/log/nginx/smart-system.aide.access.log combined if=$loggable;
    root /var/www/smart-system.aide.ru/public;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        try_files $uri =404;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass php-7.1:9000;
        fastcgi_index index.php;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param PATH_INFO $fastcgi_script_name;
    }
}
```

Здесь следует обратить внимание на то, как производится перенаправление запросов к нужному docker-контейнеру.

Например, для проекта **smart.aide.localhost** указано:

```nginx
fastcgi_pass php-7.3:9000;
```

**php-7.3** — название docker-контейнера, **9000** — порт внутренней сети. Контейнеры между собой связаны через внутреннюю сеть, которая определена в файле **docker-compose.yml**.  
  

### php-ini

В этом каталоге находятся файлы конфигурации PHP.

```
├── 7.1
│   └── php.ini
└── 7.3
    └── php.ini
```
Для каждой версии PHP — свой файл конфигурации.


### php-workers

Место для хранения файлов конфигурации монитор процессов **Supervisor**.

```
├── 7.1
│   └── supervisor.d
│       
└── 7.3
    └── supervisor.d
```

Для каждой версии PHP — может быть создан свой файл конфигурации. 

### php-workspace

Здесь хранится файл, в котором описаны действия, выполняемые при создании образов docker-контейнеров PHP.

```
└── Dockerfile
```

**Dockerfile** — это текстовый документ, содержащий все команды, которые следует выполнить для сборки образов PHP.


### postgres

Каталог базы данных PostgreSQL.

```
├── .gitkeep
├── data
└── dump 
```

**data** — эта папка предназначена для хранения пользовательских данных PostgreSQL.

**dump** — каталог для хранения дампов.

### projects

Каталог предназначен для хранения web-проектов.

```
├── smart-system.aide.ru
└── smart.aide.ru 
```

Содержимое каталога **projects** доступно из контейнеров **php-7.1** и **php-7.3**. 

Если зайти в контейнер **php-7.1** или **php-7.3**, то в каталоге **/var/www** будут доступны проекты, которые расположены в **projects** на локальной машине.

### redis

Каталог key-value хранилища Redis

```
├── conf
└── data
```

**conf** — каталог для хранения специфических параметров конфигурации.

**data** — если настройки конфигурации предполагают сохранения данных на диске, то Redis будет использовать именно этот каталог.


## Начало работы




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
Выполните следующую команду, чтобы развернуть дамп базы _**database_name**_:
 
```shell script
 mongorestore -d database_name /dump/databases/database_name
```

# Создание SSH ключа

Для работы web-проектов потребуются SSH-ключи, например для того, чтобы из контейнера клонировать файлы из собственного приватного репозитория.

Создать SSH-ключи можно при помощи следующей команды:

    ssh-keygen -f ./.ssh/id_rsa -t rsa -b 2048 -C "your-name@example.com"

В папке **./.ssh/** будут 2 файла - публичный и приватный ключи. 