Node.js 超初心者勉強会 in 大阪に行ってきました

[Node.js 超初心者勉強会 in 大阪](http://atnd.org/events/39286/)

nodebrew を使ってnode.jsの設定、node.jsのMVCフレームワーク（厳密にはMVCではない）expressの設定、

簡単のプログラムを実際に動かすまでをワークショプ形式で行う勉強会でした

僕は事前にvagrantで環境を構築していったのですが、nodebrew の設定は行っていませんでしたので、@kamiyam先生の

進めるワークショップにそって、nodebreの設定をvagrant(linux / ubuntu)に設定を行いました

以下設定のログと最後に勉強会の感想を書きます

vagrant の設定
----

1. box の設定

    vagrant 配っている Ubuntu lucid 64(http://files.vagrantup.com/lucid64.box)を使用

        $ vagrant box add ubuntu http://files.vagrantup.com/lucid64.box

2. vagrant を初期化

    適当なディレクトリに移動、ここでは「Users/[username]/Sites/node_test/」で作業をすすめます

        $ cd Users/[username]/Sites/node_test/
        $ vagrant init ubuntu

3. ブラウザからアクセスできるようにipアドレスがふられるようにする

    「vagarant init」を行った場所にVagrantfileが作成されているかと思います 

        $ vim Vagrantfile
        config.vm.network :private_network, ip: "192.168.33.10"

    上記がコメントアウトされているのでコメントを外す、設定するipは「192.168.33.10」以外でも問題はないかと思います

4. 仮想サーバを軌道

        $ vagrant up

    これで仮想サーバが軌道すると思います

5. 仮想サーバにログイン

        $ vagrant ssh

    仮想サーバにログインしていると思います

これでvagrant の準備は完了です

nodebrewの設定
----

基本的には以下のgithubのページにすべて書かれています

[github hokaccha / nodebrew](https://github.com/hokaccha/nodebrew)

軌道したての仮想環境（以下ubuntu）にはcurlが入っていませんでしたので、上記のページ書かれてcurlを使わない方法でinstall

1. download and setup

        $ wget git.io/nodebrew
        $ perl nodebrew setup

2. .bashrc にパスの設定を追加

        $ vi ~/.bashrc
        export PATH=$HOME/.nodebrew/current/bin:$PATH

3. .bashrcに追加した設定を反映

        $ source ~/.bashrc

4. nodebrew の確認

    これでinstall は完了しているはずなので確認、下記が表示されればOK

        $ nodebrew --help
        nodebrew 0.6.2

        Usage:
            nodebrew help                       Show this message
            nodebrew install <version>          Download and install a <version> (compile from source)
            nodebrew install-binary <version>   Download and install a <version> (binary file)
            nodebrew uninstall <version>        Uninstall a version
            nodebrew use <version>              Use <version>
            nodebrew list                       List installed versions
            nodebrew ls                         Alias for `list`
            nodebrew ls-remote                  List remote versions
            nodebrew ls-all                     List remote and installed versions
            nodebrew alias <key> <version>      Set alias to version
            nodebrew unalias <key>              Remove alias
            nodebrew clean <version> | all      Remove source file
            nodebrew selfupdate                 Update nodebrew
            nodebrew migrate-package <version>  Install global NPM packages contained in <version> to current version

        Example:
            nodebrew install v0.6.0     Install a specific version number
            nodebrew use v0.6.0         Use a specific version number

node.jsのinstall
----

nodebrewでnode.jsをinstall する場合2種類の方法でinstallする事ができます、一つはsourceからコンパイルする方法と、コンパイル済みのバイナリをinstallする方法です

軌道したてのubuntuにはmakeが設定されていなかったので、sourceからのインストールではなく、バイナリからinstallします

1. install 可能なバージョンの確認

        $ nodebrew ls-remote
        v0.0.1    v0.0.2    v0.0.3    v0.0.4    v0.0.5    v0.0.6

        v0.1.0    v0.1.1    v0.1.2    v0.1.3    v0.1.4    v0.1.5    v0.1.6    v0.1.7
        v0.1.8    v0.1.9    v0.1.10   v0.1.11   v0.1.12   v0.1.13   v0.1.14   v0.1.15
        v0.1.16   v0.1.17   v0.1.18   v0.1.19   v0.1.20   v0.1.21   v0.1.22   v0.1.23
        v0.1.24   v0.1.25   v0.1.26   v0.1.27   v0.1.28   v0.1.29   v0.1.30   v0.1.31
        v0.1.32   v0.1.33   v0.1.90   v0.1.91   v0.1.92   v0.1.93   v0.1.94   v0.1.95
        v0.1.96   v0.1.97   v0.1.98   v0.1.99   v0.1.100  v0.1.101  v0.1.102  v0.1.103
        v0.1.104

        v0.2.0    v0.2.1    v0.2.2    v0.2.3    v0.2.4    v0.2.5    v0.2.6

        v0.3.0    v0.3.1    v0.3.2    v0.3.3    v0.3.4    v0.3.5    v0.3.6    v0.3.7
        v0.3.8
        
        v0.4.0    v0.4.1    v0.4.2    v0.4.3    v0.4.4    v0.4.5    v0.4.6    v0.4.7
        v0.4.8    v0.4.9    v0.4.10   v0.4.11   v0.4.12
        
        v0.5.0    v0.5.1    v0.5.2    v0.5.3    v0.5.4    v0.5.5    v0.5.6    v0.5.7
        v0.5.8    v0.5.9    v0.5.10
        
        v0.6.0    v0.6.1    v0.6.2    v0.6.3    v0.6.4    v0.6.5    v0.6.6    v0.6.7
        v0.6.8    v0.6.9    v0.6.10   v0.6.11   v0.6.12   v0.6.13   v0.6.14   v0.6.15
        v0.6.16   v0.6.17   v0.6.18   v0.6.19   v0.6.20   v0.6.21
        
        v0.7.0    v0.7.1    v0.7.2    v0.7.3    v0.7.4    v0.7.5    v0.7.6    v0.7.7
        v0.7.8    v0.7.9    v0.7.10   v0.7.11   v0.7.12
        
        v0.8.0    v0.8.1    v0.8.2    v0.8.3    v0.8.4    v0.8.5    v0.8.6    v0.8.7
        v0.8.8    v0.8.9    v0.8.10   v0.8.11   v0.8.12   v0.8.13   v0.8.14   v0.8.15
        v0.8.16   v0.8.17   v0.8.18   v0.8.19   v0.8.20   v0.8.21   v0.8.22   v0.8.23
        
        
        v0.9.0    v0.9.1    v0.9.2    v0.9.3    v0.9.4    v0.9.5    v0.9.6    v0.9.7
        v0.9.8    v0.9.9    v0.9.10   v0.9.11   v0.9.12
        
        v0.10.0   v0.10.1   v0.10.2   v0.10.3   v0.10.4   v0.10.5   v0.10.6   v0.10.7
        v0.10.8
        
        v0.11.0   v0.11.1   v0.11.2

2. install する

    安定版の最新は「v0.10.8」なので今回はそれをinstallする

        $ nodebrew install-binary v0.10.8
        fetch: http://nodejs.org/dist/v0.10.7/node-v0.10.8-linux-x64.tar.gz
        --2013-05-27 16:27:13--  http://nodejs.org/dist/v0.10.8/node-v0.10.8-linux-x64.tar.gz
        Resolving nodejs.org... 165.225.133.150
        Connecting to nodejs.org|165.225.133.150|:80... connected.
        HTTP request sent, awaiting response... 200 OK
        Length: 5279340 (5.0M) [application/octet-stream]
        Saving to: `/home/vagrant/.nodebrew/src/v0.10.8/node-v0.10.8-linux-x64.tar.gz'
        
        100%[=================================================================================================================================>] 5,279,340   1.     19M/s   in 5.7s     
        
        2013-05-27 16:27:19 (901 KB/s) - `/home/vagrant/.nodebrew/src/v0.10.8/node-v0.10.8-linux-x64.tar.gz' saved [5279340/5279340]
        
        install success

    これでinstall完了です

3. install されているnode.jsのどのバージョンをつかうかを指定する

    installされているバージョンの確認

        $ nodebrew ls 
        v0.10.2
        v0.10.7
        v0.10.8

    使用するバージョンを指定する

        $ nodebrew use v0.10.8
        use v0.10.8

    確認

        $ node -v
        v0.10.8

    上記の表示がされればバージョンは変更されています

hello node.jsを表示させてみる
----

ファイルの編集はubuntuではなくmacで行う

    $ cd Users/[username]/Sites/node_test/
    $ mkdir node
    $ vim node/app.js

ip addressを「192.168.33.10」に設定している

    var http = require('http');
    http.createServer(function (req, res) {
      res.writeHead(200, {'Content-Type': 'text/plain'});
      res.end('Hello node.js\n');
    }).listen(1337, "192.168.33.10");
    console.log('Server running at http://192.168.33.10/');

今回はlocalhost上のnode.jsではなく仮想サーバ上のubuntuに設定したnode.jsを動かすため、上記のように設定しています

「vagrant up」を行ったディレクトリは軌道した仮想サーバの「/vagrant」ディレクトリにマウントされます

ローカルマシン

    $ cd cd Users/[username]/Sites/node_test/
    $ ls -la
    drwxr-xr-x   3 user  staff   102  5 25 10:11 .vagrant
    -rw-r--r--   1 user  staff  4341  5 25 11:08 Vagrantfile
    drwxr-xr-x   3 user  staff   102  5 25 11:26 node

仮想サーバ（ubuntu）

    $ cd /vagrant
    $ ls -la
    drwxr-xr-x  1 vagrant vagrant  102 May 25 03:11 .vagrant
    -rw-r--r--  1 vagrant vagrant 4341 May 25 04:08 Vagrantfile
    drwxr-xr-x  1 vagrant vagrant  102 May 25 04:26 node

仮想サーバではuser/group がvagrantになります

仮想サーバ上で以下のコマンドを実行

    $ cd node
    $ node app.js

ローカルマシンのブラウザから「http://192.168.33.10:1337」にアクセス以下の画面が表示されればOKです

<img src="https://www.evernote.com/shard/s11/sh/f6ca4adc-9409-4eb0-9bef-fa3567a1865f/811e5c0ba524d047df12564852e3a8f6/deep/0/%E3%82%B9%E3%82%AF%E3%83%AA%E3%83%BC%E3%83%B3%E3%82%B7%E3%83%A7%E3%83%83%E3%83%88%202013/05/27%2023:57.png" alt="%E3%82%B9%E3%82%AF%E3%83%AA%E3%83%BC%E3%83%B3%E3%82%B7%E3%83%A7%E3%83%83%E3%83%88%202013/05/27%2023:57" />

expressの設定
----

node.js のフレームワークの１つであるexpressを設定

※以下仮想環境のubuntuで行う

1. install 

    globalにinstallを行う、「-g」オプションをつけるとグローバル環境に設定を行う

        $ npm install -g express

2. version の確認

    install されたexpressのバージョンを確認

    $ $ express --version
    3.2.5

3. expressでアプリケーションのひな形を生成

         $ express [アプリケーション名]
           create : express
           create : express/package.json
           create : express/app.js
           create : express/routes
           create : express/routes/index.js
           create : express/routes/user.js
           create : express/views
           create : express/views/layout.jade
           create : express/views/index.jade
           create : express/public
           create : express/public/stylesheets
           create : express/public/stylesheets/style.css
           create : express/public/images
           create : express/public/javascripts      
        
           install dependencies:
             $ cd express && npm install
        
           run the app:
             $ node app

4. 必要なモジュールのダウンロードを行う

        $ cd [アプリケーション名]
        $ npm install

    package.jsonに書かれている内容の設定を行う

5. expressの生成したプロジェクトを軌道してみる

        $ node app.js
        Express server listening on port 3000

6. ブラウザでアクセスしてみる

    今回は設定されているportが3000なので以下のURLにアクセスする

        http://192.168.33.10:3000/

    <img src="https://www.evernote.com/shard/s11/sh/9d02c0b9-6d5c-4441-b535-0633b57ca36a/9c6958d500e9c4586959e1dfde108359/deep/0/%E3%82%B9%E3%82%AF%E3%83%AA%E3%83%BC%E3%83%B3%E3%82%B7%E3%83%A7%E3%83%83%E3%83%88%202013/05/28%200:14.png" alt="%E3%82%B9%E3%82%AF%E3%83%AA%E3%83%BC%E3%83%B3%E3%82%B7%E3%83%A7%E3%83%83%E3%83%88%202013/05/28%200:14" />

とりあえずここまで、実際n勉強会ではテンプレートエンジンの話しがありましたが、ここでは割愛します

実際に使ってみて、改めてエントリーします

また、午後には行けてないんですが、Sails.jsについても気になります、ruby on railsライクなフレームワークらしいのでとっつきやすい気がします

是非次回も参加したと思える勉強会でした、やっぱり発表形式の勉強会もよいですが、実際に手を動かす勉強会が楽しいです

今後もそういった勉強会には参加したいです

@kamiyam先生ありがとうございました

