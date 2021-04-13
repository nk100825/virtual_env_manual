# 環境構築手順書の作成

## 環境構築の流れ
1. virtualboxとvagrantのインストール
2. BOXの作成
3. 作業用ディレクトリとvagrantfileの作成
4. vagrantfileの編集
5. Vagrantの起動
6. phpのインストール
7. composerの導入
8. nginx(Apache)の設定ファイルの導入、起動
9. laravelの導入、起動
10. DB(mysql)のインストール
11. laravelの認証実装


# 仮想環境(MacOS)

## vagrantのインストール
```shell
$ brew install --cask vagrant # vagrantのインストール
```

## virtualbox
```shell
$ virtualbox # virtualboxのインストール
$ brew install --cask vagrant # 最新のVagrantのインストール
$ vagrant -v # Vagrantのバージョンを確認
```

## vagrant boxの作成
```shell
$ vagrant box add centos/7 # Boxをcentos/7に指定してダウンロード

 1) hyperv
 2) libvirt
 3) virtualbox
 4) vmware_desktop
 Enter your choice # 3を指定しvirtualboxを選択

　mkdir ディレクトリー名 # vagrantの作業用ディレクトリを用意する。
  $ cd vagrant_test #vagrant_testの直下に移動。
  $ vagrant init centos/7 # centos/7を指定し、仮想マシン初期化
  $ vagrant box list # CentOS7のBoxが追加されたか確認
```

## Vagrantflieの編集
  ```shell
  config.vm.network "forwarded_port", guest: 80, host: 8080 #コメントアウト
  config.vm.network "private_network", ip: "192.168.33.10"　# コメントアウト

  config.vm.synced_folder "../data", "/vagrant_data"
    # ↓ 以下に編集
  config.vm.synced_folder "./", "/vagrant", type:"virtualbox"
```

### Vagrantのプラグイン(拡張機能)vagrant-vbguestのインストール。
```shell
vagrant plugin install vagrant-vbguest # インストール

vagrant plugin list # vagrant-vbguestのインストールが完了しているか確認する。
vagrant ssh # 仮想環境へのアクセス（vagrantfileのあるディレクトリ内で実行）

sudo yum -y groupinstall "development tools" # development tools”と導入する名称をインストール
```

### phpのインストール
```shell
sudo yum -y install epel-release wget # EPEL リポジトリをインストール
sudo wget http://rpms.famillecollet.com/enterprise/remi-release-7.rpm # 指定のURLをインストール
sudo rpm -Uvh remi-release-7.rpm # パッケージをアップデート
sudo yum -y install --enablerepo=remi-php72 php  php-pdophp-mysqlnd php-mbstring php-xml php-fpm php-common php-devel php-mysql unzip # phpのインストール
php -v # phpのバージョンを確認
```

### コマンド
```shell
-y # 全ての問い合わせに「yes」で応答したものとして実行する
yum # CeontOSで利用されるパッケージ管理システム
sudo # スーパーユーザーの権限が必要なコマンドをsudoコマンド経由で実行される。
wget # wgetコマンドで指定したURLのファイルをダウンロードする。
rpm # Red Hat系のLinuxディストリビューションで使われている“RPM（Red Hat Package Manager）パッケージ”を扱うことができるパッケージ管理コマンド
EPEL リポジトリ # CentOS標準のリポジトリでは提供されていないパッケージを、yum コマンドでインストールすることを可能にするリポジトリ
php-pdo php-mysqlnd php-mbstring php-xml php-fpm php-common php-devel php-mysql php-zip unzip # PHPアプリケーションを動かす上で必要となるモジュール(拡張機能)をインストールしている。
```

### composerのインストール
```shell
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" # composerのインストール
php composer-setup.php # 実行したディレクトリに、composer-setup.phpがダウンロード
php -r "unlink('composer-setup.php');" # もう使用しないのでcomposer-setup.phpを削除
sudo mv composer.phar /usr/local/bin/composer # composer.phar” を “/usr/local/bin/” に移動
composer -v # composerのバージョンを確認
```
### コマンド
```shell
mv 移動コマンド
```
### Nginxのインストール
```shell
 # viエディタを使用して以下のファイルを作成
sudo vi /etc/yum.repos.d/nginx.repo

[nginx] # コードを書き込む
name=nginx repo
baseurl=https://nginx.org/packages/mainline/centos/\$releasever/\$basearch/
gpgcheck=0
enabled=1

sudo yum install -y nginx # インストール
nginx -v # バージョンの確認
sudo systemctl start nginx # 起動

sudo vi /etc/yum.repos.d/nginx.repo # viエディタを使用して以下のファイルを作成.
```

