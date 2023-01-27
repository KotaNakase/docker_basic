## Docker入門
### Dockerとは？
Linuxサーバ上でコンテナを作成・実行・削除等の管理を行うことができるツール。コンテナ技術の一つ。
データの保持自体は一時的なものである。Dockerを立ち上げなおす度にホストOS→Docker→データベースの導線が引かれる。

### コンテナーとは？
アプリを隔離されたパッケージにできる機能。以前作成したVMでは、一つの仮想マシンに全てのアプリケーションをインストールしたが、コンテナの考え方としては1コンテナに1ソフト格納されいる。

* 仮想マシンの作成との違い...仮想マシンを作成する場合、ストレージ、メモリ、CPU等(ハードウェア全て)を用意するため、VM一つ当たりのサイズがかなり大きくなる。(以前作成したVMはストレージ20GB,メモリ2GB)ホストOSのメモリが8GBなのでいいとこ3台が限界である。それに比べてコンテナはホストOSの上に直接コンテナが立ち上がるようなイメージ(ゲストOSなし)。最小限のリソースでOS上に存在するので、VMよりもはるかに多くコンテナを立ち上げることができる。

#### インフラのコード化
Dockerfileという設計図にあたるものがあり、構成がファイルを読むことでわかる。Dockerイメージを言語ファイル化したもの。
infrastracture As a Codeといい、インフラをコードで管理する画期的なシステム。

#### DockerfileとDockerイメージの関係性
Dockerコンテナ...Webサーバ,DB,PHPなど言語のように一つのプロセスを入れる箱。  
Dockerイメージ...Azureでのイメージ。同じ。  
Dockerfile...Dockerイメージを言語化し、ファイルにしたもの。ymlファイル。  
ビルド...DockerfileからDockerイメージを作成すること。  
ラン...DockerイメージからDockerコンテナを作成すること。

#### Dockerイメージの構造
一番下にLinuxOS(まっさらなLinux。CentOSやUbuntu)  
その上にレイヤー構造(Dockerイメージ)が積み重なっている。(インストール→設定→...)  

#### Dockerイメージのタグ機能
タグの指定は必ず行うこと。いつ作成しても同じ環境であるために、latest(最新版)ではなく、バージョンを指定すること。

#### Dockerでアプリケーション環境を作る方法
コンテナそれぞれが隔離された環境下にあるので、一つのOSにインストールした時のように、それぞれのサービスが連携できないように思える。  
→**Docker-Composeを用いることで実現可能。(リンクオプション)**

#### Docker Tool Boxのインストール
Docker環境を構築するためのインストーラ  
**VirturalBoxでLinux環境が構築され、その中にDockerがインストールされている?**→そう
ToolBoxがLinux環境とやり取りするためのもの。とは言いつつコマンドプロンプトをDocker用にカスタマイズしたものなので、基本的にコマンドなどの操作はコマンドプロンプトで行える。

#### Docker Hubとは?
Docker社がクラウド上で提供しているDockerイメージ（コンテナ）の共有サービスのこと。クラウド上のレジストリからほしいイメージを取得してコンテナ化できる。自分で作成したイメージをリポジトリにアップすることもできる。

---

### セクション4
#### docker run
DockerイメージからDockerコンテナを起動するコマンド。イメージがローカルに存在しない場合は、Docker Hubから自動で取得。
```
docker run --name コンテナ名 イメージ名
```
名前を付けてコンテナを起動できるオプション。
```
docker run --name コンテナ名 --rm イメージ名
```
コンテナの停止後にそのまま削除されるオプション。

#### docker ps
現在起動中のコンテナが表示される。停止中のコンテナも表示する場合は-aをつけると表示される。

#### docker start/stop/restart
コンテナ1つを操作するコマンド。起動、停止、再起動を行う。  
起動
```
docker start コンテナ名/ID
```
停止
```
docker stop コンテナ名/ID
```
再起動
```
docker restart コンテナ名/ID
```

