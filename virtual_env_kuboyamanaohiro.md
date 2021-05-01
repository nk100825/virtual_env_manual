# 環境構築手順書の作成

## 環境構築の流れ
1. virtualboxとvagrantのインストール
2. BOXの作成
3. 作業用ディレクトリとvagrantfileの作成
4. vagrantfileの編集
5. Vagrantの起動
6. phpのインストール
7. composerの導入
8. laravelのインストール
9. nginx(Apache)の設定ファイルの導入、起動
10. laravelの起動
11. DB(mysql)のインストール
12. laravel/ui というパッケージのインストール


## バージョン
|  ソフト   |  バージョン  |
|   ----   |    ----    |
| Composer |   2.0.11   |
|   PHP    |   7.3.27   |
|    OS    |  centos/7  |
|  nginx   |   1.19.8   |
|   mysql  |   5.7.33   |
|  laravel |    6.20    |

## コマンド説明

|  コマンド   | コマンド概要 |
|   ----   |    ----    |
| yum |   CeontOSで利用されるパッケージ管理システム   |
|  wget  |  wget URLで指定したURLのファイルをダウンロード、、保存が出来る。  |
|  rpm   |   RPM（Red Hat Package Manager）パッケージ”を扱うことができるパッケージ管理コマンド。パッケージ個々の情報を細かく管理できる  |
|   EPEL  |  CentOS標準のリポジトリでは提供されていないパッケージを、yum コマンドでインストールすることを可能にするリポジトリ  |
|   -y    |   全ての問い合わせに「yes」で応答したものとして実行する   |

## vagrantのインストール
```shell
# ホストOS
 # vagrantのインストール
$ brew install --cask vagrant
 # Vagrantのバージョンを確認
$ vagrant -v
 # 下記のコードが出たらOK
 Vagrant 2.2.14
```

## virtualbox
```shell
# ホストOS
# 公式からインストール
https://www.virtualbox.org/wiki/Download_Old_Builds_6_0
```


## vagrant boxの作成
Boxをcentos/7に指定してダウンロード
```shell
#ホストOS
$ vagrant box add centos/7

 1) hyperv
 2) libvirt
 3) virtualbox
 4) vmware_desktop
 Enter your choice
 ```

3を指定しvirtualboxを選択
下記のように表示されたらOK

```shell
Successfully added box 'centos/7' (v1902.01) for 'virtualbox'!
```

## Vagrantの作業ディレクトリを用意する
```shell
# ホストOS
   # vagrantの作業用ディレクトリを用意する。
  $ mkdir ディレクトリー名
   #vagrant_testの直下に移動。
  $ cd vagrant_test
   # centos/7を指定し、仮想マシン初期化
  $ vagrant init centos/7
   # CentOS7のBoxが追加されたか確認
  $ vagrant box list
```

## Vagrantflieの編集
  ```shell
# ホストOS上で入力してください。
  # コメントアウトを外す
  config.vm.network "forwarded_port", guest: 80, host: 8080
   # コメントアウトを外す
  config.vm.network "private_network", ip: "192.168.33.10"

  config.vm.synced_folder "../data", "/vagrant_data"
    # ↓ 以下に編集
  config.vm.synced_folder "./", "/vagrant", type:"virtualbox"
```
`./` はカレントディレクトリ(vagrant_test)を示しており、ホストOS (Mac or Windows) のvagrant_testディレクトリ内とゲストOS (Vagrant) の `/vagrant` のディレクトリ内をリアルタイムで同期するための設定。
<br>
<br>

## Vagrantのプラグイン(拡張機能)vagrant-vbguestのインストール
```shell
# ホストOS上で入力してください。
 # インストール
vagrant plugin install vagrant-vbguest

 # vagrant-vbguestのインストールが完了しているか確認する。
vagrant plugin list
 # 仮想環境へのアクセス（vagrantfileのあるディレクトリ内で実行）
vagrant ssh

 # yumのアップデート
sudo yum -y update
 # development tools”と導入する名称をインストール
sudo yum -y groupinstall "development tools"
```
Vagrantには様々なプラグイン(拡張機能)が用意されている.<br>vagrant-vbguest というプラグインをインストール
<br>
<br>


