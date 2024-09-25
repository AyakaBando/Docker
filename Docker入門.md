# Docker入門

PHPで作成されているウェブサイトのテスト用。
Docker＋Ubuntu。

## インストール方法
### 必要なもの：
[VSCode](https://code.visualstudio.com/download)
[Git](https://git-scm.com/downloads)
[WSL](https://learn.microsoft.com/ja-jp/windows/wsl/install)
[Docker](https://docs.docker.com/desktop/install/windows-install/)
[Ubuntu](https://apps.microsoft.com/detail/9pn20msr04dw?hl=ja-jp&gl=JP)

### 手順：
**以下をWindows Powershellで実行する**
1. Windows Powershellをで管理者として実行するために以下コマンドを入力
`Start-Process powershell -verb runas`
2. wslをインストールするために以下コマンドを入力
`wsl --install`
3. wslのインストールが完了したことを確認
`wsl --version`
4. インストール可能なディストリビューションの一覧
`wsl --list --online`
5. Ubuntuをインストール
`wsl --install -d Ubuntu`
6. Ubuntuのバージョンを確認
`ubuntu -v`
7. Dockerのバージョンを確認
`docker -v`
**Powershellここまで**
Ubuntuをアプリケーションから開いてインストールできているか確認。
インストールできている場合はusernameとpasswordの作成を求められる。

参考：[WSL 開発環境を設定する](https://learn.microsoft.com/ja-jp/windows/wsl/setup/environment)

wsl2からDockerを実行的ない場合は以下を確認：
[Windows の機能の有効化または無効化の設定](https://qiita.com/ryome/items/240f36923f5cb989da27)
Powershellで`docker -v`を実行するとインストールが確認できるのにWSL上ではエラーが出る場合
[wsl上のubuntuでdockerが使用できない](https://zenn.dev/kathmandu/articles/4a86c3d75b93c3)
Ubuntuが起動できない場合

username: banaya
password: banaya

### Linuxコマンドについて
シェル
: Linuxのコマンドを受け取って結果を出力するためのソフトウェア
ターミナル
: コマンドを入力する画面
（ターミナルの画面の中でシェルが動いている）

- 命令の受け取り方
ターミナル → シェル → Linux
Linux → シェル → ターミナル

- ls
今のディレクトリの中のファイルやフォルダの一覧

- pwd
今自分がいるディレクトリ

- cd
 ディレクトリを移動するコマンド

- mkdir
 ディレクトリの作成

- touch
新規ファイルの作成

- echo
`echo 'Hello' > tmp`
文字列の書き込み（上記の例は「Hello」という文字列を「tmp」というファイル内に書き込む）

- cat
ファイルの中身を確認する

- less
ファイルの中身を見る（全画面表示）
「q」で全画面モードを終了する

- mv
`mv 現在の名前　新しい名前`
ファイル名の変更

- rm
ファイルの削除

- cp
ファイルのコピー


## 代表的なDockerコマンド
- イメージからコンテナを起動
`docker container run [OPTIONS] IMAGE [COMMAND] [ARG...]`

- 起動中のコンテナに命令する
`docker container exec [OPTIONS] CONTAINER COMMAND [ARG...]`

- イメージを作成する
`docker image build [OPTIONS] PATH | URL | -`

- その他サブコマンドとコマンド
    - サブコマンド
        - container
        - image
        - volume
        - netowrk
    - コマンド
        - ls
        - inspect
        - rm
        - prune
※サブコマンドにそれぞれそれぞれ４つのコマンドが存在する


## コンテナの基本操作
- イメージからコンテナを起動
`docker container run [OPTIONS] IMAGE [COMMAND] [ARG...]`
    - 起動時に実行するコマンドを指定する場合は`[COMMAND] [ARG...]`部分に記載する
> Ubuntuの場合`[COMMAND]`部分のデフォルト値は`bash`
>> 例\
>> `docker container run ubuntu whoami`\
>> `docker container run ubuntu head -n 4 /etc/os-release`

- 起動中コンテナ一覧の確認
`docker container ls [OPTIONS]`
    - 起動中以外のコンテナを確認する場合
    `docker container ls -a`

- コンテナの停止
`docker container stop [OPTIONS] CONTAINER [CONTAINER...]`
> CONTAINERの後にCONTAINER IDを入れる
>> 例\
>> `docker container stop CONTAINER 4ece6293df6a`

- コンテナの削除
`docker container rm [OPTIONS] CONTAINER [CONTAINER...]`
    - 強制削除する場合
    `docker container rm -f CONTAINER [CONTAINER...]`

- コンテナに名前をつける
`docker container run --name [NAME] [IMAGE] [COMMAND] [ARG...]`
※コンテナを削除するときにはこのコンテナ名で削除することが可能

- コンテナ停止時に自動で削除
    - コンテナの名前重複時に終了コンテナを自動で削除できる
`docker container run --rm [IMAGE] [COMMAND] [ARG...]`
> [OPTIONS]を2種類以上使うとき
>> 例\
>> `docker conttainer run --name hello --rm hello-world`

- コンテナを対話操作する
`docker container run --interactive [IMAGE] [COMMAND] [ARG...]`
`docker container run --tty [IMAGE] [COMMAND] [ARG...]`

- コンテナのポートを公開する
    - コンテナはホストマシン上で隔離されており、コンテナ内のプロセスにホストマシンからはアクセスできない→ウェブシステム開発時にブラウザからサーバーの動作確認ができない
    - `--publish`オプションで任意のポートをホストマシンのポートにマッピングして公開できるようにする
`docker container run --publish [IMAGE] [COMMAND] [ARG...]`

- コンテナの環境変数を設定する
`docker container run --env [IMAGE] [COMMAND] [ARG...]`

- MySQLサーバーを起動する
> 以下の書き方はエラーが出る\
> `docker container run --name db --rm mysql`\
> エラー内容\
>  "You need to specify one of the following as an environment variable":\
>> - MYSQL_ROOT_PASSWORD
>> - MYSQL_ALLOW_EMPTY_PASSWORD
>> - MYSQL_RANDOM_ROOT_PASSWORD

> - 環境変数を指定するには`--env`オプションに続けて`変数名=値`の形式で変数を指定する
> - MySQLサーバーは3306晩ポートで起動する
>> `docker container run --name db --rm --env MYSQL_ROOT_PASSWORD=secret --publish 3306:3306 mysql`

- MySQLサーバーに接続する
`mysql --host=127.0.0.1 --port=3306 --user=root --password=secret`

- MySQLサーバーを起動しユーザーとデータベースを作成する
`docker container run --name db --rm --env MYSQL_ROOT_PASSWORD=secret --env MYSQL_USER=app --env MYSQL_PASSWORD=pass1234 --env MYSQL_DATABASE=sample --publish 3306:3306 mysql`
※複数の環境変数を指定するには--envを繰り返し指定する

- コンテナをバックグラウンドで実行する
`docker container run --detach`
※NginxコンテナやMySQLコンテナは起動した後にターミナルで操作しないためバックグラウンドで実行した方が便利。

- コンテナの出力を確認する
`docker container logs [OPTIONS] CONTAINER`
※エラーが出るたびに出力が増える。増える出力を確認し続けたい場合は`--follow`オプションを使う。

- PostgresSQLサーバーへの接続
`docker container run --name db --detach --env POSTGRES_PASSWORD=secret --publish 5432:5432 postgres`
`psql --host=127.0.0.1 --port=5432 --username=postgres`

>※以下のようなエラーが出る場合\
> `psql: エラー: "127.0.0.1"、ポート5432のサーバーへの接続に失敗しました: FATAL:  ユーザー"postgres"のパスワード認証に失敗しました`\
> C:\Program Files\PostgreSQL\16\data\pg_hba.confのMETHODをtrustに変更してみる

- 起動中のコンテナに命令する（起動中のコンテナで新たなコマンドを実行する）
`docker container exec [OPTIONS] CONTAINER COMMAND [ARG...]`

> - `container exec` と`container run`の違い
> - `container run`はコンテナを起動してコンテナ内でPID1を生成する。また、既存のコマンドで新たに`container run`を実行させるとコマンドを置き換えてしまう。
> - `container exec`はPID1がすでに存在するコンテナで新たなプロセスを起動する。


##　イメージの活用
- 完全イメージ名とタグ
`[HOST[:PORT_NUMBER]/][NAMESPACE/]REPOSITORY[:TAG]`
※`[:TAG]`に`latest`を利用することは推奨されていない

> - `docker.io/library/ubuntu:23.10`
> - `docker.io` -> HOST
> - `library` -> NAMESPACE
> - `ubuntu` -> REPOSITORY
> - `23.10` -> TAG

## イメージの基本操作
- コマンド一覧の確認
`docker image ls [OPTIONS][REPOSITORY[:TAG]]`

- イメージの取得
`docker image pull [OPTIONS] NAME[:TAG | @DIGEST]`

- イメージの詳細の確認
`docker image inspect [OPTIONS] IMAGE [IMAGE...]`

- コンテナにvimをインストールする
`docker container run --name ubuntu -it ubuntu:22.04 bash`
※書き込みをする必要があるため`-it (--interactive --tty)`を記載する。
> - `apt update`
> - `apt install vim`
> -  `which vi`

- コンテナからイメージを作成する
`docker container commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]`
※container配下のコマンドであること

- コンテナからtarアーカイブファイルを作成
`docker container export [OPTIONS] CONTAINER`
> tarアーカイブとは
> - tar -> アーカイブファイルを読み取り・書き込み・リストする
> - アーカイブファイル -> 1つまたは複数のファイル、ディレクトリ、あるいはその両方が入っている単一ファイルのこと

- アーカイブファイルからイメージを作成する
`docker image import [OPTIONS] file | URL | - [REPOSITORY[:TAG]]`

- イメージからtarアーカイブファイルを作成
`docker image save [OPTIONS] IMAGE [IMAGES...]`

- `image save`で作成したtarアーカイブファイルからイメージを作成する
`docker image load [OPTIONS]`

> `image save` + `image load`の目的
> - イメージをtarアーカイブファイルにしてファイルシステムで扱えるようにする
> - イメージのバックアップやマシン間の移動で使用できる

## Dockerfileの活用
- RootFS -> ファイルシステムを変更するtarアーカイブファイルの情報
- histoy -> メタデータ設定を含む履歴の配列
- DockerfileのFROM以外の命令は1命令で1履歴を作る

- FROM命令でベースイメージを指定する
`FROM [--platform=<platform>] <image> [AS <name>]`
    - Dockerfileの1行目は`FROM`から始まる

    - Dockerfileでイメージをビルドする
    `docker image build [OPTIONS] PATH | URL | - `
        - PATH - ディレクトリのパス
        - URL - GitHubリポジトリやtarアーカイブファイルのあるURL等
        - '-' - テキストファイル

> 例:（Current Directoryを指定する）\
> `docker image build --tag my-ubuntu:22.04 . `
