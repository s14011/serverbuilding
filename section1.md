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
2. '$/etc/sysconfig/network-scripts/ifup enp0s3'
   '$/etc/sysconfig/network-scripts/ifup enp0s8'
    を実行して設定を反映する
3. 設定が反映されてるか確認するため
   '$ip addr'
   を実行するとenp0s8に192.168.xxx.xxxってアドレスが振られてる
4. enp0s8のipアドレスを覚えてubuntuの端末から$ssh 192.168.xxx.xxxでアクセスする

## プロキシの設定

1. $vi /etc/profile ファイルに下記を追記する
'PROXY='172.16.40.1:8888' 
export http_proxy=$PROXY  
export HTTP_PROXY=$PROXY  
export HTTPS_PROXY=$PROXY  
export https_proxy=$PROXY'
2. yumにプロキシを設定
$vi /etc/yum.confに下記を追記
'proxy=http://172.16.40.1:8888/'
3. wgetにプロキシを設定
$vi /etc/wgetrcに下記を追記
'http_proxy = 172.16.40.1:8888/  
https_proxy = 172.16.40.1:8888/  
ftp_proxy = 172.16.40.1:8888/'
8. プロキシの設定が終わったのでupdateする
'yum update'