## phpのインストール
```shell
# ゲストOS[vagrant@localhost ~]$
 # EPEL リポジトリをインストール
sudo yum -y install epel-release wget
 # 指定のURLをインストール
sudo wget http://rpms.famillecollet.com/enterprise/remi-release-7.rpm
 # パッケージをアップデート
sudo rpm -Uvh remi-release-7.rpm
 #  PHPアプリケーションを動かす上で必要となるモジュール(拡張機能)をインストールしている。
sudo yum -y install --enablerepo=remi-php73 php  php-pdophp-mysqlnd php-mbstring php-xml php-fpm php-common php-devel php-mysql unzip
 # phpのバージョンを確認
php -v
```

## composerのインストール
```shell
# ゲストOS[vagrant@localhost ~]$
 # composerのインストール
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
 # 実行したディレクトリに、composer-setup.phpがダウンロード
php composer-setup.php
 # もう使用しないのでcomposer-setup.phpを削除
php -r "unlink('composer-setup.php');"
 # composer.phar” を “/usr/local/bin/” に移動
sudo mv composer.phar /usr/local/bin/composer
 # composerのバージョンを確認
composer -v
```

## laravelのインストール
```shell
# ゲストOS[vagrant@localhost ~]$
composer global require "laravel/installer=~1.1"
```
composerとは、プロジェクトが必要とするライブラリやパッケージを管理し、
それをもとにインストールをすることができる。
phpの依存管理ツールのこと

## Nginxのインストール
```shell
# ゲストOS[vagrant@localhost ~]$
# viエディタを使用して以下のファイルを作成
sudo vi /etc/yum.repos.d/nginx.repo

# コードを書き込む前の内容
[nginx]
name=nginx repo
baseurl=http://nginx.org/packages/mainline/centos/7/$basearch/
gpgcheck=0
enabled=1

# コードを書き込む
[nginx]
name=nginx repo
baseurl=https://nginx.org/packages/mainline/centos/\$releasever/\$basearch/
gpgcheck=0
enabled=1

 # インストール
sudo yum install -y nginx
 # バージョンの確認
nginx -v
 # 起動
sudo systemctl start nginx
```

## laravelの起動
```shell
# ホストOS上で入力してください。
sudo vi /etc/nginx/conf.d/default.conf

/etc/nginx/conf.d ディレクトリ下の default.conf ファイルが設定ファイルとなる

# コードを書き込む前のコード
server {
  listen       80;
   # Vagranfileで書いた箇所のipアドレスを記述
  server_name  192.168.33.19;
  root ;
  index  ;

# コードを書き込む
server {
  listen       80;
   # Vagranfileで書いた箇所のipアドレスを記述
  server_name  192.168.33.10;
  # 追記
  root /vagrant/laravel_app/public;
  # 追記
  index  index.html index.htm index.php;
```
rootとはnginxのドキュメントルートの指定。（初めに読み込むディレクトリーの指定）
vagrantのlaravel_appのpublicを読み込みますよと指定してあげる。

リクエストURIのパスが"/public/"のときにindex.htmlというファイルが存在すれば、"/public/index.html"に内部リダイレクトする。
指定したindex.〇〇が初めに読み込まれる。

## DB（mysql）のインストール
```shell
# ゲストOS[vagrant@localhost ~]$
# 指定したURL,https://dev.mysql.com/get/のrpmファイルにwgetで権限を与える
sudo wget https://dev.mysql.com/get/mysql57-community-release-el7-7.noarch.rpm
# mysql-community-serverのアップデート
sudo rpm -Uvh mysql57-community-release-el7-7.noarch.rpm
 # mysql-community-serverのインストール
sudo yum install -y mysql-community-server
# mysqlのバージョンの確認
mysql --version
```

versionの確認ができましたらインストール完了です。
次にMySQLを起動し接続を行います。

## mysqlの起動
 ```shell
# ゲストOS[vagrant@localhost ~]$
 # Mysqlの起動
sudo systemctl start mysqld
 # mysqlのログイン
mysql -u root -p
```
今回はデフォルトでrootにパスワードが設定されてしまっています。
まずはpasswordを調べ、接続しpassswordの再設定を行っていく必要があります。

