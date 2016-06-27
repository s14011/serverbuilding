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

    1 Amazon web servicesにログインする  
    2 EC2のアイコンをクリックしてEC2のダッシュ-ボードにアクセス  
    3 インスタンスの作成をクリック  
    4 Amazon linuxを選択  
    5 表の一番上の`t2.micro`を選択して右下にある確認と作成をクリック  
    6 作成をクリック
    7 新しいキーペアを作成する※ダウンロードしたキーファイルはsshで使うので消さないように！
    8 インスタンスの作成をクリック
    9 動いているマシンの一覧が出てくるから自分のを選択して一番右にあるセキュリティグループをクリックする
    10 グループを選択して右クリックしてインバウンドルールの編集を選択してルールを追加をクリックしてHTTPを追加する
    11 ansibelを実行する※やり方は下の方に書いてる
    12 ブラウザからアクセス(http://52.68.182.11)

## SSH接続  
   さっき作っさキーファイルのアクセス権限を変更  
   `$chmod 400 n14002.pem`  
   ssh接続  
   `$ssh -i キーファイル ec2-user@52.68.182.11`  

## ansibleを実行するための準備  
   hostsファイルの作成  
   `$vi hosts`  

    [all]  
    52.68.182.11(sshした時のIPアドレス)

## ansible実行
`$ansible-playbook 実行したいファイル -i hosts  --private-key ~/.aws/n14011.pem`

   [section6_ansible](section6)

# 6-2 AWS EC2(AMIMOTO)

    1 Amazon web servicesにログインする  
    2 EC2のアイコンをクリックしてEC2のダッシューボードにアクセス  
    3 インスタンスの作成をクリック  
    4 左のメニューバーから`AWS Marketplace`を選択して`AMIMOTO`を検索する  
    5 `WordPress powered by AMIMOTO (HHVM)`を選択  
    6 表の一番上の`t2.micro`を選択して右下にある確認と作成をクリック  
    7 セキュリティグループのところでエラーが出るので編集を選択  
    8 セキュリティグループを6-1で作ったものに変える  
    9 6−1で作ったキー名を選択してインスタンスの作成をクリック  
    10 ブラウザからアクセス(http://52.68.235.54)

# 6-3 S3

    1 ubuntuの端末で`$ aws s3 mb s3://n14011`を実行したらS3のページにn14011というディレクトリができてる  
    2   `$aws s3 ls`で確かめれる  
    3 `$aws s3 cp index.html s3://n14011/index.html`でファイルをアップロードできる  
    4 Amazon web servicesにログインする  
    5 S3のアイコンをクリックしてS3にアクセス  
    6 自分が作ったファイルまで移動して右クリックで公開をクリックして公開する
    7 公開したら右上にあるプロパティをクリック
    8 リンクをクリックしたらブラウザでアクセスできる

# 6-4 CloudFront

    1 AMIを使ってインスタンスを作る  
    2 CloudFrontに行ってCreate Distributionをクリック  
    3 WebとRTMPがあるが、Webを選択  
    4 Origin Domain Nameに1で作ったインスタンスのパブリックDNSを指定。  
    5 Create Distributionを押す
    6 Domain nameをhttp://のあとに追加して実行
     
# 6-5 ELB

    0 Amazon web servicesにログインする
    1 EC2のアイコンをクリックしてアクセス  
    2 EC2で3台くらいマシンを動かしとく  
    3 左側のメニューからロードバランサーをクリック
    4 ロードバランサーの作成をクリック
    5 セキュリティグループも動いてるマシンと同じにする
    6 ステップ 5まで移動
      自分のマシンをすべて選択する
    7 ステップ7まで移動
      作成をクリック
    8 すべてのマシンにSSHで接続
    9 ログを表示する
      $cd /var/log/nginx/
      $tail -f accecs.log
    10 IPアドレスが分散されているか確認
    12 ロードバランサーに行って、説明の一番上にあるDNSのアドレスに繋いで、最初に作ったワードプレスが表示されたら終了