### Laravelアプリケーションのコピー作成
```shell
sudo vi /etc/nginx/conf.d/default.conf

/etc/nginx/conf.d ディレクトリ下の default.conf ファイルが設定ファイルとなる

# コードを書き込む
server {
  listen       80;
  server_name  192.168.33.10; # Vagranfileで書いた箇所のipアドレスを記述
  root /vagrant/laravel_app/public;
  index  index.html index.htm index.php;
```

### DB（mysql）のインストール
```shell
sudo wget https://dev.mysql.com/get/mysql57-community-release-el7-7.noarch.rpm
sudo rpm -Uvh mysql57-community-release-el7-7.noarch.rpm
sudo yum install -y mysql-community-server
mysql -v   # mysqlのバージョンの確認
sudo systemctl start mysqld # Mysqlの起動
mysql -u root -p # ユーザー名
Enter password: # パスワード
ls -a # 隠しファイルを見る

vi .env # 下記のように設定する
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=laravel_app
DB_USERNAME=root
DB_PASSWORD=19948100Nk!

php artisan migrate # マイグレーション実行
```

### laravelのインストール、認証実装
```shell
composer require laravel/ui "^1.0" --dev
php artisan ui vue --auth
```

### バージョン
```
Composer version 2.0.11
PHP 7.3.27
config.vm.box = "centos/7"
nginx version: nginx/1.19.8
mysql  Ver 14.14 Distrib 5.7.33
laravel 6.20
```

### 環境構築の所感
```
- 仮想環境の便利さとエラーコードの調べ方について学びました。
プロジェクトを開発する場合、環境構築をすることによって失敗しても再度作り直すことも可能、全てコマンドでパッケージや拡張機能などインストール、ファイルやディレクトリの作成、削除、移動なども可能でとても便利だと思いました。

- 一個のミスでエラーが出ることと、どこのエラーが出てるか、エラーの調べ方がとても大変でした。
エラー文を翻訳したり、一個一個の単語を調べたり、何のコマンドで何のエラーが出てるかを理解することが少し出来るようになった気がします。

今後の課題
聞き方がまだ弱いので、今後は**今やっていること**、**現状**、**調べたこと**、**改善のためにやったこと**、**自分の考え**、
意識をして取り組みたいです。

```

### 参考したサイト
```
[【 rpm 】コマンド（基礎編）――RPMパッケージをインストールする／アンインストールする]
(https://www.atmarkit.co.jp/ait/articles/1609/13/news024.html)

[CentOSにEPEL リポジトリを追加する]
(https://www.websec-room.com/2014/01/15/1535#:~:text=EPEL%20%E3%83%AA%E3%83%9D%E3%82%B8%E3%83%88%E3%83%AA%E3%81%A8%E3%81%AF%E3%80%81CentOS,%E3%82%82%E3%81%AE%E3%81%AB%E3%81%AA%E3%81%A3%E3%81%A6%E3%81%84%E3%81%BE%E3%81%99%E3%80%82)

[CentOS 7 に PHP 7.2 を yum でインストールする手順]
(https://weblabo.oscasierra.net/centos7-php72-install/)

[ sudo 】コマンド――スーパーユーザー（rootユーザー）の権限でコマンドを実行する]
(https://www.atmarkit.co.jp/ait/articles/1611/28/news036.html)

[yumとは？コマンドの使い方やリポジトリの指定、追加、一覧、rpmの違いなど]
(https://kitsune.blog/yum-summary#yum%E3%81%A8%E3%81%AF
https://www.atmarkit.co.jp/ait/articles/1609/06/news023.html)

[Linux系OSのパッケージ管理システム（rpmやyum）及びリポジトリ（epel）について]
(https://rainbow-engine.com/intro-yum-rpm-epel/)

[[PHP] Composerをインストールする方法]
(https://webbibouroku.com/Blog/Article/php-composer-setup)