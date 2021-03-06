## centOSのインストール

1. USBからCentOS-7-x86_64-Minimal-1511.isoをコピー

## VirtualBoxの設定

1. virtualboxを起動
2. 新規ボタンを押して名前:centos7 タイプ:Linux バージョン:Red Hat(64bit)を設定して次へをクリック
3. メモリーサイズに1024MB割り当てて次へをクリック
4. 仮想ハードドライブを作成するを選んで作成を押す
5. 容量を8Gに設定して作成を押す
6. Box作成完了
7. ファイル→環境設定→ネットワークからホストオンリーネットワークにvboxnet0を追加する
8. Boxの設定をクリック
9. centos7の設定をおしてネットワークの設定に移動してアダプター2をクリック
10. ネットワークアダプターを有効化にチェックを入れる。
11. 割り当て:ホストオンリーアダプターにする
12. Boxの設定完了

## VirtulBoxにCentOS7をインストール

1. VirtualBoxを起動
2. install centos7を選択
3. 日本語を選択
4. キーボードをUSにする
5. インストール開始
6. インストール中に"root"以外のユーザーを作成する

## SSH接続の設定

1. $vi /etc/sysconfig/network-scripts/ifcfg-enp0s3のファイルを開いてONBOOT=noをONBOOT=yesに変更する ifcfg-enp0s8のファイルも同じようにする
2. ‘$/etc/sysconfig/network-scripts/ifup enp0s3‘
   ‘$/etc/sysconfig/network-scripts/ifup enp0s8‘
    を実行して設定を反映する
3. 設定が反映されてるか確認するため
   ‘$ip addr‘
   を実行するとenp0s8に192.168.xxx.xxxってアドレスが振られてる
4. enp0s8のipアドレスを覚えてubuntuの端末から$ssh 192.168.xxx.xxxでアクセスする

## プロキシの設定

1. $vi /etc/profile ファイルに下記を追記する  
PROXY='172.16.40.1:8888  
export http_proxy=$PROXY  
export HTTP_PROXY=$PROXY  
export HTTPS_PROXY=$PROXY  
export https_proxy=$PROXY
2. yumにプロキシを設定  
$vi /etc/yum.confに下記を追記  
proxy=http://172.16.40.1:8888/
3. wgetにプロキシを設定  
$vi /etc/wgetrcに下記を追記  
http_proxy = 172.16.40.1:8888/  
https_proxy = 172.16.40.1:8888/  
ftp_proxy = 172.16.40.1:8888/
8. プロキシの設定が終わったのでupdateする  
‘$ yum update‘

## Wordpressに必要なやつのインストール

1. PHP,Apacheのインストール  
$ yum install -y unzip wget httpd php php-mysql php-gd php-mbstring
2. mysqlのインストール  
$wget http://dev.mysql.com/get/mysql-community-release-el7-5.noarch.rpm
3. mysqlのリポジトリのインストール  
$rpm -Uvh mysql-community-release-el7-5.noarch.rpm  
設定ファイルを書き換える  
$/etc/yum.repos.d/mysql-community.repo  
[mysql-connectors-community]  
enabled=1→enabled=0  
[mysql-tools-community]  
enabled=1→enabled=0  
[mysql56-community]  
enabled=1→enabled=0

4. mysqlのインストール  
$yum --enablerepo=mysql56-community install mysql-community-server
5. MySQLとApacheを起動する  
$systemctl start mysqld.service・・・MySQL  
$systemctl start httpd.service・・・Apache
6. MySQLとApacheをずっと起動したままにする  
$systemctl enable mysqld.service  
$systemctl enable mysqld.service
7. SELinux無効設定  
$vi /etc/sysconfig/selinuxを開いて下記を変更  
SELINUX=enforcing→SELINUX=disabled
8. firewalld設定・・・http,httpsを許可する  
$firewall-cmd --add-service=http --zone=public --permanent  
$firewall-cmd --add-service=https --zone=public --permanent

## データベースの作成

1. MySQLを起動する  
$mysql -u root
2. rootユーザーにパスワードを設定する  
mysql> SET PASSWORD FOR 'root'@'localhost' = PASSWORD('xxxxxxxxxx');
3. 1回終了してrootでログイン  
mysql>exit $mysql -u root -p
4. wordpressで使うデータベースを作成する  
mysql> CREATE DATABASE データベース名;
5. ユーザの作成  
mysql> GRANT ALL PRIVILEGES ON データベース名.* to 'ユーザー名'@'localhost' IDENTIFIED BY '任意パスワード';

## Wordpressのインストール

1. Wordpressのzipファイルをインストールする  
$wget https://ja.wordpress.org/wordpress-4.5.2-ja.zip
2. インストールしたzipファイルを解凍する  
$unzip wordpress-4.2.2-ja.zip
3. 解凍したファイルを/var/www/htmlに移動する  
$mv wordpress /var/www/html
4. wordpressのフォルダに移動  
$cd /var/www/html/wordpress
5. wordpressフォルダのwp-config-sample.phpをコピーしてwp-config.phpを作成する  
$cp wp-config-sample.php wp-confing.php
6. コピーしたwp-config.phpの中を編集  
$vi wp-config.php
`WordPress のためのデータベース名`   
`define('DB_NAME', 'database_name_here');`  
↓  
`define('DB_NAME', 'mysqlで作ったデータベース名');`  
`MySQL データベースのユーザー名`  
`define('DB_USER', 'username_here');`  
↓  
`define('DB_USER', 'mysqlで作ったデータベースの所有者名');`  
`MySQL データベースのパスワード`  
`define('DB_PASSWORD', 'password_here');`  
↓  
`define('DB_PASSWORD', 'mysqlで作ったデータベースの所有者名のパスワード');`
7. 7 ブラウザからWordpressにアクセス  
192.168.xxx.xxx/wordpress/wp-admin/install.php
