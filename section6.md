# 6-1 AWS EC2 + Ansible                                                     
 ## pipをインストール
    `$sudo apt-get install python-pip`
 ## awscliをインストール
    `$sudo pip install awscli`
 ## awscliの設定
 
     $ aws configure
    AWS Access Key ID [None]: 先生からもらったID
    AWS Secret Access Key [None]: 先生からもらったKey
    Default region name [None]: ap-northeast-1
    Default output format [None]: json

     これでawsコマンドが使える
 
## EC2を使用する  
1. Amazon web servicesにログインする  
2. EC2のアイコンをクリックしてEC2のダッシュ-ボードにアクセス  
3. インスタンスの作成をクリック  
4. Amazon linuxを選択  
5. 表の一番上の`t2.micro`を選択して右下にある確認と作成をクリック  
6. 作成をクリック
7. 新しいキーペアを作成する※ダウンロードしたキーファイルはsshで使うので消さないように！
8. インスタンスの作成をクリック
9. 動いているマシンの一覧が出てくるから自分のを選択して一番右にあるセキュリティグループをクリックする
10. グループを選択して右クリックしてインバウンドルールの編集を選択してルールを追加をクリックしてHTTPを追加する
11. ansibelを実行する※やり方は下の方に書いてる                           
12. ブラウザからアクセス(http://52.68.182.11)