#### docker exec
* コンテナに入るとは？  
ホストPCからコマンドを使用して起動中のコンテナ内にアクセスすること。
```
docker exec -it コンテナ名 /bin/bash
```
起動中のコンテナ内にアクセスする。centOSのコンテナを起動してbin下のbashを実行(-it)。  
centOSはbashを実行しないと起動後すぐに終了する。コンテナから出た場合も同様にすぐに終了する。実行待ち状態にはならない。

CentOSのバージョンを取得
```
cat /etc/redhat-release
```

コンテナに入ることなく、コマンド操作を行う
```
docker exec mycentos cat /etc/redhat-release
```

#### docker rm
停止中のコンテナの削除。-fオプションによって起動中のコンテナの削除も行える。
```
docker rm コンテナ名/ID
```

#### docker images
ローカルにあるイメージを表示する。ストレージが圧迫した際にこのコマンドで使用状況を確認する。
```
docker images
```

#### docker rmi
rmi...remove image。Dockerイメージを削除。※起動中のコンテナのイメージは削除できない。イメージの上に成り立っているものなので、イメージを削除する場合はまずコンテナから消す必要がある。

コンテナの停止
```
docker stop コンテナ名/ID
```
コンテナの削除
```
docker rm コンテナ名/ID
```
イメージの削除
```
docker rmi イメージ名/ID
```

#### docker build
dockerfileからdockerイメージを作成する
```
docker build ディレクトリ名  
```
**ディレクトリ内に複数のdockerfileがあるとどうなるのか**  

#### docker cp
ホストPCからコンテナへのファイルコピーを行う。逆も可。

ホストPC→コンテナ
```
docker cp コピー元のアドレス コンテナ名:コピー先のアドレス  
```
コンテナ→ホストPC
```
docker cp コンテナ名:コピー元のアドレス コピー先のアドレス
```

#### docker logs
Dockerコンテナのログ出力。コンテナの動作確認、アクセスログなどの監視。

コマンドの履歴を確認
```
docker logs -f コンテナ名
```
-fオプションによってリアルタイム監視状態になる。

* Tips  
tails -fというLinuxサーバの監視でよく使われるコマンドがある。これもリアルタイム監視を行っている。やってることは両者同じ。

#### docker inspect
Dockerの詳細情報出力。トラブル発生時に確認することがある。ほとんど使用しない。
```
docker inspect コンテナ名
```

#### docker pull
Dockerイメージをダウンロードするコマンド。  
docker runで良いのでは？→DockerHub以外からのダウンロードが可能。機密情報を扱う場合などに、DockerHub以外から入手する手段としてある。
```
docker pull イメージ名:タグ
```

※タイムアウトになる場合はダウンロードの並列処理(3列→1列)に変更して防ぐ(max-concurrent-downloads)。

#### docker commit
コンテナをイメージ化する。コンテナにレイヤーを追加して、その状態をイメージ化したい時に使う。
```
docker commit コンテナ名 DockerHubID/イメージ名:タグ  
```
ここで作成したイメージはDockerHubにアップできる。

#### docker push
イメージをDockerHubにアップ。
```
docker push アカウント名/イメージ名
```
作成したイメージ  
https://hub.docker.com/r/kotanakase/nakase-centos

#### docker hisory
イメージの更新履歴が確認できる。他人のイメージをDockerHubからダウンロードして参考にするなど。

#### Dockerコンテナのストレージ
コンテナはメモリ状に存在するため、コンテナが削除されるたびにデータが失われる。(ディスク上に存在する仮想マシンと違う特徴)  
→コンテナは作成・破壊を想定されて作られている。

* 永続化の実現方法...ホストPCとコンテナのディレクトリを共有することで解決。

→データの書き込みをデータベース、ホストマシンのディスク両方に書き込むことで、データを永続化できる。(共有はどのように？)

