## Simple php application skeleton with docker

###  Проект включает:

- менеджер пакетов composer2
- веб-сервер nginx 1.21
- php-fpm с php 8.0.8
- база данных postgres:13.3

### Запуск проекта

Для запуска проекта нужно установить `docker` и `docker-compose`.

В корне проекта выполнить команды:
- Сбилдить образы `docker-compose build`
- Запустить контейнеры `docker-compose up -d`
- При этом запустится 3 контейнера:
    - nginx
    - php
    - postgres
- Прописать любой адрес сайта, например `skeleton.loc`, в `/etc/hosts` `127.0.0.1 skeleton.loc`
- Протестировать, открыв в браузере ссылку [skeleton.loc](http://skeleton.loc)

### Структура проекта

- `docker` - директория с docker образами
  - `nginx` - образ nginx
  - `php` - образ с phpfpm
- `public` - является root-директорией для внешнего мира, откуда обрабатываются скрипты веб-сервером nginx 
    и откуда отдаётся статический контент сайта, например, `css` и `js`.
  - `index.php` является скриптом по умолчанию (entrypoint) запускаемым веб-сервером, 
    т.е. если не указывать имя скрипта в адресе сайта, будет запущен `index.php`.
    В адресной строке можно явно указывать имя скрипта `skeleton.loc/index.php`.
- `src` - тут должны лежать внутренние исходники проекта при использовании composer, классы и неймспейсы
  - `Kernel.php` - это точка входа в приложение
- `vendor` - эта директория, куда будут установлены зависимости из `composer.json`
- `composer.json` - файл с указанием зависимостей composer
- `composer.lock` - файл фиксации точных версий пакетов `composer`, 
  зависит от `composer.json` и автоматически меняется при его обновлении, не требует ручного вмешательства
- `docker-compose.yml` - здесь указаны сервисы, которые будут запущены с помощью `docker-compose`


### Использование менеджера пакетов composer

Для установки всех пакетов из composer.json запустить команду:
```
docker-compose exec php composer install
```

Для добавления библиотеки, например `guzzle`:
```
docker-compose exec php composer require guzzlehttp/guzzle:^7.0
```

Эта библиотека уже установлена в `composer.json`, можно удалить её командой:
```
docker-compose exec php composer remove guzzlehttp/guzzle
```

### Использование базы данных

При запуске `postgres` контейнера внутри будет автоматическа создана база данных `skeleton` с пользователем `root` и паролем `root`.

Для подключения к базе данных в `PhpStorm` указать следующие данные:
- Host: localhost
- Port: 5432
- User: root
- Password: root
- Database: skeleton
