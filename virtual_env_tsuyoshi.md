# vagrant 仮想環境構築
## vagrantBox vagrant インストール
### vagrantBoxインストール
下記のサイトからそれぞれのdmgファイルをダウンロード後、インストールを進めてください。

[Virtual Box公式](https://www.virtualbox.org/wiki/Download_Old_Builds_6_0)

※ **Vagrantの最新バージョンがVirtualBoxの最新バージョンに対応していないため、Virtual Boxはver6.0.14をインストールするようにしてください。**

### Macの場合
`OS X hosts` を選択しましょう。

以下のコマンドを実行してVirtualBoxのウィンドウが表示されれば正常にインストールされています。

```
$ virtualbox
```

コマンド実行後は入力を受け付けない状態となるため、 Control + c を押してください。

Vagrant インストール

<br>

## Vagrant 作業用ディレクトリ作成

**[いずれかのディレクトリ下に任意の名前で作成]**
- 自分の作業用ディレクトリ
- デスクトップ
```
mkdir フォルダ名
```

**[作成したディレクトリに移動]**
```
cd ディレクトリ名
```
<br>

## Vagrantfile 編集
**【移動したファイルに対しvagrant設定ファイルの投入】**

vagrant init box  
vagrant init centos/7  //先ほどダウンロードし指定したvagrant boxを投入する

 **実行後問題なければ以下のような文言が表示されます**

```

A `Vagrantfile` has been placed in this directory. You are now
ready to `vagrant up` your first virtual environment! Please read
the comments in the Vagrantfile as well as documentation on
`vagrantup.com` for more information on using Vagrant.
```

**【vagrant 編集】  Vagrantfile ファイル**

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
     
- 変更点1  
  ・ネットワークに必要なIPアドレスの設定している  
  ・[その他vagrantネット設定説明](https://qiita.com/centipede/items/64e8f7360d2086f4764f)

- 変更点2  
・カレントディレクトリと指定のディレクトリを指している。  
・紐付けることでリアルタイムで同期することができる  
・type 指定で環境構築に必要なボックス(OS)を設定している

<br>

## Vagrant プラグインインストール  

[guest additions 参考リンク](https://weblabo.oscasierra.net/vagrant-vbguest-plugin-1/)  
[vagrant-vbguest 参考リンク](https://pentan.info/server/vagrant/vagrant_vbguest_note.html)

vagrant はプラグイン(拡張機能)が用意されています  
今回はvagrant-vbguestとsaharaをインストール します  
vagrant-vbguestは初めに追加したBoxの中にインストールされているGuest  
Additionsというもののバージョンを、
VirtualBoxのバージョンに合わせて最新化してくれるプラグインです。

**Geust Additions 利点**
-  クリップボードの共有
-  フォルダの共有
-  自動ログイン
-  ホストマシンとの時刻同期 

**sahara利点**
-  環境構築中のゲストOSの状態の保存・巻き戻しができる
-  [sahara 操作方法はこちら](https://qiita.com/kidach1/items/ba365905b2a770c72be1)


**vagrant-vbguestインストール**

```
$ vagrant plugin install vagrant-vbguest
```

**saharaインストール**
```
$ git clone https://github.com/ryuzee/sahara.git  //saharaパッケージクローン
$ cd sahara                                       //ディレクトリ移動
$ bundle install                                  // インストール
$ vagrant plugin install sahara                   //プラグインインストール
$ vagrant plugin list                             //バージョン確認
```

**saharaバージョン確認**
```
$ vagrant plugin list
sahara (0.0.xx)   ← インストールバージョンが異なります
```

**プラグインバージョン確認**  
プラグインがインストールされたか確認しましょう！
```
$ vagrant plugin list
```

プラグインインストール確認ができたらVagrantを使用してゲストOSの起動をします。  
以上で仮想環境を構築する準備は整いました。  
Vagrantfileがあるディレクトリにて以下のコマンドを実行して、早速起動してみましょう。

**vagrantを使用してゲストOSを起動**  
以上で仮想環境を構築する準備はできました
Vagrantfileのあるディレクトリにて以下のコマンドを実行しましょう！

```
$vagrant up  
```

初回起動に少し時間がかかるのでボケーっとしましょう！

## ゲストOSをログインする  

起動が正常に終了すれば、皆さんが使用しているPCのOSであるホストOSの上に全く別のゲストOSが立ち上がったことになります。  
では実際にゲストOSにログインしていきましょう。SSHはリモートマシンにログインするコマンドです。

**ssh**  
sshはリモートマシンにログインするコマンドです  
今回は、VirtualBoxが提供するネットワークを通じてターミナル上でホストOSからゲストOS(リモートマシン)にログインします。

**macでログイン**  
作成した vagrant_test ディレクトリに移動して下記のコマンドを実行しましょう。

```
$ vagrant ssh
```

**ログイン後表記(ゲストログイン中)**

```
Welcome to your Vagrant-built virtual machine.
[vagrant@localhost ~]$
```

<br>

**vagrant ssh-configというコマンドを実行した結果出力される下記の情報を元に、SSHでの接続を行っています**  
vagrant ssh-config というコマンドを実行した結果出力される下記の情報を元に、SSHでの接続を行っています。
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

※注意
この先では様々なコマンドや操作を実行していきますが、今自分がホストOSとゲストOS、どちらのOSをコマンドラインで操作しているのか適切に判断できないと上手くセクションの内容が進められないだけでなく、何か取り返しのつかない操作をしてしまう可能性があります。

プロンプトの部分を見て自分が今どちらのOSを操作しているのか、常に注意して把握するようにしてください。※ターミナルやコマンドプロンプトをカスタマイズしている方は下記の通りにはなりません。

## Mac
**ホストOS**

```
ユーザー名noMacbook:~ ユーザー名$
```

**ゲストOS**

```
[vagrant@localhost ~]$
```

**パッケージをインストール**  
では早速グループパッケージをインストールしていきましょう。下記のコマンドを実行してください。

```
sudo yum -y groupinstall "development tools"
```

このコマンドを実行することによりgitなどの開発に必要なパッケージを一括でインストールできます。

<br>

## PHPインストール(7.3 ver)

https://www.suzu6.net/posts/152-centos7-php-73/

**REMIリポジトリ追加**

```
sudo yum -y install epel-release wget
sudo wget http://rpms.famillecollet.com/enterprise/remi-release-7.rpm
sudo rpm -Uvh remi-release-7.rpm
```

**PHP7.3インストール**

```
sudo yum -y install --enablerepo=epel,remi,remi-php73 php php-devel php-mbstring php-pdo  php-gd php-xml
php-mcrypt
php -v      //バージョン確認後7.3と表示されればOK
```

yumコマンドを使用してPHPをインストールした場合、古いバージョンのPHPがインストールされてしまいます。  
Laravelを動作させるにはPHPのバージョン7以上をインストールする必要があるため  
yumではなく外部パッケージツールをダウンロードして、そこからPHPをインストールしていきます。

<br>

**拡張モジュール**  
下記のコマンド参照  
`php php-devel php-mbstring php-pdo php-gd php-xml php-mcrypt`の部分は  
PHPのインストールと同時に、PHPアプリケーションを動かす上で必要となるモジュール(拡張機能)をインストールしています  
例えば `php-pdo` はPDO接続行う為に、`php-mbstring`はPHPで日本語名護のマルチバイト文字を扱う為に必要になります  
アプリケーションの開発に必要な外部モジュール箱のように追加していきます。

[phpバージョン アップデート 変更 消去](https://qiita.com/heimaru1231/items/84d0beca81ca5fdcffd0)  
[auth インストール](https://qiita.com/daisu_yamazaki/items/a914a16ca1640334d7a5)


## composerのインストール


**composer はパッケージ事の依存関係を管理・解決するツール**
**次にPHPのパッケージ管理ツールであるcomposerをインストールしていきます。**

```
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
php composer-setup.php
php -r "unlink('composer-setup.php');"
```

**どのディレクトリにいてもcomposerコマンドを使用を使用できるようfileの移動を行います**

```
sudo mv composer.phar /usr/local/bin/composer
composer -v
```

## laravel インストール(var6.0)
**バージョン指定する**
vagrantfileのディレクトリにてvagrant ssh 実行時の場所で以下のコマンドを実行してください

```
composer create-project laravel/laravel --prefer-dist ディレクトリ名 6.0
php artisan -v
```
バージョンを指定してもcomposerに依存したバージョンになるため気にしなくて大丈夫です

**認証機能導入**
ログイン機能を実行

```
laravel 6.0の場合
composer require laravel/ui:^1.0 --dev
php artisan ui vue --auth
```

`http://192.168.33.19/`  実行してみましょう


phpバージョンの相違があるとエラーが起きるので下記参照
[auth インストール](https://qiita.com/daisu_yamazaki/items/a914a16ca1640334d7a5)


## データベースインストール
今回インストールするデータベースはMySQLとなります。versionは5.7を使用します.  
centos7は、デフォルトでvariaDBというでーばベースがインストールされていますが、MariaDBはMYSQLと互換性があるDBなので気にせず、MySQLのインストールを進めていきます。  
rpmに新たにリポジトリを追加し、インストールを行います。

#### rpm
rpmとは RedHat という会社が作成したLinuxディストリビューション(CentOSやFedora)内で使用できるパッケージ管理ツールです。  
yumと異なる点としては、yumがパッケージごとの 依存関係 を管理してくれることに対して、  
rpmはパッケージ自体を管理することはできますがその管理対象のパッケージの依存関係までは管理することはできません。


**データベースインストール**

```
sudo wget http://dev.mysql.com/get/mysql57-community-release-el7-7.noarch.rpm
sudo rpm -Uvh mysql57-community-release-el7-7.noarch.rpm
sudo yum install -y mysql-community-server
mysql --version
```

versionの確認ができましたらインストール完了です。  
次にMySQLを起動し接続を行います。

sudo systemctl start mysqld  
mysql -u root -p  
Enter password:

MacあるいはWindowsにMySQLをインストールしたときは、何も入力せずに接続が可能だったかと思いますが今回はデフォルトでrootにパスワードが設定されてしまっています。  
まずはpasswordを調べ、接続しpassswordの再設定を行っていく必要があります。

※ 今回は、比較的簡単な方法でパスワードの再設定を行いますが、セキュリティ的によろしくはないため本番環境と呼ばれる環境でこの方法で再設定するのは避けてください。

```
sudo cat /var/log/mysqld.log | grep 'temporary password'  # このコマンドを実行したら下記のように表示されたらOKです
2017-01-01T00:00:00.000000Z 1 [Note] A temporary password is generated for root@localhost: hogehoge
```

`hogehoge` と記載されている箇所に存在するランダムな文字列がパスワードとなります。  
では先程出力したランダム文字列をコピー後、再度以下のコマンドを実行し、パスワード入力時にペーストしてください。

```
$ mysql -u root -p
$ Enter password:
mysql > 
```

問題なく接続できたでしょうか？次に接続した状態でpasswordの変更を行います。

```
mysql > set password = "新たなpassword";
```

新たなpasswordには、必ず大文字小文字の英数字 + 記号かつ8文字以上の設定をする必要があります。  
MySQL5.7のパスワードポリシーは厳格で開発段階では非常に面倒のため、以下の設定を行いシンプルなパスワードに初期設定できるようにMySQLの設定ファイルを変更します。(ステージング環境や本番環境で使用するDBにはポリシーを遵守したパスワードを設定してください。)

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

編集後はMySQLサーバの再起動が必要です

```
$ sudo systemctl restart mysqld  //MySQLサーバ再起動
```

再度MySQLにログインしてパスワードの初期設定を行えば、簡単なパスワードで登録ができます。  
以上で、MySQLの導入と設定が完了となります。

# データベース構築
## データベースの作成
実際にLaravelのTodoアプリケーションを動かす上で使用するデータベースの作成を行います。

```
mysql > create database laravel_app;
```

Query OKと表示されたら作成は完了となります。
## データベース内のLaravelを動かす
laravel_appディレクトリ下の `.env` ファイルの内容を以下に変更してください。

```
DB_PASSWORD=
# ↓ 以下に編集
DB_PASSWORD=登録したパスワード
```

では、laravel_appディレクトリに移動して `php artisan migrate` を実行します。  
マイグレーションが問題なく実行できた後、ブラウザ上でユーザー登録ができればローカルで動かしていたLaravelを
仮想環境上で全く同じように動かすことができたということになります。

<br>

# Nginx を使用してWEBサーバーを起動
まだゲストOSにログインしていない方は  
vagrant用に作成したディレクトリにてvagrant ssh コマンドを実行してログインしてください。

## Nginx インストール
**Nginxの最新版をインストールしていきます。viエディタを使用して以下のファイルを作成します。**

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

**書き終えたら保存して、以下のコマンドを実行しNginxのインストールを実行します。**

```
$ sudo yum install -y nginx
$ nginx -v
```

**Nginxのバージョンは確認できたでしょうか？ではNginxの起動をしましょう。**

```
$ sudo systemctl start nginx
```

ブラウザにて http://192.168.33.10 (Vagrantfileでipを書き換えた方はそのipアドレス)と入力し、NginxのWelcomeページが表示されましたでしょうか？
表示されたら問題なく動いていますので次に進みましょう。

## Nginx と php-fpm 設定値変更
では、早速Nginxの設定ファイルを編集していきます。
使用しているOSがCentOSの場合、`/etc/nginx/conf.d` ディレクトリ下の default.conf ファイルが設定ファイルとなります。

```
$ sudo vi /etc/nginx/conf.d/default.conf
```

**編集範囲がやや広いですが頑張りましょう。コメント外しのミスが多いのでしっかりと確認してください。**

```
server {
  listen       80;
  server_name  192.168.33.10; # Vagranfileでコメントを外した箇所のipアドレスを記述してください。
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

Nginxの設定ファイルの変更は、以上です。次に php-fpm の設定ファイルを編集していきます。

```
$ sudo vi /etc/php-fpm.d/www.conf
```

変更箇所は以下になります。

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

```
$ sudo systemctl restart nginx   //Nginx 再起動
$ sudo systemctl start php-fpm   //php-fpm 再起動
```

再度ブラウザにて、 `http://192.168.33.10` を入力して確認してください。  
Apache同様にTodoアプリケーションを動かすことはできたでしょうか？？

<br>

## 正しい表示がされなかった時

**もし出なければ以下のようなLaravelのエラーが表示されると思います。**

```
The stream or file "/vagrant/laravel_app/storage/logs/laravel.log" could not be opened:failed to open stream: Permission denied
```

これは 先程php-fpmの設定ファイルの user と group を `nginx` に変更したと思いますが、ファイルとディレクトリの実行 user と group に `nginx` が許可されていないため起きているエラーです。

**試しに以下のコマンドを実行してみてください。**

```
$ ls -la ./ | grep storage && ls -la storage/ | grep logs && ls -la storage/logs/ | grep laravel.log
```

出力結果から、storageディレクトリも logsディレクトリも laravel.logファイルも全て `user` と `group` が
`vagrant` となっていますので、これでは `nginx` というユーザーの権限をもってlaravel.logファイルへの書き込みができません。

では、以下のコマンドを実行して `nginx` というユーザーでもログファイルへの書き込みができる権限を付与してあげましょう。

```
$ cd /vagrant/laravel_app
$ sudo chmod -R 777 storage
```

**書き込み権限を得たのか確認**  
laravel.logにはLaravelアプリケーション実行中にエラーが生じた場合に画面に表示されるエラー内容(皆さんも良く見たであろうWhoops)と全く同じ内容が書き込みされています。

そのため、意図的にアプリケーション実行エラーを起こしてlaravel.logに画面と同じエラーが表示されているか見てみましょう。

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

では、再度 `http://192.168.33.10` のURLにアクセスしてみます。

```
syntax error, unexpected '}', expecting ';'
```

といった内容のエラーが画面上に表示され、同じ内容のエラー文言がlaravel.logに書き込みされたことも確認できたでしょうか？？

確認が完了しましたら、`Ctrl + c` でtailの実行モードを終了しましょう。

では変更した `routes/web.php` の内容を元に戻して再度 http://192.168.33.10 にアクセスして正常にLaravelのWelcome画面の表示をしてください。

[パーミッション 操作権限の変更](https://note.mokuzine.net/ssh-permission-denied/)

<br>

[環境構築と所感]  
自身で構築してみて重いとゆうのが第一印象でした。
エラー文に直面した時にディレクトリを作成していない、必要パッケージインストールをしていないなどのことがあった為エラー文を解決することによってのググり力とゆうのがついた気がします。
英語が弱く、このカリキュラムを通して翻訳使わずとも読解できるように少しでも意識を付けていくようになった気がします。
この教材では仮想環境開発を構築のみしかしていないのでそこに自身でアプリ導入する際や現場などでそういった場面があった時に経験値積んでその開発に適した環境構築をできるようになり、柔軟に対応しなければと思いました。



[参考サイト]  
[全体参考](https://qiita.com/7968/items/97dd634608f37892b18a)  
[Virtual Box公式](https://www.virtualbox.org/wiki/Download_Old_Builds_6_0)  
[その他vagrantネット設定説明](https://qiita.com/centipede/items/64e8f7360d2086f4764f)   
[phpバージョン アップデート 変更 消去](https://qiita.com/heimaru1231/items/84d0beca81ca5fdcffd0)  
[auth インストール](https://qiita.com/daisu_yamazaki/items/a914a16ca1640334d7a5)  
[sahara 操作方法はこちら](https://qiita.com/kidach1/items/ba365905b2a770c72be1)  
[guest additions 参考リンク](https://weblabo.oscasierra.net/vagrant-vbguest-plugin-1/)  
[vagrant-vbguest 参考リンク](https://pentan.info/server/vagrant/vagrant_vbguest_note.html)  
[パーミッション 操作権限の変更](https://note.mokuzine.net/ssh-permission-denied/)