@ serverspec を使って初めてのインフラテスト
====

仕事でserverspecを使ってテストを行ったのでその時のメモを公開します

provisoning toolにはchef(chef-solo)を使っています、今まではvagrant + chefでテスト環境を構築するだけでしたが
production環境にもcookbookを定期用する事になりました、今まではテスト環境の構築だったので例えばiptablesはoffとか
の設定をしていましたがそういうわけにも行かず一度全てのcookbookの見直しを行い、足りない部分は追加しました

production環境にcookbookを適用するにあたってやはりcookbookが正しいかどうかを1台づつ確認するのはさすがに手がかかり
すぎるしコストもかかります、何より正しいかどうかもうたがしいです

って事でテストを書く事にしました、今となっては当たりの事ですが今までアプリのテストは書いてましたがサーバのテストを
書くのは初めてです

サーバのテストを行うにあたって色々ツールがあるようですが、一番簡単で汎用的なserverspecを使う事にしました

以下ログです

$ install 
----

rubyはinstallされている前提です

    # gem install serverspec

$ init
----

1. テストを作成するdirectoryに移動

        # cd [test directory]

2. init 

        # serverspec-init
        
        Select OS type:
        
        1) UN*X
        2) Windows
        
        Select number: 1
        
        Select a backend type:
        
          1) SSH
          2) Exec (local)
        
        Select number: 1
        
        Vagrant instance y/n: n
        Input target host name: 192.168.33.10
         + spec/
         + spec/192.168.33.10/
         + spec/192.168.33.10/httpd_spec.rb
         + spec/spec_helper.rb
         + Rakefile

    色々聞かれるけどとりあえず 1) 1) n を選択

2. 実際のテストに使ったResource Types

    netを検索すると色々情報が出てきますがとりあえず公式サイトが一番わかりやすかったです

    [http://serverspec.org/](http://serverspec.org/)

    [Resource Types](http://serverspec.org/resource_types.html) に使えるresourceとサンプルコードが載っています

    実際に使ったresourceを以下に紹介します

    * package resource

        [package](http://serverspec.org/resource_types.html#package)

            describe package('httpd') do
              it { should be_installed }
            end

        * be_installed

            package('httpd') に記述されているpackageがinstallされているかをテスト

    * file resource

        [file](http://serverspec.org/resource_types.html#file)

            describe file('/etc/httpd/conf/httpd.conf') do
              it { should be_file }
              it { should contain "DocumentRoot \"/home/ecocatcms/current\"" }
            end

        * be_file

            file('/etc/httpd/conf/httpd.conf') に記述されているファイルが存在するかをテスト

        * contain

            file('/etc/httpd/conf/httpd.conf') に記述されているファイルに contain "〜"の記述が書かれているかをテスト

    * service resource

        [service](http://serverspec.org/resource_types.html#service)

            describe service('httpd') do
              it { should be_enabled   }
              it { should be_running   }
            end

        * be_enabled

            service('httpd')に記述されているpackageの起動設定がされているかをテスト

        * be_running

            service('httpd')に記述されているpackageが起動しているかをテスト

    * port resource

        [port](http://serverspec.org/resource_types.html#port)

            describe port(80) do
              it { should be_listening }
            end

        * be_listening

            port(80) に記述されているportが開いているかをテスト

    * command resource

        [command](http://serverspec.org/resource_types.html#command)

            describe command('convert --version') do
              it { should return_stdout /ImageMagick/}
            end

        * return_stdout

            command('convert --version')に記述されたcommandを実行した結果から return_stdout /ImageMagick/ に書かれた記述が存在するをテスト
            記述の仕方は正規表現も使える

    * iptables resource

        [iptables](http://serverspec.org/resource_types.html#iptables)

            describe iptables do
              it { should have_rule('-P INPUT ACCEPT') }
              it { should have_rule('-P FORWARD ACCEPT') }
              it { should have_rule('-P OUTPUT ACCEPT') }
              it { should have_rule('-A INPUT -p icmp -j ACCEPT') }
              it { should have_rule('-A INPUT -i lo -j ACCEPT') }
              it { should have_rule('-A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT') }
              it { should have_rule('-A INPUT -p tcp -m state --state NEW -m tcp --dport 22 -j ACCEPT') }
              it { should have_rule('-A INPUT -p tcp -m state --state NEW -m tcp --dport 80 -j ACCEPT') }
              it { should have_rule('-A INPUT -p tcp -m state --state NEW -m tcp --dport 443 -j ACCEPT') }
              it { should have_rule('-A INPUT -p tcp -m state --state NEW -m tcp --dport 25 -j ACCEPT') }
              it { should have_rule('-A INPUT -p tcp -m state --state NEW -m tcp --dport 587 -j ACCEPT') }
              it { should have_rule('-A INPUT -j REJECT --reject-with icmp-host-prohibited') }
              it { should have_rule('-A FORWARD -j REJECT --reject-with icmp-host-prohibited') }
            end

        * have_rule

            have_rule('-P INPUT ACCEPT')に記述された設定がされているかをテスト

    * php_config resource

        [php_config](http://serverspec.org/resource_types.html#php_config)

            describe 'PHP config parameters' do
              context php_config('date.timezone') do
                its(:value) {should eq 'Asia/Tokyo'}
              end
              context php_config('post_max_size') do
                its(:value) {should eq '5000M'}
              end
              context php_config('upload_max_filesize') do
                its(:value) {should eq '5000M'}
              end
              context php_config('extension_dir') do
                its(:value) {should eq '/usr/lib64/php/modules'}
              end
              context php_config('enable_dl') do
                its(:value) {should eq 1}
              end
            end

        * php_config

                context php_config('date.timezone') do
                  its(:value) {should eq 'Asia/Tokyo'}
                end

            php.iniの設定項目をphp_config('date.timezone')に書く、設定項目の値を its(:value) {should eq 'Asia/Tokyo'} に書く、設定が正しいかをテスト

    * selinux resource

        [selinux](http://serverspec.org/resource_types.html#selinux)

            describe selinux do
              it {should be_disabled}  
            end

        * be_disabled

            selinuxがdisabledになっているかをテストする、ほかにもbe_enforcing, be_permissiveがある

    * user resource

        [user](http://serverspec.org/resource_types.html#user)

            describe user('deploy') do
              it {should exist}
              it {should belong_to_group 'deploy'}
              it {should have_home_directory '/home/deploy'}
              it {should have_login_shell '/bin/bash'}
            end

        * exist

            user('deploy') が存在するかをテスト

        * belong_to_group

            user('deploy') がbelong_to_group 'deploy'に属しているかをテスト

        * have_home_directory

            user('deploy') のhome directoryが should have_home_directory '/home/deploy' かどうかをテスト

        * have_login_shell

            user('deploy') のshellが should have_login_shell '/bin/bash' かどうかをテスト

今回はじめてインフラのテストをしましたが、serverspecはとても使いやすかったです、chefに依存してないところが気に入りました
開発環境のvagrantにも、本番のsakura VPSにも同じようにテストを流す事ができました

次回はdockerなんかを使ってCIをまわす環境を構築してみようかと思います

でわでわ、失礼します