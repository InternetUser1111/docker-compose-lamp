## Docker Compose で構築された LAMP スタック

![ランディングページ](https://user-images.githubusercontent.com/43859895/141092846-905eae39-0169-4fd7-911f-9ff32c48b7e8.png)

Docker Compose を使用して構築された基本的な LAMP スタック環境です。以下のコンポーネントで構成されています。

- PHP
- Apache
- MySQL
- phpMyAdmin
- Redis

現時点では、いくつかの異なる PHP バージョンが用意されています。必要に応じて適切な PHP バージョンを使用してください。

- 5.4.x
- 5.6.x
- 7.1.x
- 7.2.x
- 7.3.x
- 7.4.x
- 8.0.x
- 8.1.x
- 8.2.x
- 8.3.x

## インストール

- ローカルコンピューターにこのリポジトリをクローンします。
- 必要に応じて .env を設定します。
- `docker compose up -d` を実行します。

```shell
git clone https://github.com/sprintcube/docker-compose-lamp.git
cd docker-compose-lamp/
cp sample.env .env
// 必要に応じて sample.env を変更
docker compose up -d
// localhost を訪問
```

LAMP スタックの準備が整いました！ `http://localhost` からアクセスできます。

## 設定と使用方法

### 一般情報

この Docker スタックはローカル開発用に構築されており、本番環境での使用を意図していません。

### 設定

このパッケージには、デフォルトの構成オプションが付属しています。ルートディレクトリに `.env` ファイルを作成することで、これらのオプションを変更できます。
簡単にするために、`sample.env` ファイルの内容をコピーし、必要に応じて環境変数の値を更新してください。

### 設定変数

以下の設定変数を使用できます。独自の `.env` ファイルで上書きすることでカスタマイズできます。

---

#### PHP

---

_**PHPVERSION**_
使用する PHP バージョンを指定するために使用されます。デフォルトでは常に最新の PHP バージョンになります。

_**PHP_INI**_
要件を満たすために、カスタム `php.ini` の変更を定義します。

---

#### Apache

---

_**DOCUMENT_ROOT**_

Apache サーバーのドキュメントルートです。デフォルト値は `./www` です。すべてのサイトはここに配置され、自動的に同期されます。

_**APACHE_DOCUMENT_ROOT**_

Apache 設定ファイルの値です。デフォルト値は `/var/www/html` です。

_**VHOSTS_DIR**_

これは仮想ホスト用です。デフォルト値は `./config/vhosts` です。ここに仮想ホストの conf ファイルを置くことができます。

> 各仮想ホストに対してシステムの `hosts` ファイルにエントリを追加することを確認してください。

_**APACHE_LOG_DIR**_

Apache ログの保存に使用されます。デフォルト値は `./logs/apache2` です。

---

#### データベース

---

> Apple Silicon ユーザーの場合：
> MariaDB をデータベースとして選択してください。Oracle は arm アーキテクチャ用の SQL コンテナを構築していません。

_**DATABASE**_

使用する MySQL または MariaDB バージョンを定義します。

_**MYSQL_INITDB_DIR**_

コンテナが初めて起動されたときに、このディレクトリ内のファイルで拡張子が `.sh`、`.sql`、`.sql.gz`、`.sql.xz` のものがアルファベット順に実行されます。ファイル実行権限のない `.sh` ファイルは、実行されるのではなくソースされます。
デフォルト値は `./config/initdb` です。

_**MYSQL_DATA_DIR**_

これは MySQL データディレクトリです。デフォルト値は `./data/mysql` です。すべての MySQL データファイルはここに保存されます。

_**MYSQL_LOG_DIR**_

Apache ログの保存に使用されます。デフォルト値は `./logs/mysql` です。

## Web サーバー

Apache はポート 80 で実行するように設定されています。そのため、`http://localhost` からアクセスできます。

#### Apache モジュール

デフォルトでは、以下のモジュールが有効になっています。

- rewrite
- headers

> さらにモジュールを有効にする場合は、`./bin/phpX/Dockerfile` を更新してください。汎用的に適している場合は、PR を作成してマージすることもできます。
> `docker compose build` を実行して Docker イメージを再構築し、Docker コンテナを再起動する必要があります。

#### SSH 経由での接続

`docker compose exec` コマンドを使用して Web サーバーに接続し、さまざまな操作を実行できます。SSH 経由でコンテナにログインするには、以下のコマンドを使用します。

```shell
docker compose exec webserver bash
```

## PHP

インストールされた PHP のバージョンは、`.env` ファイルによって異なります。

#### 拡張機能

デフォルトでは、以下の拡張機能がインストールされています。
PHP バージョン <7.x.x では異なる場合があります。

- mysqli
- pdo_sqlite
- pdo_mysql
- mbstring
- zip
- intl
- mcrypt
- curl
- json
- iconv
- xml
- xmlrpc
- gd

> さらに拡張機能をインストールする場合は、`./bin/webserver/Dockerfile` を更新してください。汎用的に適している場合は、PR を作成してマージすることもできます。
> `docker compose build` を実行して Docker イメージを再構築し、Docker コンテナを再起動する必要があります。

## phpMyAdmin

phpMyAdmin はポート 8080 で実行されるように設定されています。以下のデフォルトの資格情報を使用します。

http://localhost:8080/  
ユーザー名：root  
パスワード：tiger

## Xdebug

Xdebug はデフォルトでインストールされており、そのバージョンは `".env"` ファイルで選択された PHP のバージョンによって異なります。

**Xdebug バージョン:**

PHP <= 7.3: Xdebug 2.X.X

PHP >= 7.4: Xdebug 3.X.X

Xdebug を使用するには、選択した PHP のバージョンに応じて `./config/php/php.ini` ファイルの設定を有効にする必要があります。

例：

```
# Xdebug 2
#xdebug.remote_enable=1
#xdebug.remote_autostart=1
#xdebug.remote_connect_back=1
#xdebug.remote_host = host.docker.internal
#xdebug.remote_port=9000

# Xdebug 3
#xdebug.mode=debug
#xdebug.start_with_request=yes
#xdebug.client_host=host.docker.internal
#xdebug.client_port=9003
#xdebug.idekey=VSCODE
```

Xdebug VS Code: "PHP Debug" という Xdebug 拡張機能をインストールする必要があります。インストールしたら、デバッグに進み、起動ファイルを生成して、IDE が適切にリスンして動作できるようにします。

例：

**非常に重要：** `pathMappings` は、VS Code でフォルダを開いた方法によって異なります。各フォルダには独自の構成の起動ファイルがあり、`.vscode/launch.json` で確認できます。

```json
{
 "version": "0.2.0",
 "configurations": [
  {
   "name": "Listen for Xdebug",
   "type": "php",
   "request": "launch",
   // "port": 9000, // Xdebug 2
   "port": 9003, // Xdebug 3
   "pathMappings": {
    // "/var/www/html": "${workspaceFolder}/www" // ルートフォルダで VSCODE を開いている場合
    "/var/www/html": "${workspaceFolder}" // ./www フォルダで VSCODE を開いている場合
   }
  }
 ]
}
```

これで、ブレークポイントを作成してデバッグを実行できます。

**ヒント！** これらの設定の後、コンテナを再起動する必要がある場合があります。

## Redis

Redis が付属しています。デフォルトのポート `6379` で実行されます。

## SSL (HTTPS)

`https` ドメインのサポートは組み込まれていますが、デフォルトでは無効になっています。SSL を有効化して構成できる方法は 3 つあり、`localhost` での `https` が最も簡単です。本番環境にできるだけ近いテスト環境を再現しようとしている場合は、さらに構成を行うことで、任意のドメイン名をサポートできます。

**ご注意：** `https` を使用するローカルホスト以外のすべてのドメイン名について、コンピューターの [hosts ファイル](https://en.wikipedia.org/wiki/Hosts_%28file%29) を変更し、ドメイン名を `127.0.0.1` にポイントする必要があります。これを行わないと、SSL は機能せず、ローカルでそのドメイン名にアクセスしようとするたびにインターネットにルーティングされます。

### 1) localhost での HTTPS

`localhost` (https://localhost) で `https` を有効にするには、以下の手順を実行する必要があります。

1. [mkcert](https://github.com/FiloSottile/mkcert#installation) などのツールを使用して、`localhost` 用の SSL 証明書を作成します。
  - `mkcert` を使用して、ターミナルで `mkcert localhost 127.0.0.1 ::1` を実行します。
  - 生成されたファイルを `cert.pem` と `cert-key.pem` にそれぞれ名前を変更します。
  - これらのファイルを `config/ssl` ディレクトリに配置することで、Docker セットアップに移動します。
2. `config/vhosts/default.conf` の `443` vhost のコメントを外します。

完了しました。これで、LAMP コンテナをオンにするたびに、`localhost` で `https` が機能します。

### 2) 1 つの証明書で複数のドメインでの HTTPS

ローカルテストで通常のドメイン名を使用し、`https` サポートが必要な場合は、すべてのドメイン名を網羅する SSL 証明書が最も簡単なソリューションです。

1. [mkcert](https://github.com/FiloSottile/mkcert#installation) などのツールを使用して、サポートするすべてのドメイン名を網羅する SSL 証明書を作成します。
  - `mkcert` を使用して、ターミナルで `mkcert example.com "*.example.org" myapp.dev localhost 127.0.0.1 ::1` を実行し、括弧内の部分をサポートするドメイン名と IP アドレスに置き換えます。
  - 生成されたファイルを `cert.pem` と `cert-key.pem` にそれぞれ名前を変更します。
  - これらのファイルを `config/ssl` ディレクトリに配置することで、Docker セットアップに移動します。
2. `config/vhosts/default.conf` の `443` vhost のコメントを外します。

完了しました。すべてのドメイン名を 1 つの証明書にまとめたため、vhost ファイルはさらに修正する必要なく、セットアップをサポートします。ただし、必要に応じてドメイン固有のルールを追加できます。これで、LAMP コンテナをオンにするたびに、指定したすべてのドメインで `https` が機能します。

### 3) 複数の証明書で複数のドメインでの HTTPS

ローカルテスト環境を本番環境と完全に一致させ、`https` サポートが必要な場合は、サポートするすべてのドメイン用の SSL 証明書を作成できます。

1. [mkcert](https://github.com/FiloSottile/mkcert#installation) などのツールを使用して、サポートするドメイン名用の SSL 証明書を作成します。
  - `mkcert` を使用して、ターミナルで `mkcert [your-domain-name(s)-here]` を実行し、括弧内の部分をドメイン名に置き換えます。
  - 生成されたファイルを `[name]-cert.pem` と `[name]-cert-key.pem` などのユニークな名前に名前変更し、括弧内の部分をユニークな名前に置き換えます。
  - これらのファイルを `config/ssl` ディレクトリに配置することで、Docker セットアップに移動します。
2. vhost ファイル (`config/vhosts/default.conf`) の `443` の例を使用して、ドメイン名と証明書ファイル名と一致する新しいルールを作成します。

完了しました。LAMP コンテナは、起動時に `config/ssl` 内の SSL 証明書を自動的に取得します。vhost ファイルを正しく構成し、SSL 証明書を `config/ssl` に配置していれば、LAMP コンテナをオンにするたびに、指定したドメインで `https` が機能します。

## 貢献

プルリクエストを作成したり、ユーザーの課題を解決したりする場合は喜んでお手伝いします。PR を作成する場合は、このスタックは本番環境での使用を意図しておらず、変更は汎用的に適していて、過度に特殊化されていないものである必要があることに注意してください。

> 各 PHP バージョン用の複数のブランチから 1 つの集中管理された master ブランチにプロジェクト構造を簡素化したことに注意してください。master ブランチに対して PR を作成してください。
>
> ありがとうございます！

## このスタックを本番環境でそのまま使用しない理由

私たちは、開発者が創造的なアプリケーションを迅速に作成できるようにしたいと考えています。そのため、さまざまなフレームワークと PHP バージョン用のローカル開発環境を簡単にセットアップできるようにしています。
本番環境では、少なくとも以下の項目を変更する必要があります。

- php ハンドラー: mod_php=> php-fpm
- 適切なソース IP 制限を適用して、MySQL ユーザーを安全に保護する
