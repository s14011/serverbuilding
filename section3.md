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


