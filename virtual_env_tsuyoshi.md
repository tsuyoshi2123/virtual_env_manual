参考資料  
全体網羅  
https://qiita.com/7968/items/97dd634608f37892b18a


#vagrant 仮想環境構築
## vagrantBox vagrant インストール
---
###vagrantBoxインストール
下記のサイトからそれぞれのdmgファイルをダウンロード後、インストールを進めてください。

[Virtual Box公式](https://www.virtualbox.org/wiki/Download_Old_Builds_6_0)

※  **Vagrantの最新バージョンがVirtualBoxの最新バージョンに対応していないため、Virtual Boxはver6.0.14をインストールするようにしてください。**

### Macの場合
`OS X hosts` を選択しましょう。

以下のコマンドを実行してVirtualBoxのウィンドウが表示されれば正常にインストールされています。

```
$ virtualbox
```

コマンド実行後は入力を受け付けない状態となるため、 Control + c を押してください。

Vagrant インストール




<br>

## Vagrant 作業用ディレクトリ作成
---
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
---
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
---
[guest additions 参考リンク](https://weblabo.oscasierra.net/vagrant-vbguest-plugin-1/)
[vagrant-vbguest 参考リンク](https://pentan.info/server/vagrant/vagrant_vbguest_note.html)

vagrant はプラグイン(拡張機能)が用意されています
今回はvagrant-vbguestとsaharaをインストールします
vagrant-vbguestは初めに追加したBoxの中にインストールされているGuest Additionsというもののバージョンを、
VirtualBoxのバージョンに合わせて最新化してくれるプラグインです。

**Geust Additions 利点**
-  クリップボードの共有
-  フォルダの共有
-  自動ログイン
-  ホストマシンとの時刻同期 

**sahara利点利点**
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
---
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
127.0.0.1  Host名
vagrant    ユーザー名
-i         ログインに使用する鍵の場所を指定します
-p         ゲストOSにログインする際に使用するホストOSのポート番号を指定します
```



<br>

※注意
この先では様々なコマンドや操作を実行していきますが、今自分がホストOSとゲストOS、どちらのOSをコマンドラインで操作しているのか適切に判断できないと上手くセクションの内容が進められないだけでなく、何か取り返しのつかない操作をしてしまう可能性があります。

プロンプトの部分を見て自分が今どちらのOSを操作しているのか、常に注意して把握するようにしてください。※ターミナルやコマンドプロンプトをカスタマイズしている方は下記の通りにはなりません。

##Mac

---
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

##PHPインストール(7.3 ver)
---
https://www.suzu6.net/posts/152-centos7-php-73/

**REMIリポジトリ追加**

```
sudo yum -y install epel-release wget
sudo wget http://rpms.famillecollet.com/enterprise/remi-release-7.rpm
sudo rpm -Uvh remi-release-7.rpm
```

**PHP7.3インストール**

```
sudo yum -y install --enablerepo=epel,remi,remi-php73 php php-devel php-mbstring php-pdo php-gd php-xml
php-mcrypt
php -v//バージョン確認後7.3と表示されればOK
```


yumコマンドを使用してPHPをインストールした場合、古いバージョンのPHPがインストールされてしまいます。
Laravelを動作させるにはPHPのバージョン7以上をインストールする必要があるため
yumではなく外部パッケージツールをダウンロードして、そこからPHPをインストールしていきます。

<br>

**拡張モジュール**
下記のコマンド参照
`php php-devel php-mbstring php-pdo php-gd php-xml php-mcrypt`の部分は
PHPのインストールと同時に、PHPアプリケーションを動かす上で必要となるモジュール(拡張機能)をインストールしています
例えば  php-pdo はPDO接続行う為に、 `php-mbstring` はPHPで日本語名護のマルチバイト文字を扱う為に必要になります
アプリケーションの開発に必要な外部モジュール箱のように追加していきます。

[phpバージョン アップデート 変更 消去](https://qiita.com/heimaru1231/items/84d0beca81ca5fdcffd0)
[auth インストール](https://qiita.com/daisu_yamazaki/items/a914a16ca1640334d7a5)


composerのインストール
---

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

##laravel インストール(6.0)
---
バージョン指定する
```

composer create-project laravel/laravel --prefer-dist ディレクトリ名 6.0
php artisan -v
```
バージョンを指定してもcomposerに依存したバージョンになるため気にしなくて大丈夫です

[auth インストール](https://qiita.com/daisu_yamazaki/items/a914a16ca1640334d7a5)



##apacheインストール , 編集,起動
---
WebサーバソフトウェアであるApacheをインストールしていきます。まだゲストOSにログインしていない方は
vagrant用に作成したディレクトリにてvagrant ssh コマンドを実行してログインしてください。

```
$ sudo yum -y install httpd     //apache インストール
$ httpd -v                      //バージョン確認
```
・パッケージ管理ツールのyumを使用してApacheのインストールをした


次はApacheの設定ファイルを編集していきます。
CentOSのyumを使用してインストールしたApacheの設定ファイルは /etc/httpd/conf ディレクトリ下にある httpd.conf というファイルです。

編集には vi コマンドを使用します。

```
$ sudo vi /etc/httpd/conf/httpd.conf
```
編集箇所は大きく分けて3箇所あります。

```
# 省略

# 変更点①
DocumentRoot "/var/www/html"
# ↓ 以下に編集
DocumentRoot "/vagrant/laravel_app/public"

# 省略

# 変更点②
<Directory "/var/www/">
  AllowOverride None
  Require all granted
</Directory>
# ↓ 以下に編集
<Directory "/vagrant/laravel_app/public">
  AllowOverride All 
  Require all granted
</Directory>

# 省略

# 変更点③
User apache
Group apache
# ↓ 以下に編集
User vagrant
Group vagrant
```


DocumentRoot

192.168.33.19(ipを変えた方は設定したip)に対してリクエストがあった場合に、サーバ内のどこのディレクトリを参照するか決める指定です。

Directory

指定したディレクトリに対して個別の設定をすることができます。設定する内容については <Directory> </Directory> の中に記述していきます。

AllowOverride

Directoryに指定したディレクトリ内の .htaccess というファイルの使用を有効化する設定です。.htaccess というファイルを配置することでディレクトリごとにWebサーバの設定を変更することができます。

Require

Directoryに指定したディレクトリへのアクセスを制御します。今回は all granted と指定されているので全てのipアドレスからのアクセスを許可しています。

User, Group

Apacheを起動するユーザーとグループを指定します。今回は vagrant というユーザーでゲストOS内にログインしているため apache を vagrant に変更してvagrantユーザーでApacheを実行できるように設定しました。


apche起動
```
$ sudo systemctl start httpd
```
また、下記のコマンドを実行してApacheが正常に起動しているか確認してください。Activeの箇所にinactive あるいは falied という記述がある場合は、Apacheの起動ができていないか設定ファイルの編集ミスで起動に失敗していますので、再度コマンドを実行し直すか編集した箇所、ディレクトリの場所が正しい位置にあるか見直してください。

```
$ sudo systemctl status httpd
```

以下コマンド実行後の例
```
 ● httpd.service - The Apache HTTP Server
   Loaded: loaded (/usr/lib/systemd/system/httpd.service; disabled; vendor preset: disabled)
   Active: active (running) since 土 XXXX-XX-XX XX:XX:XX UTC; 2s ago
     Docs: man:httpd(8)
           man:apachectl(8)
 Main PID: 16536 (httpd)
   Status: "Processing requests..."
   CGroup: /system.slice/httpd.service
           ├─16536 /usr/sbin/httpd -DFOREGROUND
           ├─16537 /usr/sbin/httpd -DFOREGROUND
           ├─16538 /usr/sbin/httpd -DFOREGROUND
           ├─16539 /usr/sbin/httpd -DFOREGROUND
           ├─16540 /usr/sbin/httpd -DFOREGROUND
           └─16541 /usr/sbin/httpd -DFOREGROUND

 X月 XX XX:XX:XX localhost.localdomain systemd[1]: Starting The Apache HTTP Server...
 X月 XX XX:XX:XX localhost.localdomain httpd[16536]: AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using ... message
 X月 XX XX:XX:XX localhost.localdomain systemd[1]: Started The Apache HTTP Server.
 Hint: Some lines were ellipsized, use -l to show in full.
```

このように何らかのコマンドを実行した場合、必ずその結果や出力を確認する癖を付けてください。コマンドを実行して自分では起動したと思っていても、実際には起動に失敗しているかもしれません。結果を確認する癖を付けることで、今現在どこに問題が生じているか特定が容易になりますね。

さて、起動しましたらブラウザに http://192.168.33.10 と入力し確認してみてください。ブラウザ画面に、Laravelのwelcomeページが表示されましたでしょうか？

おそらく答えは、「No」だと思います。画面すら表示されずの状態かと思います。ではなぜこのような画面が表示されているのか、答えは、 ブラウザに書いてあります。

表示されている文言の下部に「ファイヤーウォール」という単語が確認できますでしょうか？聞きなれない単語かと思いますが、セキュリティの観点では「ファイヤーウォール」は大切な機能であるため、起動状態のままホストOS側からアクセスできるようにしてあげましょう。

Vagrantfileの編集をした際を思い出してください。 config.vm.network "forwarded_port", guest: 80, host: 8080 と記述されていた箇所のコメントを解除したと思います。この 80 という数字は、httpという通信を行うためのポートと呼ばれる窓口番号です。なのでファイヤーウォールに対してこの80ポートを経由したhttp通信によるアクセスを許可するためのコマンドを実行します。


```
# ファイヤーウォールの起動
$ sudo systemctl start firewalld.service
$ sudo firewall-cmd --add-service=http --zone=public --permanent

# 新たに追加を行ったのでそれをファイヤーウォールに反映させるコマンドも合わせて実行します
$ sudo firewall-cmd --reload
```

では、一旦この状態で画面を確認してみます。

もしまだ表示できないようであれば、一度以下のコマンドを実行してください。

```
$ sudo systemctl restart httpd

```
Laravelのwelcome画面が表示されたでしょうか？



それでも表示されなかったときは？

Laravelのwelcomeページが表示されず、 Forbidden 403 というエラーが出た場合の対処法を以下に記載します。(Laravelのwelcomeページが表示された場合は読み飛ばしてしまって問題ありません。)

viエディタを使用してSELinuxの設定を変更します。「SELinux コンテキスト」の不一致によりエラーが出ているので、SELinuxを無効化します。(今回はローカル環境なので無効にしてしまっても問題ありませんが、本番環境構築のときは別のアプローチが必要です。)

```
$ sudo vi /etc/selinux/config

```
viエディタが開き設定ファイルが表示されるので下記の部分を探してください。

```
# This file controls the state of SELinux on the system.
# SELINUX= can take one of these three values:
# enforcing - SELinux security policy is enforced.
# permissive - SELinux prints warnings instead of enforcing.
# disabled - No SELinux policy is loaded.
SELINUX=enforcing

```
こちらの記述を下記のように書き換えて、保存してください。

SELINUX=disabled

設定を反映させるためにゲストOSを再起動する必要があるので、ゲストOSをから一度ログアウトして下記コマンドを実行してください。

```
$ exit
$ vagrant reload

```
リロードが完了したら再度ゲストOSにログインしましょう。

```
$ vagrant ssh

```
再度Apacheを起動します。

```
$ sudo systemctl start httpd

```
これでLaravelのwelcomeページが表示されているはずです。画面を確認してみてください。






プラグイン暗号鍵の変更
https://note.mokuzine.net/ssh-permission-denied/