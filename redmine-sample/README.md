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