#### Docker runのvolumeオプション
* 具合的な共有方法  
ホストPCのディレクトリをコンテナが参照できるようにコンテナにマウントする?
ホストPCの指定したディレクトリとコンテナ側のディレクトリを一致させることでデータの永続化を実現している。同じフォルダを見に行っており、ホストのデータを逐一複製しているわけではない。

#### Docker Tool Boxで立ち上げたコンテナへのアクセス方法
IPアドレスで区切られた場所とは、コンテナがローカルホストに存在しない?  
windowsではcmdでdockerコマンドを実行すると、立ち上げたLinux上で処理が行われる。Linux上にサーバーが立ち上がっているのでホストPCのローカルではない→windowsではローカルホストからの接続はできない。

#### nginxの立ち上げ
```
docker run -v htmlファイルのアドレス:VM上のアドレス --name mynginx -p 8080:80 nginx:1.16
```
-vオプションでnginxが参照するアドレスを指定してホストPCとフォルダを共有する(マウント)。  
-オプションでIPフォワーディングを行っている。VMに向けて8080ポートにリクエストした場合、それを80へのアクセスに変換してリクエストを行っている。

#### 本番環境ではどのようにデータを扱っている?
データベースを冗長構成にしている。→複数のデータベースのうちの一つをマウントすると他のデータベースとの状態の差異が生まれてしまい、コンテナが情報を持ってしまっている状態に。  
* 解決策1...Amazon auroraを使用。Amazon auroraにマウントしている?
* 解決策2...データベースをコンテナ化して、データはクラウド上の高速SSD1箇所に保存。  
どちらにしてもデータベースのデータを一か所に集める必要がある。

#### Dockerfile
Dockerイメージをコード化したもの。読むだけでインフラ構成がわかる。Dockerfileを使えば同じイメージ、同じコンテナを作成することができる。

* Dockerfileの作り方(推奨)
  * コンテナはエフェメラルであるべき。  
エフェメラル...コンテナ一つ一つが状態を持たない。停止・破棄可能。
  * コンテナ一つ一つに不要なパッケージのインストールを避ける。  
  * コンテナ毎に1つのプロセスだけ実行。webサーバ、DB、アプリケーションサーバ...これらに対してそれぞれコンテナを立ち上げること。最終的にこれらのコンテナを連携させて、webサーバのようなふるまいをさせる。
  * レイヤーの数は最小限に。
  * 複数行の記述はバックスラッシュで改行すること。

#### RUNとCMD
コマンドの実行を行う。→両者で実行タイミングが異なる。
* RUN...Dockerfile→イメージ
* CMD...イメージ→コンテナ

Dockerfile例  
```
FROM ubuntu:20.04
RUN apt-get update -y && \
    apt-get install -y nginx
CMD ["nginx", "-g", "daemon off;"]
```
ビルド時にRUNコマンドが実行され、コンテナの立ち上げ時にCMDコマンドが実行される。
&& \を使うことで、レイヤーを一つにできる。以下の書き方と同じ内容。**※無駄なレイヤー**
```
RUN apt-get update -y
RUN apt-get install -y nginx
```
Dockerfile作成後、イメージを作成
```
docker build -t イメージ名 Dockerfileのアドレス
```
-tオプションでDockerfileから作成したイメージに名前をつけることができる。  
-yオプションでインストール時のyes noの確認を無視できる。

作成したイメージからコンテナを作成  
```
docker run -d -p 8081:80 --name コンテナ名 イメージ名
```
-dオプションでwebサーバがバックグラウンド起動される。**有無でどう違うか分からない。フォアグラウンドとの違い** →コンソールで立ち上がるかそうでないか。Daemon offの指定をしていたため、Ctrl+Cでも起動したままだった。

#### COPYとADD
どちらもファイルをイメージに追加するコマンド
* ADD...ネット経由でファイルのダウンロードができ、tarzipのような圧縮ファイルもダウンロードできる。
* COPY...ADDに比べて機能が少ない。**Docker公式が推奨**

