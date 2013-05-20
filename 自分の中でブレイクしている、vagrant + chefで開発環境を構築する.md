@ 自分の中でブレイクしている、vagrant + chefで開発環境を構築する
====

プログラマーとして仕事をするようになって常々感じていた課題の一つとして本番環境(linux)と開発環境の違いがあります

開発マシンをwindowsからmacに変えたのも環境の違いをなるべくなくすためでした

また複数人で開発を行う場合、各々の開発環境の違いは本当に悩みの種でした

これまでにvmwereなどを導入してファイルを配ったり、構築手順書を作って配ったりしていましたがやはりベストな方法ではありませんでした

vmを使った場合結局scp/ftpなどで編集したファイルのアップロードを行う必要があったり、開発サーバの構成管理が出来ませんでした

この問題を解決してくれる方法が今僕の中でブレイクしているvirtualbox + vagrant + chef + gitで構築する開発環境構築です

今回はcakephpでプロジェクトを進める場合の環境構築してみます

1. virtualbox + vagrant の設定
----

1. virtualboxの設定

    <img src="https://www.evernote.com/shard/s11/sh/fe7c0eec-ab7e-4332-8bc6-8114edbdbb98/d60634f15fadaf1fcc2b528c39303d4c/deep/0/%E3%82%B9%E3%82%AF%E3%83%AA%E3%83%BC%E3%83%B3%E3%82%B7%E3%83%A7%E3%83%83%E3%83%88%202013/05/17%2021:45.png" alt="%E3%82%B9%E3%82%AF%E3%83%AA%E3%83%BC%E3%83%B3%E3%82%B7%E3%83%A7%E3%83%83%E3%83%88%202013/05/17%2021:45" />

    [ここからvirtualboxをダウンロード](https://www.virtualbox.org/)

2. vagrantの設定

     <img src="https://www.evernote.com/shard/s11/sh/676dafa4-c22f-4d1c-9fd1-dd0125db3b91/2dc4b0af999efda838254fde5f3a25a9/deep/0/%E5%85%A8%E7%94%BB%E9%9D%A2%202013/05/17%2021:27.png" alt="%E5%85%A8%E7%94%BB%E9%9D%A2%202013/05/17%2021:27" />

    [ここからvagrantをダウンロード](http://www.vagrantup.com/)

上記のサイトからお使いの環境に合わせたパッケージをダウンロードしてインストールを行ってください

インストールの詳しい説明は割愛します

