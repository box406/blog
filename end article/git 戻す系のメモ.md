	Git 戻す系の処理を自分の理解の為にまとめてみた
----

先週Gitでファイルを巻き戻す時にどうするのかを聞かれてうまく説明できなかったので以下に自分がちゃんと
理解していないかを痛感したので自分の為にまとめてみる事にしました

※Gitのinstallや基本的な操作についてはふれません

Gitは以下の3つのファイルの状態を持っています

* ワーキングツリー

    現在のファイルの状態

* インデックス

    addした時点のファイルの状態

* HEAD
    commitした時点の状態

Gitが上記の3つのファイルの状態を持っている事をふまえて一連の流れを書きます
----

1. git init

    適当なディレクトリを作成しそのディレクトリ内で以下のコマンドを発行する

        $ git init

    __この時点のGitの状態__

    ![](https://www.evernote.com/shard/s11/sh/ec8c1d5b-3945-4cb1-a023-3e03cda66862/f6b76628642365eb03fa48575b026fdb/deep/0/gitの説明.key.png)

2. ファイルを作成する

        $ vim index.html
        <!DOCTYPE HTML>
        <html lang="jp">
        <head>
        <meta charset="UTF-8">
        <title></title>
        </head>
        <body>
        </body>
        </html>

    __この時点のGitの状態__

    ![](https://www.evernote.com/shard/s11/sh/eb7dd6e3-db22-484c-9123-5e283aa41d90/f7c27169acee2049a224037e040774bb/deep/0/gitの説明.key.png)

    ファイルが作成されたのでワーキングツリーの位置が進みます

3. add する

		$ git add index.html
		
    __この時点のGitの状態__
	
	![](https://www.evernote.com/shard/s11/sh/847ed4b9-5ea0-41ef-a6b9-e44280232646/e867a129ce1f4584d70a5bfe211beb74/deep/0/gitの説明.key.png)
	
	インデックスの位置が進みます

4. commit　する

		$ git commit -m "first commit"
		[master (root-commit) 9d3f252] first commit
		 1 file changed, 9 insertions(+)
		 create mode 100644 index.html

    __この時点のGitの状態__

	![](https://www.evernote.com/shard/s11/sh/dc76454b-cd37-4d18-8bd2-9f44ca55cb1f/653a53c8e7642a194b4b9db54990eb58/deep/0/gitの説明.key.png)


基本的にはpushしてリモートリポジトリに追加して一連の流れとしてはOKかと思います

次はファイルを巻き戻す方法を以下に書いていきたいと思います


修正しているファイルの修正箇所を取り消す
----

1. index.htmlを編集する
	

	![](https://www.evernote.com/shard/s11/sh/8b7c60e5-448a-437a-901f-e4f910311c47/5c3a799c12959bdcb193bc2fd9c8c784/deep/0/gitの説明.key.png)

	ワーキングツリーのファイルを修正したのワーキングツリーが1つ進む
	
2. 修正している箇所を取り消す

	ワーキングツリーを戻す
	
		$ git checkout index.html
		
	![](https://www.evernote.com/shard/s11/sh/ec8c1d5b-3945-4cb1-a023-3e03cda66862/f6b76628642365eb03fa48575b026fdb/deep/0/gitの説明.key.png)

addを取り消す
----

1. index.htmlを修正、add(インデックスに登録する)

	![](https://www.evernote.com/shard/s11/sh/b407e6c8-bfb1-4da1-aa62-0ecabf228c0b/9e03953f247f3996706bff61dca6271a/deep/0/gitの説明.key.png)

2. addを取り消す

	* addだけ取り消す

			$ git reset HEAD

		この状態になる
		
		![](https://www.evernote.com/shard/s11/sh/8b7c60e5-448a-437a-901f-e4f910311c47/5c3a799c12959bdcb193bc2fd9c8c784/deep/0/gitの説明.key.png)

	* ワーキングツリーの変更も取り消す
	
			$ git reset --hard HEAD
	
		この状態になる

	![](https://www.evernote.com/shard/s11/sh/ec8c1d5b-3945-4cb1-a023-3e03cda66862/f6b76628642365eb03fa48575b026fdb/deep/0/gitの説明.key.png)

commit を取り消す
----

1. index.htmlを修正して、add, commitをする

	![](https://www.evernote.com/shard/s11/sh/e1057931-d0c7-43da-a47a-79d0af5aa4a0/2205f58585ac0bfc52d34274e25151cd/deep/0/gitの説明.key.png)

2. commit を取り消す

	* commit だけを直前に戻す
	
			$ git reset --soft HEAD^
	
		この状態になる
		
		![](https://www.evernote.com/shard/s11/sh/c8c524ec-76b3-4629-8a84-2246ed0aff63/729dfe2f77806bbf49ce1da0a12f89cf/deep/0/gitの説明.key.png)
	
	* commitとaddを直前に戻す
	
			$ git reset HEAD^
	
		![](https://www.evernote.com/shard/s11/sh/9c7c8040-e2d6-4881-b2a8-bdd18fbc2d5f/921f478c64f760f3b22b13937df64c9e/deep/0/gitの説明.key.png)
	
	* ワーキングツリーも含めて直前に戻す
	
			$ git reset --hard HEAD^
	
		![](https://www.evernote.com/shard/s11/sh/ec8c1d5b-3945-4cb1-a023-3e03cda66862/f6b76628642365eb03fa48575b026fdb/deep/0/gitの説明.key.png)


まとめ
----

* HEAD, HEAD^

	* HEAD
	
		最新のcommitの位置
		
	* HEAD^
	
		一つ前のcommitの位置

	![](https://www.evernote.com/shard/s11/sh/d9f95a51-4e96-40ae-bb0a-8e61371c6931/abe45f644ebc32ffa03ba521eabc5baf/deep/0/gitの説明.key.png)

	※commit IDを指定して戻す事も可能
	
		$ git reser [commit id]

* --soft, --hard, no option

	* --soft
	
		HEADを操作する
		
	* option無し
	
		HEAD, indexを操作する
		
	* --hard
	
		HEAD, index, ワーキングツリーを操作する

	![](https://www.evernote.com/shard/s11/sh/61255ac4-c530-4dc7-b294-519c2be8a110/9969428fbb3f40f16ab12f2289b26b87/deep/0/gitの説明.key.png)

だいぶ頭を整理できましたようするに

	git reset [なにを戻すかを指定する] [どの位置まで戻すかを指定する]	

って感じですか、間違ってたら教えて下さい

でわでわ

#### 参考URLにしたページ

[Qiita git-resetは結局何を戻すのか](http://qiita.com/fnobi/items/ec036c1b5d7ee5a8517c)

[murankの日記 git reset についてもまとめてみる](http://d.hatena.ne.jp/murank/20110327/1301224770)