いつ使うのか...プログラムのソースコードをイメージに入れたい場合や、設定ファイルを予め入れておき、その設定ファイルが反映された状態でイメージを作成したい場合。

例)index.htmlをnginxコンテナの/var/www/html内にコピーする
```
COPY コピー元ファイル コピー先のアドレス
```
Dockerfile
```
FROM ubuntu:20.04
RUN apt-get update -y
RUN apt-get install -y nginx
COPY index.html /var/www/html
CMD ["nginx", "-g", "daemon off;"]
```

* **なぜ初期起動時のHTMLが参照されなくなったのか。優先度がある?** →そう。index.htmlが優先して表示される。

ボリュームマウントでhtml表示すればよいのでは?
Dockerfile自体の用途として環境を用意する側が作成して他人に作ってもらうもの。  
ボリュームマウントだとhtmlファイルを自分で用意するため、最終的な結果が同じであるが、目的が違う。

#### ENV(環境変数)
このコマンドを使用することで、ビルドのタイミングで環境変数を決めることができる。(データベースへの接続先の設定、動作環境など)

```
FROM ubuntu:20.04
RUN apt-get update -y
RUN apt-get install -y nginx
ENV TESTENV="nakase"
ENV APP_ENV="production"
CMD ["nginx", "-g", "daemon off;"]
```

作成したコンテナの環境変数を確認
```
docker inspect コンテナ名
```

#### MariaDBの作成
```
FROM mariadb:10.4
RUN apt-get update -y
COPY my.conf /etc/mysql/conf.d
COPY create-table.sql /docker-entrypoint-initdb.d
ENV MYSQL_USER=root
ENV MYSQL_DATABASE=docker
ENV MYSQL_ROOT_PASSWORD=root
```
今まで作成していたubuntuのまっさらなOSではなく、公式のMariaDBを作成。
**何が違うのか。** →公式のイメージにはOSのインストールも含まれているので
起動時設定のCMDは不要。  
mariaDB:10.4が作成されたコンテナのOSはubuntu20.04だった。

confファイルとsqlファイルをそれぞれ、MariaDBの設定ファイルのディレクトリに置いている。

コンテナ作成後、DBにログイン
```
mysql -u root -p
```

### docker-compose
docker-composeとは...複数のコンテナを連携させて、それらを管理できる仕組みのこと。yml形式の設計図を作成することで実現可能。

* docker run --linkによる連携方法
1. DBコンテナの立ち上げ(mysql:5.6)
```
docker run --name コンテナ名 -e MYSQL_ROOT_PASSWORD=password -e MYSQL_DATABASE=wordpress -d mysql:5.6
```
MYSQLのパスワード・使用するデータベースを環境変数に指定。

2. wordpressコンテナの立ち上げ
```
docker run -p 8084:80 -e WORDPRESS_DB_PASSWORD=password -d --name wordpress --link コンテナ名(db):mysql wordpress:4.7-php5.6
```
--linkでwordpressのデータベースとしてMYSQLを指定。

上記の方法では複数のコンテナの連携が非現実的であるため、docker-composeを使用することが推奨されている。

#### ymlファイル
jsonファイルは{}で入れ子を表現⇔ymlファイルはスペースで入れ子を表現  
ymlファイル(docker-compose)例
```
version:"3"
  services:
    web:
      build: ./php
    db:
      image: mariadb:10.4
```
* versionとservicesの項目は必須
* services内に立ち上げるコンテナを記載し、要素名は任意でよい。(web,db)

#### 相対パスと絶対パス
* 絶対パス(ルートディレクトリから辿る)
/Users/中瀬康汰/Documents/env

* 相対パス(カレントディレクトリから辿る)
./env

#### imageとbuild
docker-composeにおけるimageとbuild
* image→DockerHubから指定(docker runと同じ書き方)
* build→Dockerfileのパスを指定

