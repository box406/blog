@ gangliaを設定したのでメモ
====

100台規模のサーバを１人で運用監視する可能性が出てきたので色々調べたみた結果Gangliaをとりあえず使って
見る事にした

以下はその設定メモです

とりあえずテスト的に以下のサーバを監視する設定をして見ました

<img src="https://www.evernote.com/shard/s11/sh/e20e564d-3086-49cb-b7ac-1ed053745e00/e05ab532525f2705bb0947e5b5716182/deep/0/%E3%83%97%E3%83%AC%E3%82%BC%E3%83%B3%E3%83%86%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B31.png" />

##### ※前提としてサーバOSはcentOS6.4 64bitを使います、Apache, phpのインストールは省きます

1. ganglia のインストール

    * 共通の設定

        epelリポジトリの追加

            # wget http://ftp-srv2.kddilabs.jp/Linux/distributions/fedora/epel/6/x86_64/epel-release-6-8.noarch.rpm
            # rpm -ivh epel-release-6-8.noarch.rpm

    * 監視サーバ


    * 監視対象サーバ