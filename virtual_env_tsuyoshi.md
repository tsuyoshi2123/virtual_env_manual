# 目次

 - 仮想環境  
 - 環境  
 -  vagrant 
    - vagrant virtualbox インストール  
    - vagrant box ダウンロード  
    - Vagrant 作業用ディレクトリ作成  
    - Vagrantfile 編集  
    - vagrant プラグインインストール  
    - ゲストOSをログインする  
    - ログイン時のOS識別(Mac)  
 - PHP
   - PHPインストール(7.3 ver)  
   - composerのインストール  
 - laravel
   - laravel インストール(var6.0)
 - DB 
   - データベースインストール
   - データベース構築  
   - データベースの作成  
   - データベース内のLaravelを動かす  
 - Nginx
   - Nginx を使用してWEBサーバーを起動  
   - Nginx インストール  
   - Nginx と php-fpm 設定値変更  
   - Nginx再起動  
   - 正しい表示がされなかった時  

<br>

# 仮想環境  
 ### 仮想環境とは  
  仮想的サーバ、ストレージを作ることによって物理的環境に左右されることなく使用することができる。  
  仮想環境を構築することによって linux 用のパソコン、 windows のパソコンなどそれぞれ用意する必要がなくなるので  
  好きな環境を構築し一つのハードウェアで一元管理することができる。  
  仮に仮想環境が故障した時にバックアップをとることによってバックアップファイルを別のハードウェアを移行することによって容易に運用することができる。

<br>

### vagrantとは  
  仮想マシン(virtualbox,docker)の簡単な構築をし同じような環境を再現することができる。  
  そのほかにコマンド一つで仮想マシンを起動削除したりなど容易に管理する。  
  これから利用する**virtualbox**では**ホストOSとゲストOS間を同時並行**して仮想的なOSを一つを追加して  
  OS間の移動をすることができる。  
  共有フォルダを設けることによって**ホストOSとゲストOS間のファイルやディレクトリを共有**することできる  
  などの操作をすることができる。詳しくは上記にリンクが貼っているので参照しましょう。  

<br>

実際にvagrantを利用して仮想環境構築していきましょう。  


# 環境
<br>

|   環境   |  バージョン|
|:-------:|:---------:|
| PHP     |   7.3     |
|Nginx    |  1.19     |
|MySQL    |  5.7      |
|Laravel  |  6.0      |
|OS       |  Cent7    |

<br>

# vagrant
## vagrant virtualbox インストール
### virtualbox ダウンロード

