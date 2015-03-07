@ 自分の中でブレイクしている、vagrant + chef + gitで開発環境を構築する
====

プログラマーとして仕事をするようになって常々感じていた課題の一つとして本番環境(linux)と開発環境の違いがあります

開発マシンをwindowsからmacに変えたのも環境の違いをなるべくなくすためでした

また複数人で開発を行う場合、各々の開発環境の違いは本当に悩みの種でした

これまでにvmwereなどを導入してファイルを配ったり、構築手順書を作って配ったりしていましたがやはりベストな方法ではありませんでした

vmを使った場合結局scp/ftpなどで編集したファイルのアップロードを行う必要があったり、開発サーバの構成管理が出来ませんでした

この問題を解決してくれる方法が今僕の中でブレイクしているvirtualbox + vagrant + chef + gitで構築する開発環境構築です

今回はcakephpでプロジェクトを進める場合の環境構築してみます

virtualbox + vagrant の設定
----

1. virtualboxの設定

    <img src="https://www.evernote.com/shard/s11/sh/fe7c0eec-ab7e-4332-8bc6-8114edbdbb98/d60634f15fadaf1fcc2b528c39303d4c/deep/0/%E3%82%B9%E3%82%AF%E3%83%AA%E3%83%BC%E3%83%B3%E3%82%B7%E3%83%A7%E3%83%83%E3%83%88%202013/05/17%2021:45.png" alt="%E3%82%B9%E3%82%AF%E3%83%AA%E3%83%BC%E3%83%B3%E3%82%B7%E3%83%A7%E3%83%83%E3%83%88%202013/05/17%2021:45" />

    [ここからvirtualboxをダウンロード](https://www.virtualbox.org/)

2. vagrantの設定

     <img src="https://www.evernote.com/shard/s11/sh/676dafa4-c22f-4d1c-9fd1-dd0125db3b91/2dc4b0af999efda838254fde5f3a25a9/deep/0/%E5%85%A8%E7%94%BB%E9%9D%A2%202013/05/17%2021:27.png" alt="%E5%85%A8%E7%94%BB%E9%9D%A2%202013/05/17%2021:27" />

    [ここからvagrantをダウンロード](http://www.vagrantup.com/)

上記のサイトからお使いの環境に合わせたパッケージをダウンロードしてインストールを行ってください

インストールの詳しい説明は割愛します

仮想環境をセットアップ
----

1. box をダウンロード

    [http://www.vagrantbox.es/](http://www.vagrantbox.es/)からダウンロードを行います、今回はvagrant が用意している「Ubuntu lucid 64」を使用します

        $ vagrant box add ubuntu http://files.vagrantup.com/lucid64.box

    ダウンロードが完了したら以下のコマンドを発行して確認を行って下さい

        $ vagrant box list
        ubuntu    (virtualbox)

2. vagrant init

    まず今回は「/Users/[username]]/Sites/」にcakephp_sampleというディレクトリを作成し、そこのディレクトリにセットアップを行います

    とりあえずvagrant initまで

        $ cd /Users/[username]/Sites
        $ mkdir cakephp_sample
        $ cd cakephp_sample
        $ vagrant init ubuntu
        A `Vagrantfile` has been placed in this directory. You are now
        ready to `vagrant up` your first virtual environment! Please read
        the comments in the Vagrantfile as well as documentation on
        `vagrantup.com` for more information on using Vagrant.

    確認

        $ ls -l
        -rw-r--r--  1 [username]  staff  4343  5 28 22:59 Vagrantfile 

    Vagrantfile が作成されている事を確認して下さい

3. vagrant up

    vagrant を軌道します、Vagrantfileが作成されているディレクトリで vagarant up コマンドを発行

        $ cd /Users/[username]/Sites/cakephp_sample
        $ vagrant up
        Bringing machine 'default' up with 'virtualbox' provider...
        [default] Importing base box 'ubuntu'...
        [default] Matching MAC address for NAT networking...
        [default] Setting the name of the VM...
        [default] Clearing any previously set forwarded ports...
        [default] Creating shared folders metadata...
        [default] Clearing any previously set network interfaces...
        [default] Preparing network interfaces based on configuration...
        [default] Forwarding ports...
        [default] -- 22 => 2222 (adapter 1)
        [default] Booting VM...
        [default] Waiting for VM to boot. This can take a few minutes.
        [default] VM booted and ready for use!
        [default] Configuring and enabling network interfaces...
        [default] Mounting shared folders...
        [default] -- /vagrant

4. vagrant ssh

    軌道したvagrantにssh で接続してみる、ディレクトリは先ほどと同じ場所

        $ cd /Users/[username]/Sites/cakephp_sample
        $ vagrant ssh
        Linux lucid64 2.6.32-38-server #83-Ubuntu SMP Wed Jan 4 11:26:59 UTC 2012 x86_64 GNU/Linux
        Ubuntu 10.04.4 LTS
        
        Welcome to the Ubuntu Server!
         * Documentation:  http://www.ubuntu.com/server/doc
        New release 'precise' available.
        Run 'do-release-upgrade' to upgrade to it.
        
        Welcome to your Vagrant-built virtual machine.
        Last login: Fri Sep 14 07:31:39 2012 from 10.0.2.2
        vagrant@lucid64:~$

php + httpd + mysql を設定するrecipeを作成する
----

1. recipeを書く準備をする

    recipeを格納するディレクトリを設定する、以下の作業は先ほど軌道した仮想環境ではなくmac側で行います

        $ cd /Users/[username]/Sites/cakephp_sample
        $ mkdir cookbooks

    recipeを作成します、ひな形の設定にknifeコマンドを使用します、knifeコマンドはrubygemsでinstallします、今回はinstallされている前提で話しを進めます

2. recipeのひな形を用意する

    httpd のrecipe用のひな形を作成します

        $ knife cookbook create -o cookbooks httpd
        ** Creating cookbook httpd
        ** Creating README for cookbook: httpd
        ** Creating CHANGELOG for cookbook: httpd
        ** Creating metadata for cookbook: httpd

    確認

        $ ll cookbooks/
        drwxr-xr-x  13 [username]  staff  442  5 28 23:46 httpd

    さらに作成されたrecipeの中を確認

        $ ll cookbooks/httpd
        -rw-r--r--  1 [username]  staff   409  5 28 23:46 CHANGELOG.md
        -rw-r--r--  1 [username]  staff  1439  5 28 23:46 README.md
        drwxr-xr-x  2 [username]  staff    68  5 28 23:46 attributes
        drwxr-xr-x  2 [username]  staff    68  5 28 23:46 definitions
        drwxr-xr-x  3 [username]  staff   102  5 28 23:46 files
        drwxr-xr-x  2 [username]  staff    68  5 28 23:46 libraries
        -rw-r--r--  1 [username]  staff   274  5 28 23:46 metadata.rb
        drwxr-xr-x  2 [username]  staff    68  5 28 23:46 providers
        drwxr-xr-x  3 [username]  staff   102  5 28 23:46 recipes
        drwxr-xr-x  2 [username]  staff    68  5 28 23:46 resources
        drwxr-xr-x  3 [username]  staff   102  5 28 23:46 templates

    続いてmysql, php, groupのレシピのひな形を用意します

        $ knife cookbook create -o cookbooks php
        ** Creating cookbook php
        ** Creating README for cookbook: php
        ** Creating CHANGELOG for cookbook: php
        ** Creating metadata for cookbook: php

        $ knife cookbook create -o cookbooks mysql
        ** Creating cookbook mysql
        ** Creating README for cookbook: mysql
        ** Creating CHANGELOG for cookbook: mysql
        ** Creating metadata for cookbook: mysql        

        $ knife cookbook create -o cookbooks group
        ** Creating cookbook group
        ** Creating README for cookbook: group
        ** Creating CHANGELOG for cookbook: group
        ** Creating metadata for cookbook: group        


3. httpdのレシピを作成する

    先ほど作成したhttpdのcookbookにレシピを書いていきます、とりあえずコードをはりつけます

        $ vim cookbooks/httpd/recipes/default.rb
        execute "apt-get update" do
          command "apt-get update"
        end
        package "apache2" do
          action :install
        end
        bash "rewrite" do
          code <<-EOC
              sudo a2enmod rewrite
          EOC
        end
        service "apache2" do
          supports :status => true, :restart => true, :reload => true
          action [:enable, :start]
        end

    上記を記述する

4. recipeを実行する

    Vagrantfileを編集してvagrant に適用するレシピとcookbookのパスを記述する

        $ vim Vagrantfile
        Vagrant.configure("2") do |config|
          config.vm.box = "ubuntu"
          config.vm.provision :chef_solo do |chef|
            chef.cookbooks_path = "./cookbooks"
            chef.add_recipe "httpd"
          end
        end

    vagrant が軌道する

        $ vagrant up

    vagrant が軌道されている場合は vagrant reloadを実行する

        $ vagrant reload
        [default] Attempting graceful shutdown of VM...
        [default] Setting the name of the VM...
        [default] Clearing any previously set forwarded ports...
        [default] Creating shared folders metadata...
        [default] Clearing any previously set network interfaces...
        [default] Preparing network interfaces based on configuration...
        [default] Forwarding ports...
        [default] -- 22 => 2222 (adapter 1)
        [default] Booting VM...
        [default] Waiting for VM to boot. This can take a few minutes.
        [default] VM booted and ready for use!
        [default] Configuring and enabling network interfaces...
        [default] Mounting shared folders...
        [default] -- /vagrant
        [default] -- /tmp/vagrant-chef-1/chef-solo-1/cookbooks
        [default] Running provisioner: chef_solo...
        Generating chef JSON and uploading...
        Running chef-solo...
        stdin: is not a tty
        [2013-05-29T16:34:57+02:00] INFO: *** Chef 10.14.2 ***
        [2013-05-29T16:34:57+02:00] INFO: Setting the run_list to ["recipe[httpd]"] from JSON
        [2013-05-29T16:34:57+02:00] INFO: Run List is [recipe[httpd]]
        [2013-05-29T16:34:57+02:00] INFO: Run List expands to [httpd]
        [2013-05-29T16:34:57+02:00] INFO: Starting Chef Run for lucid64.hsd1.ca.comcast.net.
        [2013-05-29T16:34:57+02:00] INFO: Running start handlers
        [2013-05-29T16:34:57+02:00] INFO: Start handlers complete.
        [2013-05-29T16:34:57+02:00] INFO: Processing execute[apt-get update] action run (httpd::default line 9)
        [2013-05-29T16:35:36+02:00] INFO: execute[apt-get update] ran successfully
        [2013-05-29T16:35:36+02:00] INFO: Processing package[apache2] action install (httpd::default line 13)
        [2013-05-29T16:37:26+02:00] INFO: Processing service[apache2] action enable (httpd::default line 17)
        [2013-05-29T16:37:26+02:00] INFO: Processing service[apache2] action start (httpd::default line 17)
        [2013-05-29T16:37:26+02:00] INFO: Chef Run complete in 148.550341 seconds
        [2013-05-29T16:37:26+02:00] INFO: Running report handlers
        [2013-05-29T16:37:26+02:00] INFO: Report handlers complete

    recipeが実行されてhttpd（apache2）がinstallされる

    httpd がinstall されているかの確認

        $ vagrant ssh 
        $ apache2 -v
        Server version: Apache/2.2.14 (Ubuntu)
        Server built:   Mar  8 2013 16:46:38

        $ ps ax | grep apache2
         1510 ?        Ss     0:00 /usr/sbin/apache2 -k start
         1512 ?        S      0:00 /usr/sbin/apache2 -k start
         1513 ?        Sl     0:00 /usr/sbin/apache2 -k start
         1514 ?        Sl     0:00 /usr/sbin/apache2 -k start

    上記が確認できればinstall、自動起動の設定は完了しています

    ※ recipeを反映する場合は　vagrant provisionを実行する

        $ vagrant provision

3. mysql のrecipeを用意する

    httpdのレシピと同じように、用意したmysqlのcookbookにレシピを書いていく

        $ vim cookbooks/mysql/recipes/default.rb
        package "mysql-server" do
            action :install
        end
        service "mysql" do
          action [ :enable, :start]
        end

    上記を記述する

4. php のレシピを用意する

    httpdのレシピと同じように、用意したphpのcookbookにレシピを書いていく

        $ vim cookbooks/mysql/recipes/default.rb
        %w{php5 php-pear php5-cli php5-common php5-dev php5-gd php5-mysql libapache2-mod-php5 php-apc}.each do |suffix|
            package suffix do
                action :install
            end
        end

    上記を記述する

    ※レポジトリに登録されているのはphp5.3今なら5.4を入れたいところだが、今回はこのまま5.3をinstallする

5. vagrant 特有の設定を書いていく

    apache2のドキュメントルートを変更する、httpdのレシピに以下のコードを追記する

        $ vim cookbooks/httpd/recipes/default.rb        
        template "/etc/apache2/sites-available/default" do
            source "default.erb"
            owner "root"
            group "root"
            mode 0644
        end

    ※ service起動のコードの上に書く

    template ファイルを「cookbooks/httpd/templates/default」に設置する

    groupの設定を行う、/vagrant に設定されているユーザ／groupはvagrantが設定されているので、apacheを動かしているwww-dataグループにvagrantを追加する

        $ vim cookbooks/group/recipes/default.rb        
        group "www-data" do
            action :modify
            members ['vagrant']
            append true
        end

    ubuntuではapacheを動かしているのはwww-dataというユーザ／グループが設定されている

6. recipeがすべてそろったのでVagrantfileに追加するレシピを追加する

        $ vim Vagrantfile
        Vagrant.configure("2") do |config|
          config.vm.box = "ubuntu"
          config.vm.network :private_network, ip: "192.168.33.10"
          config.vm.provision :chef_solo do |chef|
             chef.cookbooks_path = "./cookbooks"
             chef.add_recipe "httpd"
             chef.add_recipe "mysql"
             chef.add_recipe "php"
             chef.add_recipe "group"
          end
        end

7. recipeを適応する

    $ vagrant reload

cakephpを設定する
----

1. cakephpをダウンロード

    <img src="https://www.evernote.com/shard/s11/sh/c829140e-681b-4f18-930d-81bf69330ad8/26ae3151391f548d52f308b448310866/deep/0/%E3%82%B9%E3%82%AF%E3%83%AA%E3%83%BC%E3%83%B3%E3%82%B7%E3%83%A7%E3%83%83%E3%83%88%202013/05/31%2023:59.png" alt="%E3%82%B9%E3%82%AF%E3%83%AA%E3%83%BC%E3%83%B3%E3%82%B7%E3%83%A7%E3%83%83%E3%83%88%202013/05/31%2023:59" />

    上記のページのダウンロードからcakephpをダウンロードしてくる

    Vagrantfileが設定されているディレクトリに展開する、以下のような状態になる

        $ ls -la
        -rw-r--r--@  1 [username]  staff   3182  5 11 09:56 CONTRIBUTING.md
        -rw-r--r--@  1 [username]  staff   1665  5 11 09:56 README.md
        -rw-r--r--   1 [username]  staff   4408  5 31 23:53 Vagrantfile
        drwxr-xr-x  16 [username]  staff    544  5 11 09:56 app
        -rw-r--r--@  1 [username]  staff    174  5 11 09:56 build.properties
        -rw-r--r--@  1 [username]  staff  10089  5 11 09:56 build.xml
        drwxr-xr-x   6 [username]  staff    204  5 31 23:37 cookbooks
        -rw-r--r--@  1 [username]  staff   1466  5 11 09:56 index.php
        drwxr-xr-x   3 [username]  staff    102  5 11 09:56 lib
        drwxr-xr-x   3 [username]  staff    102  5 11 09:56 plugins
        drwxr-xr-x   3 [username]  staff    102  5 11 09:56 vendors 

2. cakephpの設定を行う、設定はmac側で行う

    * パーミッションの設定

            $ chmod -R 777 app/tmp

    * データベースの設定を行う

    * 上記の設定が完了したらブラウザから「http://192.168.33.10」にアクセスする

        以下の画面が表示されれば設定は完了です

        <img src="https://www.evernote.com/shard/s11/sh/efab902e-c647-4c3b-92b1-147bb5e035e2/053b238c2154cc9bc4de0d8210488462/deep/0/%E3%82%B9%E3%82%AF%E3%83%AA%E3%83%BC%E3%83%B3%E3%82%B7%E3%83%A7%E3%83%83%E3%83%88%202013/06/01%200:38.png" alt="%E3%82%B9%E3%82%AF%E3%83%AA%E3%83%BC%E3%83%B3%E3%82%B7%E3%83%A7%E3%83%83%E3%83%88%202013/06/01%200:38" />

git に追加する
----

この状態でgitに追加する、今回はgithubに追加しておきました

<img src="https://www.evernote.com/shard/s11/sh/07f38142-73cc-455b-b150-3f70a5dbfeae/04204b050254d65cd92165f335a3208d/deep/0/%E3%82%B9%E3%82%AF%E3%83%AA%E3%83%BC%E3%83%B3%E3%82%B7%E3%83%A7%E3%83%83%E3%83%88%202013/06/01%200:46.png" alt="%E3%82%B9%E3%82%AF%E3%83%AA%E3%83%BC%E3%83%B3%E3%82%B7%E3%83%A7%E3%83%83%E3%83%88%202013/06/01%200:46" />

[https://github.com/box406/cakephp_sample](https://github.com/box406/cakephp_sample)においてあります

この状態でgithubにおいておけば、cloneしてvagrant upすればcakephpの環境が３分ぐらいで立ち上がるかと思います

コードを書く
----

ここまでの状態でvagrant upを行います

Vagrantfileがあるディレクトリはvagrant にマウントされます

<img src="https://www.evernote.com/shard/s11/sh/be72aff8-f598-4b80-b06b-a14e76095321/88fde60c4005dd3733a07e47e39e47af/deep/0/1.%20tmux%20(tmux).png" alt="1.%20tmux%20(tmux)" />

これでmac側でコードを編集して実行は仮想サーバで行うが、ファイルのアップロードなどの必要はなくすべてローカルに環境が設置されているかのように開発を進める事ができる

また環境構築はすべてレシピに書かれているので、複数人で開発環境をそろえる事ができるし配布するのも簡単です、今回の場合はgithubに一式があるので、cloneしてくるだけです

vagrant + chefを利用する事でより本番環境に近い環境を作る事ができ、複数人での開発の時のなやみの種だった開発環境の違いが解消されます、gitで管理する事で再配布も簡単に行えます

一番気にいっているのはローカルマシンに直接環境を作っていくとどうしても開発マシンの環境が汚れてくる事です

今回はvagrant + chef + gitを使っての開発環境構築について書いたので、レシピなどは動けばよいていで書いています、実際に環境構築を行う場合はもう少し丁寧にレシピを書いた方が良いと思います

長いのでとりあえずここまでにします

でわでわ