## パスワードの再設定
```shell
sudo cat /var/log/mysqld.log | grep 'temporary password'
  # このコマンドを実行したら下記のように表示されたらOKです
2017-01-01T00:00:00.000000Z 1 [Note] A temporary password is generated for root@localhost: hogehoge
```
hogehoge と記載されている箇所に存在するランダムな文字列がパスワードとなります。

<br>
先程出力したランダム文字列をコピー後、再度以下のコマンドを実行し、パスワード入力時にペーストしてください。

```shell
$ mysql -u root -p
$ Enter password:
mysql >
```
次に接続した状態でpasswordの変更を行います。
```shell
mysql > set password = "新たなpassword";
```
新たなpasswordには、必ず大文字小文字の英数字 + 記号かつ8文字以上の設定をする必要があります。

```shell
$ sudo vi /etc/my.cnf
```
テキストの変更


```shell
[mysqld]
# read_rnd_buffer_size = 2M
datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock

# 下記の一行を追加
validate-password=OFF
```
## migrateの設定
```shell
# ホストOS上で入力してください。
ls -a
 # 隠しファイルを見る
 ```
mysqlを再起動
```shell
$ sudo systemctl restart mysqld
```

下記のように設定する
```shell
 # マイグレーション実行
vi .env
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=laravel_app
DB_USERNAME=root
DB_PASSWORD=

php artisan migrate
```
## laravel/ui というパッケージのインストール
```
ホストOS上で入力してください。
composer require laravel/ui "^1.0" --dev
php artisan ui vue --auth
```


## 環境構築の所感
- 仮想環境の便利さとエラーコードの調べ方について学びました。
プロジェクトを開発する場合、仮想環境自体が作り直し可能な性質を持っており、 yumだったりcomposer、wgetコマンドでパッケージや拡張機能などインストール、ファイルやディレクトリの作成、削除、移動なども可能でとても便利だと思いました。

- 一個のミスでエラーが出ることと、どこのエラーが出てるか、エラーの調べ方がとても大変でした。
エラー文を翻訳したり、一個一個の単語を調べたり、何のコマンドで何のエラーが出てるかを理解することが少し出来るようになった気がします。

## 今後の課題
聞き方がまだ弱いので、今後は**今やっていること**、**現状**、**調べたこと**、**改善のためにやったこと**、**自分の考え**、
意識をして取り組みたいです。

## 参考したサイト
[【 rpm 】コマンド（基礎編）――RPMパッケージをインストールする／アンインストールする](https://www.atmarkit.co.jp/ait/articles/1609/13/news024.html)

[CentOSにEPEL リポジトリを追加する](https://www.websec-room.com/2014/01/15/1535#:~:text=EPEL%20%E3%83%AA%E3%83%9D%E3%82%B8%E3%83%88%E3%83%AA%E3%81%A8%E3%81%AF%E3%80%81CentOS,%E3%82%82%E3%81%AE%E3%81%AB%E3%81%AA%E3%81%A3%E3%81%A6%E3%81%84%E3%81%BE%E3%81%99%E3%80%82)

[CentOS 7 に PHP 7.2 を yum でインストールする手順](https://weblabo.oscasierra.net/centos7-php72-install/)

[ sudoコマンド――スーパーユーザー（rootユーザー）の権限でコマンドを実行する](https://www.atmarkit.co.jp/ait/articles/1611/28/news036.html)

[yumとは？コマンドの使い方やリポジトリの指定、追加、一覧、rpmの違いなど](https://kitsune.blog/yum-summary#yum%E3%81%A8%E3%81%AF)

[【 yum 】コマンド（応用編その3）――パッケージ／システムをアップデートする](https://www.atmarkit.co.jp/ait/articles/1609/06/news023.html)

[Linux系OSのパッケージ管理システム（rpmやyum）及びリポジトリ（epel）について](https://rainbow-engine.com/intro-yum-rpm-epel/)

[[PHP] Composerをインストールする方法](https://webbibouroku.com/Blog/Article/php-composer-setup)