どこからイメージを作成するかで書き方が違う。特別カスタマイズする内容がなければ、imageで作成するとよい。

#### container_name
コンテナに任意の名前を付与できる。(docker run --nameオプションと同じ)  
名前はダブルクォーテーションで囲うこと。  
例)
```
version:"3"
  services:
    web:
      build: ./php
      container_name: "php8"
    db:
      image: mariadb:10.4
      container_name: "db2"
```

#### volume
ホストPCとコンテナのディレクトリを共有(docker run -vオプションと同じ)  
例)
```
version:"3"
  services:
    web:
      build: ./php
      container_name: "php8"
    db:
      image: mariadb:10.4
      container_name: "db2"
      volume: ./data:/var/lib/mysql
```
ホスト側のパスは相対パスで書くこと。
* C:\Users\\**中瀬康汰**\Documents\  
絶対パスの場合、ユーザー名の影響を受けてしまうため、Dockerの特性が活かせていない。(ユーザー毎にymlの書き換えが必要になる)

#### ports
コンテナのポート開放とポートフォワーディング(docker run -pオプションと同じ)  
例)
```
version:"3"
  services:
    web:
      build: ./php
      container_name: "php8"
      ports:
        "8080:80"
    db:
      image: mariadb:10.4
      container_name: "db2"
      volume: ./data:/var/lib/mysql
```
ポート番号の記述は入れ子にすること。

#### docker-compose間の通信方法
docker-composeで作成されるコンテナ群は小さなネットワークで囲われている。  
その中で、サービス名を内部IPアドレスに変換されている。(ミニDNS)  
DNSの操作を自動で行ってくれるのでサービス名の指定さえすれば互いに通信できる。

#### docker-composeを使用したwordpressの立ち上げ
ymlファイルを用意した後にコンテナをまとめて作成。
Dockerfile.yml
```
version: "3"
services:
  db:
    image: mariadb:10.4
    container_name: "wordpress-db"
    volumes:
      #- ./db-data:/var/lib/mysql→このディレクトリでは上手くいかない
      - ./db-data:/var/lib/mariadb
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wordpress
      MYSQL_PASSWORD: wordpress
    
  wordpress:
    image: wordpress:5
    container_name: "wordpress-wp"
    volumes:
      - ./wp-data:/var/www/html
    ports:
      - "8000:80"
    environment:
      WORDPRESS_DB_HOST: db:3306
      WORDPRESS_DB_USER: wordpress
      WORDPRESS_DB_PASSWORD: wordpress
      WORDPRESS_DB_NAME: wordpress
```
フォルダをそれぞれのコンテナで共有してDB,WordPressのカスタマイズを行える。  
環境変数をそれぞれ接続情報を記述。**WORDPRESS_DB_HOST: db:3306** でサービス名(db)とすると内部IPに変換してくれる。

```
docker-compose up -d
```

IP、ポート番号を指定してwordpressが開ければ成功。  
ymlファイルのdb側の共有フォルダをmariadbに変更することでwordpressが起動した。原因は、mysql内のファイルがきっかけでエラーをはいている可能性がある。  
実行環境による違いが原因の可能性もあり。(自分のPC以外では問題なく起動する)

エラー原因のヒント
```
[Warning] InnoDB: Failed to set O_DIRECT on file./ibdata1; CREATE: Invalid argument, continuing anyway. O_DIRECT is known to result in 'Invalid argument' on Linux on tmpfs, see MySQL Bug#26662.
```


#### docker-composeのコマンド
docker-compose.ymlで管理されているコンテナをストップ&削除
```
docker-compose down
```
docker-compose.ymlで管理されているコンテナを再起動
```
docker-compose restart
```
docker-compose.ymlで管理されているコンテナ一覧を表示。複数のプロジェクトを立ち上下ている際の管理に便利
```
docker-compose ps
```
docker-compose.ymlで管理されているサービス一つを指定してコマンドを実行
```
docker-compose run サービス名 実行したいコマンド
```