下記のサイトからそれぞれのdmgファイルをダウンロード後、インストールを進めてください。  
[Virtual Box公式](https://www.virtualbox.org/wiki/Download_Old_Builds_6_0)  

##### Vagrantの最新バージョンがVirtualBoxの最新バージョンに対応していないため、Virtual Boxはver6.0.14をインストールするようにしてください。

<br>

#### virtualbox インストール (Macの場合)
**`OS X hosts`** を選択しましょう。

##### 以下のコマンドを実行してVirtualBoxのウィンドウが表示されれば正常にインストールされています。  

```
$ virtualbox
```

コマンド実行後は入力を受け付けない状態となるため、 **`Control + c`** を押してください。

#### Vagrant インストール (Macの場合)
下記コマンドで簡単にインストールすることが可能です。

```
$ brew cask install vagrant
```

**バージョン確認する方法**

```
$ vagrant -v
```

##### バージョンが確認できたらインストール完了です  

<br>

## vagrant box ダウンロード  
<br>

仮想環境(ゲストOS)をで開発するにあったって、ゲストOSに必要なOSを追加しなくてはなりません  
今回使用するのはlinuxのCentOSのバージョン7のBox名 **`centos/7`** を指定して実際にダウンロードしてみましょう!  

##### コマンドを実行するディレクトリはどこでも構いません

```
vagrant box add centos/7
```

##### コマンドを実行すると、下記のような選択肢が表示されます

```
1) hyperv
2) libvirt
3) virtualbox
4) vmware_desktop
Enter your choice: 3
```

今回使用するソフトはVirtualBoxのため、**3**を選択して**enter**を押しましょう。

<br>

##### 下記のように表示されたら完了です。

```
Successfully added box 'centos/7' (v1902.01) for 'virtualbox'!
```

##### 念のためboxが追加されたか確認する

```
vagrant box list
centos/7 (virtualbox, 1902.01)
```

<br>

## vagrant 作業用ディレクトリ作成  

<br>

##### いずれかのディレクトリ下に任意の名前で作成  
- **自分の作業用ディレクトリ**
- **デスクトップ**

```
$ # mkdir ディレクトリ名 ここではディレクトリ名をvagrant_testとしましょう
$ mkdir vagrant_test
```

##### 作成したディレクトリに移動

```
$ cd vagrant_test
```

##### 作成したらフォルダの中で以下のコマンドを実行してください

```
# 作成したディレクトリに移動
cd vagrant_test
# vagrant init box名 先ほどダウンロードしたboxを使用することになります
vagrant init centos/7
実行後問題なければ以下のような文言が表示されます
A `Vagrantfile` has been placed in this directory. You are now
ready to `vagrant up` your first virtual environment! Please read
the comments in the Vagrantfile as well as documentation on
`vagrantup.com` for more information on using Vagrant.
```

`vagrant init centos/7`を実行したことにより作成したディレクトリ内に
`Vagrantfile` という設定ファイルが作成されています
実際に編集してみましょう

<br>

## Vagrantfile 編集
[Vagrantfile](https://qiita.com/pugiemonn/items/bcd95a35c3ec7624cd61)

##### vagrantfaile編集前に...

`vi vagrantfile` `vi 指定ファイルまでの相対パス` と実行するとvimコマンドが実行されターミナル上で指定したファイルの
編集を行うことができます.
編集してみましょう!

##### vagrant 編集 Vagrantfile ファイル  

下記の編集箇所のコメントを外し変更を加えてください  

```
変更点1   IPアドレスプライベートネットワーク設定
  # config.vm.network "private_network", ip: "192.168.33.10"
                            ↓
  # config.vm.network "private_network", ip: "192.168.33.19"
変更点2   
  # config.vm.synced_folder "../data", "/vagrant_data"
                            ↓
  # config.vm.synced_folder "./", "/vagrant", type:"virtualbox"  
```


- **変更点1**
  ・ネットワークに必要なIPアドレスの設定している  
  ・[その他vagrantネット設定説明](https://qiita.com/centipede/items/64e8f7360d2086f4764f)  

-  **変更点2**
  ・カレントディレクトリと指定のディレクトリを指している。  
  ・`./`はホストOSの `vagrant_test` を指しており `/vagrant` はゲストOSを指しており virtualbox で紐付けることでリアルタイムで同期しファイルの共有することが可能になる。  
・type 指定で環境構築に必要なボックス仮想マシンを指定している

<br>

## vagrant プラグインインストール  

[guest additions 参考リンク](https://weblabo.oscasierra.net/vagrant-vbguest-plugin-1/)  
[vagrant-vbguest 参考リンク](https://pentan.info/server/vagrant/vagrant_vbguest_note.html)  

vagrant はプラグイン(拡張機能)が用意されています  
今回は vagrant-vbguest と sahara をインストールします  
vagrant-vbguest は初めに追加した Box の中にインストールされている Guest  
Additions というもののバージョンを、  
VirtualBox のバージョンに合わせて最新化してくれるプラグインです。  

<br>

##### Geust Additions 利点
-  クリップボードの共有
-  フォルダの共有
-  自動ログイン
-  ホストマシンとの時刻同期 

##### sahara利点
-  環境構築中のゲストOSの状態の保存・巻き戻しができる
-  [sahara 操作方法はこちら](https://qiita.com/kidach1/items/ba365905b2a770c72be1)  

<br>

##### vagrant-vbguestインストール

```
$ vagrant plugin install vagrant-vbguest
```

##### saharaインストール

```
$ git clone https://github.com/ryuzee/sahara.git  //saharaパッケージクローン
$ cd sahara                                       //ディレクトリ移動
$ bundle install                                  // インストール
$ vagrant plugin install sahara                   //プラグインインストール
$ vagrant plugin list                             //バージョン確認
```

<br>

##### saharaバージョン確認

```
$ vagrant plugin list
sahara (0.0.xx)   ← インストールバージョンが異なります
```

##### プラグインバージョン確認  
プラグインがインストールされたか確認しましょう！

```
$ vagrant plugin list
```

<br>

##### vagrantを使用してゲストOSを起動  
以上で仮想環境を構築する準備はできました  
Vagrantfileのあるディレクトリにて以下のコマンドを実行しましょう！

```
$ vagrant up  
```

起動が完了したら次に進みましょう!


## ゲストOSをログインする  

起動が正常に終了すれば、皆さんが使用しているPCのOSであるホストOSの上に全く別のゲストOSが立ち上がったことになります。  
では実際にゲストOSにログインしていきましょう。 SSH はリモートマシンにログインするコマンドです。  

##### ssh  
sshはリモートマシンにログインするコマンドです。  
今回は、 **VirtualBox** が提供するネットワークを通じてターミナル上でホストOSからゲストOS(仮想マシン)にログインします。

<br>

##### macでログイン  
作成した **vagrant_test** ディレクトリに移動して下記のコマンドを実行しましょう。

```
$ vagrant ssh
```

##### ログイン後表記(ゲストログイン中)

```
Welcome to your Vagrant-built virtual machine.
[vagrant@localhost ~]$
```

<br>

##### vagrant ssh-configというコマンドを実行した結果出力される下記の情報を元に、SSH(サーバ)での接続を行っています  
**`vagrant ssh-config`** というコマンドを実行した結果出力される下記の情報を元に、SSHでの接続を行っています。  
ゲストOS内にログインしている人は一度exit コマンドを使用してログアウトして、自分の環境で試しにコマンドを実行してみましょう。

```
$ vagrant ssh-config
Host default
  HostName 127.0.0.1
  User vagrant
  Port 2222
  〜省略〜
  IdentityFile /Users/xxxx/.vagrant/machines/default/virtualbox/private_key
  IdentitiesOnly yes
  LogLevel FATAL
```

```
Tips
127.0.0.1  //Host名
vagrant    //ユーザー名
-i         //ログインに使用する鍵の場所を指定します
-p         //ゲストOSにログインする際に使用するホストOSのポート番号を指定します
```

<br>

##### ※注意  
この先では様々なコマンドや操作を実行していきますが、今自分がホストOSとゲストOS、どちらのOSをコマンドラインで操作しているのか適切に判断できないと上手く作業手順書通り進められないだけでなく、何か取り返しのつかない操作をしてしまう可能性があります。  

プロンプトの部分を見て自分が今どちらのOSを操作しているのか、常に注意して把握するようにしてください。※ターミナルやコマンドプロンプトをカスタマイズしている方は下記の通りにはなりません。  

## ログイン時のOS識別(Mac)

[sudo参考リンク](https://www.sejuku.net/blog/54857)  

ゲストOSかホストOSかわからなくなった時の区別として以下を参考にしてみてください   

##### ホストOS

```
ユーザー名noMacbook:~ ユーザー名$
```

##### ゲストOS

```
[vagrant@localhost ~]$
```

<br>

##### パッケージをインストール  
では早速グループパッケージをインストールしていきましょう。下記のコマンドを実行してください。

```
$ sudo yum -y groupinstall "development tools"
```

このコマンドを実行することによりgitなどの開発に必要なパッケージを一括でインストールできます。  

<br>

##### sudo  

**ユーザを切り替えず**にスーパーユーザー(全ての権限を持っているユーザーのこと)権限でコマンドを実行することができる続行するにあったって以下の操作をすることができる  
- コマンドを実行することができる
- シェルを実行できる
- ファイルの編集、実行することができる  

しかし禁止されているコマンドなどある為注意して実行するようにしましょう

<br>

##### yum
ユーザーが手軽に Linux を利用しやすいように OS 動作に必要なパッケージを一括でインストールすることができる
インストール時に yes/no と聞かれる時に全て yes で実行してくれる為、大量のパッケージを実行する際に  
毎回yesと入力する手間が省けるコマンドと覚えましょう！

<br>

# PHP
## PHPインストール(7.3 ver)  

[PHP7.3インストール](https://www.suzu6.net/posts/152-centos7-php-73/)
[phpバージョン アップデート 変更 消去](https://qiita.com/heimaru1231/items/84d0beca81ca5fdcffd0)  

##### REMIリポジトリ追加

```
$ sudo yum -y install epel-release wget
$ sudo wget http://rpms.famillecollet.com/enterprise/remi-release-7.rpm
$ sudo rpm -Uvh remi-release-7.rpm
```

##### PHP7.3インストール

```
$ sudo yum -y install --enablerepo=epel,remi,remi-php73 php php-devel php-mbstring php-pdo  php-gd php-xml
$ php-mcrypt
$ php -v      //バージョン確認後7.3と表示されればOK
```

yum コマンドを使用してPHPをインストールした場合、古いバージョンのPHPがインストールされてしまいます。  
Laravel を動作させるにはPHPのバージョン7以上をインストールする必要があるため  
yum ではなく外部パッケージツールをダウンロードして、そこからPHPをインストールしていきます。

<br>

##### 拡張モジュール  
下記のコマンド参照  
`php php-devel php-mbstring php-pdo php-gd php-xml php-mcrypt`の部分は  
PHPのインストールと同時に、PHPアプリケーションを動かす上で必要となるモジュール(拡張機能)をインストールしています  
例えば `php-pdo` はPDO接続行う為に、`php-mbstring`はPHPで日本語名護のマルチバイト文字を扱う為に必要になります  
アプリケーションの開発に必要な外部モジュール箱のように追加していきます。

##### 補足
下記コマンドを入力することでインストールしたモジュールを確認することができる

```
$ rpm -qa | grep php
```

## composerのインストール


##### composer はパッケージ事の依存関係を管理・解決するツール  
##### 次に PHP のパッケージ管理ツールである composer をインストールしていきます。  

```
$ php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
$ php composer-setup.php
$ php -r "unlink('composer-setup.php');"
```

##### どのディレクトリにいても composer コマンドを使用できるよう file の移動を行います  

```
$ sudo mv composer.phar /usr/local/bin/composer
$ composer -v
```

# laravel
## laravel インストール(var6.0)
[auth インストール](https://qiita.com/daisu_yamazaki/items/a914a16ca1640334d7a5)  

##### バージョン指定する  
vagrantfileのディレクトリにて `vagrant ssh` 実行時の場所で以下のコマンドを実行してください  

```
$ composer create-project laravel/laravel --prefer-dist ディレクトリ名 6.0
$ php artisan -v
```

composer に依存したバージョンになる為laravelバージョン**6.x**になればOKです！  

<br>

##### 認証機能導入  
次にインストールしたlaravelに認証(ログイン)機能を実装していきます。  
バージョンによっては **`php artisan ui vue --auth`** が利用できない場合がある為きをつけましょう!

<br>

##### ログイン機能を実行(laravel 6.0の場合)

```
$ composer require laravel/ui:^1.0 --dev
$ php artisan ui vue --auth
```

**`http://192.168.33.19/`**  実行してみましょう


php バージョンの相違があるとエラーが起きるので下記を参考にし再度インストールしし直してください!!  
[phpバージョン アップデート 変更 消去](https://qiita.com/heimaru1231/items/84d0beca81ca5fdcffd0)

# DB
## データベースインストール  

<br>

今回インストールするデータベースはMySQLとなります。versionは5.7を使用します.  
centos7 は、デフォルトで variaDB というデータベースがインストールされていますが、 MariaDB は MYSQL と互換性があるDBなのできにせず、MySQLのインストールを進めていきます。  
rpm に新たにリポジトリを追加し、インストールを行います。  
<br>

##### rpm
rpm とは RedHat という会社が作成した Linux ディストリビューション(CentOSやFedora)内で使用できるパッケージ管理ツールです。  
yum と異なる点としては、yum がパッケージごとの **依存関係** を管理してくれることに対して、  
rpmはパッケージ自体を管理することはできますがその**管理対象のパッケージの依存関係**までは管理することはできません。
<br>

##### データベースインストール
```
$ sudo wget http://dev.mysql.com/get/mysql57-community-release-el7-7.noarch.rpm
$ sudo rpm -Uvh mysql57-community-release-el7-7.noarch.rpm
$ sudo yum install -y mysql-community-server
$ mysql --version
```

version の確認ができましたら**インストール完了**です。  
次に**MySQLを起動**し接続を行います。

```
$ sudo systemctl start mysqld  
$ mysql -u root -p  
$ Enter password:
```

<br>

MacあるいはWindowsにMySQLをインストールしたときは、何も入力せずに接続が可能だったかと思いますが今回はデフォルトで  
root にパスワードが設定されてしまっています。  
まずは password を調べ、接続し passsword の再設定を行っていく必要があります。  

**※** 今回は、比較的簡単な方法でパスワードの再設定を行いますが、セキュリティ的によろしくはないため本番環境と呼ばれる環境でこの方法で再設定するのは避けてください。

```
$ sudo cat /var/log/mysqld.log | grep 'temporary password'  # このコマンドを実行したら下記のように表示されたらOKです
2017-01-01T00:00:00.000000Z 1 [Note] A temporary password is generated for root@localhost: hogehoge
```

`hogehoge` と記載されている箇所に存在するランダムな文字列がパスワードとなります。  
では先程出力したランダム文字列をコピー後、再度以下のコマンドを実行し、パスワード入力時にペーストしてください。

```
$ mysql -u root -p
$ Enter password:
mysql > 
```

問題なく接続できたでしょうか？次に接続した状態で**passwordの変更**を行います。

```
mysql > set password = "新たなpassword";
```

新たな password には、必ず大文字小文字の英数字 + 記号かつ8文字以上の設定をする必要があります。  
MySQL5.7 のパスワードポリシーは厳格で開発段階では非常に面倒のため、以下の設定を行いシンプルなパスワードに初期設定できるように MySQL の設定ファイルを変更します。(ステージング環境や本番環境で使用するDBにはポリシーを遵守したパスワードを設定してください。)

```
$ sudo vi /etc/my.cnf
```

```
# 省略
[mysqld]
# 省略
# read_rnd_buffer_size = 2M
datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock
# 下記の一行を追加
validate-password=OFF
```

##### 編集後はMySQLサーバの再起動が必要です

```
$ sudo systemctl restart mysqld  //MySQLサーバ再起動
```

再度MySQLにログインしてパスワードの初期設定を行えば、簡単なパスワードで登録ができます。  
以上で、MySQLの導入と設定が完了となります。

## データベースの作成  
Laravelのアプリケーションを動かす上で使用するデータベースの作成を行います。  

```
mysql > create database laravel_app;
```

**Query OK**と表示されたら作成は完了となります。  
## データベース内のLaravelを動かす  
##### laravel_appディレクトリ下の `.env` ファイルの内容を以下に変更してください。  

```
DB_PASSWORD=
# ↓ 以下に編集
DB_PASSWORD=登録したパスワード
```

では、laravel_appディレクトリに移動して `php artisan migrate` を実行します。  
マイグレーションが問題なく実行できた後、次にNginx を使用してwebサーバ起動します。  

<br>

# Nginx  
## Nginx インストール  
##### Nginx の最新版をインストールしていきます。 vi エディタを使用して以下のファイルを作成します。  

<br>

まだゲストOSにログインしていない方は  
vagrant用に作成したディレクトリにて vagrant ssh コマンドを実行してログインしてください。  

```
$ sudo vi /etc/yum.repos.d/nginx.repo
書き込む内容は以下になります。
```

```
[nginx]
name=nginx repo
baseurl=http://nginx.org/packages/mainline/centos/\$releasever/\$basearch/
gpgcheck=0
enabled=1
```

##### 書き終えたら保存して、以下のコマンドを実行しNginxのインストールを実行します。

```
$ sudo yum install -y nginx
$ nginx -v
```

##### Nginxのバージョンは確認できたでしょうか？ではNginxの起動をしましょう。

```
$ sudo systemctl start nginx
```

ブラウザにて http://192.168.33.19 (Vagrantfileでipを書き換えた方はそのipアドレス)と入力し、NginxのWelcomeページが表示されましたでしょうか？
表示されたら問題なく動いていますので次に進みましょう。

## Nginx と php-fpm 設定値変更  
では、早速 Nginx の設定ファイルを編集していきます。  
うまく動かない時は **`php-fpm`** がインストールされていない可能性がある為念のため確認してみましょう  
なかった場合 php インストールの所に再インストールコマンドや資料がある為参考にして実行してみましょう  

##### パッケージ確認方法

```
rpm -qa | grep php
```

<br>

##### 使用しているOSがCentOSの場合、`/etc/nginx/conf.d` ディレクトリ下の default.conf ファイルが設定ファイルとなります。
```
$ sudo vi /etc/nginx/conf.d/default.conf
```

<br>

##### 編集範囲がやや広いですが頑張りましょう。コメント外しのミスが多いのでしっかりと確認してください。  

```
server {
  listen       80;
  server_name  192.168.33.19; # Vagranfileでコメントを外した箇所のipアドレスを記述してください。
  # ApacheのDocumentRootにあたります
  root /vagrant/laravel_app/public; # 追記
  index  index.html index.htm index.php; # 追記
  #charset koi8-r;
  #access_log  /var/log/nginx/host.access.log  main;
  location / {
      #root   /usr/share/nginx/html; # コメントアウト
      #index  index.html index.htm;  # コメントアウト
      try_files $uri $uri/ /index.php$is_args$args;  # 追記
  }
  # 省略
  # 該当箇所のコメントを解除し、必要な箇所には変更を加える
  # 下記は root を除いたlocation { } までのコメントが解除されていることを確認してください。
  location ~ \.php$ {
  #    root           html;
      fastcgi_pass   127.0.0.1:9000;
      fastcgi_index  index.php;
      fastcgi_param  SCRIPT_FILENAME  /$document_root/$fastcgi_script_name;  # $fastcgi_script_name以前を /$document_root/に変更
      include        fastcgi_params;
  }
  # 省略
```

<br>

##### Nginxの設定ファイルの変更は、以上です。次に **`php-fpm`** の設定ファイルを編集していきます。

```
$ sudo vi /etc/php-fpm.d/www.conf
```

##### 変更箇所は以下になります。

```
;24行目近辺
user = apache
# ↓ 以下に編集
user = nginx
group = apache
# ↓ 以下に編集
group = nginx
```

設定ファイルの変更に関しては、以上となります。  
では早速起動しましょう(Nginxは再起動になります)。

<br>

## Nginx再起動  

<br>

```
$ sudo systemctl restart nginx   //Nginx 再起動
$ sudo systemctl start php-fpm   //php-fpm 再起動
```

再度ブラウザにて、 `http://192.168.33.19` を入力して確認してください。  
laravel アプリケーションを動かすことはできたでしょうか？？  

<br>

## 正しい表示がされなかった時  

<br>

##### もし出なければ以下のようなLaravelのエラーが表示されると思います。  

```
The stream or file "/vagrant/laravel_app/storage/logs/laravel.log" could not be opened:failed to open stream: Permission denied
```

これは 先程 php-fpm の設定ファイルの user と group を `nginx` に変更したと思いますが、ファイルとディレクトリの実行 user と group に `nginx` が許可されていないため起きているエラーです。

##### 試しに以下のコマンドを実行してみてください。

```
$ ls -la ./ | grep storage && ls -la storage/ | grep logs && ls -la storage/logs/ | grep laravel.log
```

<br>

出力結果から、storageディレクトリも logsディレクトリも laravel.logファイルも全て `user` と `group` が  
`vagrant` となっていますので、これでは `nginx` という**ユーザーの権限**をもってlaravel.logファイルへの書き込みができません。  

では、以下のコマンドを実行して `nginx` というユーザーでもログファイルへの書き込みができる**権限を付与**してあげましょう。  

```
$ cd /vagrant/laravel_app
$ sudo chmod -R 777 storage
```

##### 書き込み権限を得たのか確認  
laravel.logにはLaravelアプリケーション実行中にエラーが生じた場合に画面に表示されるエラー内容(皆さんも良く見たであろうWhoops)と全く同じ内容が書き込みされています。  

そのため、意図的にアプリケーション実行エラーを起こして laravel.log に画面と同じエラーが表示されているか見てみましょう。

```
$ cd /vagrant/laravel_app
$ vi routes/web.php
```

では以下のように変更しましょう。

```
#Route::get('/', function () {
#    return view('welcome');
#});
↓ # ; を消します
Route::get('/', function () {
    return view('welcome')
});
```

編集が完了しましたら次は以下のコマンドを実行します。  

```
$ tail -f storage/logs/laravel.log
```

では、再度 **`http://192.168.33.19`** のURLにアクセスしてみます。  

```
syntax error, unexpected '}', expecting ';'
```

といった内容のエラーが画面上に表示され、同じ内容のエラー文言がlaravel.logに書き込みされたことも確認できたでしょうか？？  

確認が完了しましたら、**`Ctrl + c`** でtailの実行モードを終了しましょう。  

では変更した **`routes/web.php`** の内容を元に戻して再度 http://192.168.33.19 にアクセスして正常に Laravel の  Welcome 画面の表示をしてください。

welcome画面がでできたら右上に **`register`** とクリックできる場所があるのでそこでユーザ追加して再度ログインできたら完成です！  

お疲れ様でした!  


<br>

##### 環境構築と所感  
自身で構築してみて重いとゆうのが第一印象でした。  
エラー文に直面した時にディレクトリを作成していない、必要パッケージインストールをしていないなどのことがあった為エラー文を解決することによってのググり力とゆうのがついたきがします。  
英語が弱く、このカリキュラムを通して翻訳使わずとも読解できるように少しでも意識を付けていくようになったきがします。  
この教材では仮想環境開発を構築のみしかしていないのでそこに自身でアプリ導入する際や現場などでそういった場面があった時に経験値積んでその開発に適した環境構築をできるようになり、柔軟に対応しなければと思いました。  

<br>

##### 参考サイト 
[仮想環境とは](https://bcblog.sios.jp/what-is-virtualenvironment-vmware/#i)  
[virtualbox1](https://www.sejuku.net/blog/72833)  
[virtualbox2](https://eng-entrance.com/vm-list#VirtualBox)  
[Virtual Box公式](https://www.virtualbox.org/wiki/Download_Old_Builds_6_0)  
[vagrantfile1](https://qiita.com/pugiemonn/items/bcd95a35c3ec7624cd61)  
[その他vagrantネット設定説明](https://qiita.com/centipede/items/64e8f7360d2086f4764f)  
[guest additions 参考リンク](https://weblabo.oscasierra.net/vagrant-vbguest-plugin-1/)  
[vagrant-vbguest 参考リンク](https://pentan.info/server/vagrant/vagrant_vbguest_note.html)  
[sahara 操作方法はこちら](https://qiita.com/kidach1/items/ba365905b2a770c72be1)  
[sudo参考リンク](https://www.sejuku.net/blog/54857)  
[PHP7.3インストール](https://www.suzu6.net/posts/152-centos7-php-73/)  
[phpバージョン アップデート 変更 消去](https://qiita.com/heimaru1231/items/84d0beca81ca5fdcffd0)  
[auth インストール](https://qiita.com/daisu_yamazaki/items/a914a16ca1640334d7a5)  
[phpバージョン アップデート 変更 消去](https://qiita.com/heimaru1231/items/84d0beca81ca5fdcffd0)  
[パーミッション 操作権限の変更](https://note.mokuzine.net/ssh-permission-denied/)  