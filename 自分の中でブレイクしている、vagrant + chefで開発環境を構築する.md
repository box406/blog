@ 自分の中でブレイクしている、vagrant + chefで開発環境を構築する
====

現代会ではvagrabt + chef これがもっともリリース環境に近い開発環境を構築する最適な方法ではないかと思います

今回はLAMP環境（Linux + Apache + mysql + php）のテスト環境を構築してみようと思います

今時はnginxだとかrubyだとか聞こえて来そうですが、現在進行中のプロジェクトの基本的な構成が上記なので今回は

LAMP環境を開発環境として構築していきます

virtualbox + vagrant の設定
----

1. virtualboxの設定

    <img src="https://www.evernote.com/shard/s11/sh/fe7c0eec-ab7e-4332-8bc6-8114edbdbb98/d60634f15fadaf1fcc2b528c39303d4c/deep/0/%E3%82%B9%E3%82%AF%E3%83%AA%E3%83%BC%E3%83%B3%E3%82%B7%E3%83%A7%E3%83%83%E3%83%88%202013/05/17%2021:45.png" alt="%E3%82%B9%E3%82%AF%E3%83%AA%E3%83%BC%E3%83%B3%E3%82%B7%E3%83%A7%E3%83%83%E3%83%88%202013/05/17%2021:45" />

        https://www.virtualbox.org/

2. vagrantの設定

     <img src="https://www.evernote.com/shard/s11/sh/676dafa4-c22f-4d1c-9fd1-dd0125db3b91/2dc4b0af999efda838254fde5f3a25a9/deep/0/%E5%85%A8%E7%94%BB%E9%9D%A2%202013/05/17%2021:27.png" alt="%E5%85%A8%E7%94%BB%E9%9D%A2%202013/05/17%2021:27" />

        http://www.vagrantup.com/

上記のサイトからお使いの環境に合わせたパッケージをダウンロードしてインストールを行ってください

インストールの詳しい説明は割愛します

