@chef-solo の使い方
====

vagrantの設定
----

* vagrant で立ち上げたOSをbridgeネットワークにする

        # sudo vim Vagrantfile
        config.vm.network :bridged, :bridge => "en0: Ethernet"
        # sudo vagrant up

    上記の設定でsshコマンドからもログインが可能になる

        # sudo vagrant sssh

        # ipconfig 
        eth1      Link encap:Ethernet  HWaddr 08:00:27:EE:B2:27
        inet addr:192.168.0.118  Bcast:192.168.0.255  Mask:255.255.255.0
        inet6 addr: fe80::a00:27ff:feee:b227/64 Scope:Link
        UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
        RX packets:185 errors:0 dropped:0 overruns:0 frame:0
        TX packets:13 errors:0 dropped:0 overruns:0 carrier:0
        collisions:0 txqueuelen:1000
        RX bytes:25794 (25.1 KiB)  TX bytes:1445 (1.4 KiB)

    **※ デフォルトで設定されているユーザ／パスワードは以下**

        vagrant / vagrant
        root / vagrant


knife-soloの設定
----

#### knife-soloを設定して開発マシン（mac）からchef-soloを実行する

**※ruby, gems, gitはインストールされている前提**

1. gemをアップデート

    # sudo gem update

2. bundleをインストールする

    # sudo gem instll bundle

3. githubからknife-soloの0.3.0を取得

    # git clone git clone git://github.com/matschaffer/knife-solo.git

4. knife-solo0.3.0をインストール

    # cd knife-solo
    # bundle
    # sudo rake install
    # mkdir ~/.chef
    # vim ~/.chef/knife.rb
    knife[:solo_path] = '/tmp/chef-solo'

    **※gemでもインストール可能ですが、0.2.0しか入りません

        # gem install knife-solo

これで開発マシン(mac)にknife-soloが設定されているかと思います

knife-soloでノードにレシピを実行する
----

レシピを適用するノードはvagrantで用意

1. ノードの用意

    ブリッッジネットワークを設定

        # sudo vagrant init
        # sudo vagrant up
        # sudo vagrant ssh
        # ipconfig 
        eth1      Link encap:Ethernet  HWaddr 08:00:27:EE:B2:27
        inet addr:192.168.0.117  Bcast:192.168.0.255  Mask:255.255.255.0
        inet6 addr: fe80::a00:27ff:feee:b227/64 Scope:Link
        UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
        RX packets:185 errors:0 dropped:0 overruns:0 frame:0
        TX packets:13 errors:0 dropped:0 overruns:0 carrier:0
        collisions:0 txqueuelen:1000
        RX bytes:25794 (25.1 KiB)  TX bytes:1445 (1.4 KiB)

    ユーザ／パスワードはデフォルトを使用

        vagrant / vagrant
        root / vagrant

2. chef レポジトリを作る

        # cd /Users/[username]/Sites/recipe/
        # knife solo init chef-repo
    
    ついでにgitでの管理も行う

        # git init
        # git add -A
        # git commit -m "first commit"

3. 対象のノードの環境を整える

        # knife solo prepare vagrant@192.168.0.117
        Bootstrapping Chef...
        Enter the password for vagrant@192.168.0.117:
          % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                         Dload  Upload   Total   Spent    Left  Speed
        101  6471  101  6471    0     0   6204      0  0:00:01  0:00:01 --:--:-- 36150
        Downloading Chef 11.4.0 for el...
        Installing Chef 11.4.0
        warning: /tmp/tmp.wqEXvPmS/chef-11.4.0.x86_64.rpm: Header V4 DSA/SHA1 Signature, key ID 83ef826a: NOKEY
        Preparing...                ########################################### [100%]
           1:chef                   ########################################### [100%]
    
    対象ノードのchef-soloの環境をよしなに揃えてくれる

    ##### ※気をつけるポイント
    
        # knife solo prepare vagrant@192.168.0.117

    ホスト名@IPアドレスを指定する箇所でrootを指定するとsudoできないユーザですとエラーがはかれる、指定するユーザはsudoが可能なユーザ名を設定する

4. cookbookを作成する

        # knife cookbook create httpd -o site-cookbooks
        # ll
        cookbooks
        data_bags
        nodes
        　|_192.168.0.117.json
        roles
        site-cookbooks
          |_httpd

    上記が作成されます

    自分がつくるクックブックは「site-cookbooks」に格納する

    ダウンロードしてきたクックブックは「cookbook」に格納する

5. レシピを作成

    **※お使いのeditorでレシピを編集します、ここではvimを使います**

        # vim site-cookbooks/httpd/recipes/default.rb
        package "httpd" do
          action :install
        end

6. node/[****].jsonを編集

    **※お使いのeditorでレシピを編集します、ここではvimを使います**

    run_listに作成してレシピを追加します

        # vim nodes/192.168.0.117.json
        {"run_list":["recipe[httpd]"]}

7. 対象ノードのchef-soloを実行する

        # knife solo cook vagrant@192.168.0.17
        Running Chef on 192.168.0.117...
        Checking Chef version...
        Enter the password for vagrant@192.168.0.117:
        Syncing kitchen...
        vagrant@192.168.0.117's password:
        Adding patches...
        vagrant@192.168.0.117's password:
        Permission denied, please try again.
        vagrant@192.168.0.117's password:
        vagrant@192.168.0.117's password:
        Syncing solo config...
        vagrant@192.168.0.117's password:
        Running Chef...
        Starting Chef Client, version 11.4.0
        Compiling Cookbooks...
        Converging 1 resources
        Recipe: httpd::default
          * package[httpd] action install
            - install version 2.2.15-26.el6.centos of package httpd
    
        Chef Client finished, 1 resources updated

    何度かパスワードを聞かれますので、都度パスワードを入力する

    ##### ※気をつけるポイント
    
        # knife solo prepare vagrant@192.168.0.117

    ホスト名@IPアドレスを指定する箇所でrootを指定するとsudoできないユーザですとエラーがはかれる、指定するユーザはsudoが可能なユーザ名を設定する

8. 対象ノードにhttpdがインストールされている事を確認

9. レシピをcommitする

        # git add -A
        # git commit -m "add httpd recipe"

    commit はもっと頻繁に行うべきかと思いますがここでは省きます


saharaでOSのrollbackを行う
----

OSの状態を記憶しその状態に戻す事ができるプラグイン、SQLのトランザクション（begin commit rollback）のような機能です

1. saharaのインストール

    # vagrant gem install sahara

2. 現在の状態を記憶

    # vagrant sandbox on

3. 記憶した状態に戻す

    # vagrant sandbox rollback

4. 確定する

    # vagrant sandbox commit

5. sandboxを解除する

    # vagrant sandbox off