## 3-1 ansible

## ansibleのインストール  

    sudo /usr/local/apache2/bin/apachectl start                            
    sudo apt-get install software-properties-common
    sudo apt-add-repository ppa:ansible/ansible
    sudo apt-get update
    sudo apt-get install ansible

### vagrantのプラグインのインストール
    $vagrant plugin install vagrant-proxyconf  
    
### Vagrantfileの作成
    `$vagrant init Centos7`
### ホストオンリーアダプターの設定
      $vi Vagrantfileを開いて
      Vagrant.configure(2) do |config| end
      の間に`config.vm.network :private_network, ip:"192.168.56.129"を追加
### proxyの設定
  
      if Vagrant.has_plugin?("vagrant-proxyconf")
         config.proxy.http     = ENV["HTTP_PROXY"]
         config.proxy.https    = ENV["HTTP_PROXY"]
         config.proxy.no_proxy = ENV["NO_PROXY"]
      end  
- ↑を追加  

## 3-1 ansibleでwordpressをうごかす

1. hostsファイルを作成  
   `$ echo 自分のサーバーのIPアドレス > hosts`  
2. playbook.ymlをつくって設定を書く
3. ディレクトリをつくる  
   `$ mkdir nginx php-fpm wordpress`   
4. コマンドでplaybookを実行する  
   `$ ansible-playbook playbook.yml -i hosts  --private-key ~/.vagrant.d/insecure_private_key -u vagrant -k -vv`
5. wordpressが立ち上がってるか確認する  
   `自分のIPアドレス/wp-admin`

