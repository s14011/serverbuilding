## Vagrant

## 2-1 Vagrantを使用したCentOS 7環境の起動

サーバーを構築するたびにOSのインストールからやってられないので事前に用意したCentOS 7の環境をVagrantで起動します。

### Vagrantで起動できるCentOS 7のイメージを登録

USBストレージにVagrant用CentOS boxを用意してありますので登録して使用します。

    vagrant box add CentOS7 コピーしたboxファイル --force

### Vagrantの初期設定

作業用ディレクトリを作成し、その中で初期設定を行ないます。

   vagrant init

上記コマンドを実行するとVagrantfileというファイルが作成されます。このファイルにVagrantの設定が書かれています。
そのままではデフォルトのOS(存在しない)を起動してしまうので、CentOS 7を起動するようにします。

viでVagrantfileを開き、

    config.vm.box = "base"

と書かれているのを

    config.vm.box = "CentOS7"

とします。ここで指定するものはvagrant box addで指定したものの名前(上記の例だとCentOS7)を指定します。

### 仮想マシンの起動

    vagrant up

### 仮想マシンの停止

    vagrant halt

### 仮想マシンの一時停止

    vagrant suspend

### 仮想マシンの破棄

最初からやり直したい…そんな時に破棄するとCentOSが初期化されます。
また`vagrant up`をすると立ち上がります…

    vagrant destroy

### 仮想マシンへ接続

実際の仮想マシンへはsshで接続します。

    vagrant ssh

### ホストオンリーアダプターの設定

サーバーを設定したあと、動作確認するために接続するためのIPアドレスを設定します。
また、そのためのNICを追加します。

Vagrantfileの

    Vagrant.configure(2) do |config|

から一番最後の

    end

の間に

    config.vm.network "private_network", ip:"192.168.56.129"

と書くと仮想マシンのNIC2に192.168.56.129のIPアドレスが振られます。
`config.vm.box = "CentOS7"` の下にでも書くといいと思います。

※ 当然のことながら、複数台の仮想マシンを立ち上げる時には異なるIPアドレスを割り当てる必要があります。

### Vagrantfileの反映

Vagrantfileで変更した設定を反映させるには

    vagrant reload

すると反映されます。ただし、再起動されますので注意してね。

## 2-2 Wordpressを動かす

### プロキシの設定

1. $vi /etc/profileファイルを開く  
PROXY='172.16.40.1:8888'    
export http_proxy=$PROXY    
export HTTP_PROXY=$PROXY    
export HTTPS_PROXY=$PROXY    
export https_proxy=$PROXY  
↑を書く
2. yumにプロキシを設定  
$vi /etc/yum.conf ファイルに下記を追記  
proxy=http://172.16.40.1:8888/
3. wgetにプロキシを設定  
$vi /etc/wgetrc ファイルに下記を追記  

http_proxy = 172.16.40.1:8888/  
https_proxy = 172.16.40.1:8888/  
ftp_proxy = 172.16.40.1:8888/  
4. プロキシの設定が終わったのでupdateする  
$yum update

## 2-2.wordpressに必要なものをインストール

1. nginxのリポジトリのインストール
$sudo yum install http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm
2. nginxのパッケージのインストール   
$ sudo yum install --enablerepo=nginx nginx
3. サービスを起動  
$ sudo systemctl start nginx
4. PHPと必要なモジュールをインストール  
$sudo yum -y install php-mysql php php-gd php-mbstring php-fpm
5. php-fpm設定  
sudo vi /etc/php-fpm.d/www.conf開いて  

user = apache
group = apach
変更　↓
user = nginx
group = nginx

6. 起動させる  
$ sudo systemctl start php-fpm.service
7. mariadbのインストール  
$ sudo yum -y install mariadb mariadb-server
8. サービスの起動  
$ sudo systemctl start mariadb
9. 設定  
$ mysql_secure_installation  
全部 y で!!!!!!!!!!!!!1!!!!!!!!
10. 再起動  
$ sudo systemctl restart mariadb
11. root でログイン  
$ mysql -u root -p
12. wordpressで使うデータベースの作成  
mysql> CREATE DATABASE データベース名;  
13. ユーザーの作成  
mysql> GRANT ALL PRIVILEGES ON データベース名.* to 'ユーザ名'@'localhost' IDENTIFIED BY '任意パスワード';  
mysql> exit
14. Wordpressをダウンロード  
$ wget http://wordpress.org/latest.tar.gz
15. 解凍  
$ tar xzfv latest-ja.tar.gz
16. 所有者とグループをnginxに変更  
$ sudo chown -R nginx:nginx wordpress
17. wordpressをディレクトリ/var/www/にいどう 
$ mv wordpress /var/www
18. default.confの中身を変更  
rootをwordpressを置いた場所に変更
root /var/www/;に変更  

fastcgi_param SCRIPT_FILENAME /scripts$fastcgi_script_name;  
    ↓にかえる
fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;  
19. nginxを再起動  
$ sudo systemctl restart nginx

20. wordpressフォルダのwp-config-sample.phpをコピーしてwp-config.phpを作成する  
    wordpressフォルダの中でcp wp-config-sample.php wp-config.php
21. コピーしたwp-config.phpの中を編集  
$vi wp-config.phpで開く  

WordPress のためのデータベース名  
     define('DB_NAME', 'database_name_here');  
    ↓
     define('DB_NAME', 'mysqlで作ったデータベース名');  
     MySQL データベースのユーザー名  
     define('DB_USER', 'username_here');  
    ↓
     define('DB_USER', 'mysqlで作ったデータベースの所有者名');  
     MySQL データベースのパスワード  
     define('DB_PASSWORD', 'password_here');  
    ↓
     define('DB_PASSWORD', 'mysqlで作ったデータベースの所有者名のパスワード');  
     
22. 192.16.56.129/wordpress/wp-admin/ でアクセスする

## 2-3 プロキシの設定

1. プロキシの設定  
   `$vi /etc/profile` ファイルに下記を追記  

    PROXY='172.16.40.1:8888'  
    export http_proxy=$PROXY  
    export HTTP_PROXY=$PROXY  
    export HTTPS_PROXY=$PROXY  
    export https_proxy=$PROXY
    
2. yumにプロキシを設定する  
   `$vi /etc/yum.conf` ファイルに下記を追記
3. wgetにプロキシを設定  
    
    http_proxy = 172.16.40.1:8888/  
    https_proxy = 172.16.40.1:8888/  
    ftp_proxy = 172.16.40.1:8888/
4. yumのアップデートをする  
    `$yum update`

## 2-3 Wordpressに必要なものをインストール

1. Apacheのインストール  
   `$wget http://ftp.yz.yamagata-u.ac.jp/pub/network/apache//httpd/httpd-2.2.31.tar.gz`
2. ファイルの解凍  
    `$tar -xvf httpd-2.2.31.tar.gz`
3. ファイルに移動する  
    `$cd httpd-2.2.31`
4. Apacheのインストール  
   
    $./configure    
    $make  
    $sudo make install

5. Apacheを起動させる  
   `$sudo /usr/local/apache2/bin/apachectl start`

    エラーが出ます!!!!!!!!!!!!!!!
    `sudo vi /usr/local/apache2/conf/httpd.conf`ファイルに追加する↓
    
    ServerName www.example.com:80 ←の下に  
    ServerName localhost:80
    
    sudo /usr/local/apache2/bin/apachectl restartで再起動させる
    
6. 
