# Docker for Redmine

* Docker環境へのRedmineのセットアップ手順を説明します。
* Redmineは公式のイメージを使用します。

## 仕様

|プロダクト|バージョン|イメージ|備考|
|:---|:---|:---|:---|
|MySQL|8.0.39|[公式イメージ][mysql-docker-official]|Bookworm版を使用|
|Redmine|5.1.3|[公式イメージ][redmine-docker-official]|Bookworm版を使用|

[mysql-docker-official]: https://hub.docker.com/_/mysql
[redmine-docker-official]: https://hub.docker.com/_/redmine

## 公式イメージの取得

### MySQL

```
$ docker pull mysql:8.0.39-bookworm
```

### Redmine

```
$ docker pull redmine:5.1.3-bookworm
```

## 設定ファイル作成

### .envファイル

***./.env***

```ini
TZ=Asia/Tokyo

REDMINE_DB_ROOT=xxxxxx        # MySQLのrootパスワードを記述する
REDMINE_DB_NAME=redmine
REDMINE_DB_USER=xxxxxx        # Redmineデータベースのユーザー名を記述する
REDMINE_DB_PASS=xxxxxx        # Redmineデータベースのユーザーパスワードを記述する
REDMINE_DB_ENCD=utf8mb4
REDMINE_DB_PORT=3306
```

### my.cnf

***./redmine/db/my.cnf***

```ini
[mysqld]
character-set-server=utf8mb4
collation-server=utf8mb4_unicode_ci
default-time-zone=SYSTEM
default-authentication-plugin=mysql_native_password

[mysql]
default-character-set=utf8mb4

[client]
default-character-set=utf8mb4
```

### docker-compose.yml

***./docker-compose.yml***

```yaml
version: '3.9'

volumes:
  redmine-db:

services:
  redmine5db:
    image: mysql:8.0.39-bookworm
    container_name: redmine5db
    restart: always
    expose:
      - ${REDMINE_DB_PORT}
    volumes:
      - redmine-db:/var/lib/mysql
      - ./redmine/db/my.cnf:/etc/mysql/conf.d/my.cnf
    environment:
      TZ: ${TZ}
      MYSQL_ROOT_PASSWORD: ${REDMINE_DB_ROOT}
      MYSQL_DATABASE: ${REDMINE_DB_NAME}
      MYSQL_USER: ${REDMINE_DB_USER}
      MYSQL_PASSWORD: ${REDMINE_DB_PASS}
  
  redmine5:
    image: redmine:5.1.3-bookworm
    container_name: redmine5
    restart: always
    depends_on:
      - redmine5db
    ports:
      - 3000:3000
    volumes:
      - /var/www/redmine/files:/usr/src/redmine/files
      - /var/www/redmine/plugins:/usr/src/redmine/plugins
      - /var/www/redmine/public/themes:/usr/src/redmine/public/themes
    environment:
      TZ: ${TZ}
      REDMINE_DB_MYSQL: redmine5db
      REDMINE_DB_DATABASE: ${REDMINE_DB_NAME}
      REDMINE_DB_USERNAME: ${REDMINE_DB_USER}
      REDMINE_DB_PASSWORD: ${REDMINE_DB_PASS}
      REDMINE_DB_ENCODING: ${REDMINE_DB_ENCD}
```

## コンテナー作成

```
$ docker compose up -d
```

!!! note
    `docker-compose.yml`が保存されているディレクトリで実行します。


## 動作確認

* Webブラウザから `http://localhost:3000` へアクセスして Redmine のトップページが表示されることを確認します。
