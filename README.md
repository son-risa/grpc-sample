# 目的
grpcのサンプルアプリをDockerコンテナ化し、Kubernetesにデプロイするとともに、istioとEnvoyを使ってサービスメッシュで管理し、サービスの利用状況をPrometeusとGrafanaで可視化するサンプルを作成する

# 参考にしたURL

- protoファイルの置き場所について
https://qiita.com/lufia/items/bcdb5081ddc10af50d8a


# コンパイル手順
1. protoファイルを読み込んでプログラムファイルを生成
```
$ cd $GOPATH/src/github.com/son-risa/grpc-sample/rpc
$ ls helloworld
main.proto
$ protoc -I helloworld --go_out=plugins=grpc:helloworld helloworld/main.proto
$ ls helloworld
main.pb.go      main.proto
```

2. サーバコードをコンパイル
```
$ cd $GOPATH/src/github.com/son-risa/grpc-sample/server
$ ls
main.go
$ go build
$ ls
main.go server
```

3. クライアントコードをコンパイル
```
$ cd $GOPATH/src/github.com/son-risa/grpc-sample/client
$ ls
main.go
$ go build
$ ls
main.go client
```

4. 実行方法

    1. サーバを実行するとポート番号50051でListenし、クライアントからのメッセージを待つ。
    ```
    $ $GOPATH/src/github.com/son-risa/grpc-sample/server/server
    ```

    2. クライアントを実行し、サーバにメッセージを送る。
    ```
    $ $GOPATH/src/github.com/son-risa/grpc-sample/client/client こんにちは
    ...
    2019/04/26 14:44:41 Greeting: Hello こんにちは
    ```

    3. サーバ側にもメッセージが表示されます。
    ```
    2019/04/26 14:44:41 Received: こんにちは
    ```

# protoファイルの構造と生成されるGo言語のプログラムを解説



# メモ
- protoパッケージについて：protoファイルはプロジェクトで共有するものなので、生成されたpb.goのパッケージ名やファイルの置き場所は一元管理されることが望ましい。Go言語では、環境変数GOPATHからのフォルダ構成がパッケージ名に相当するので、ソースコード管理をGithubを使っている場合は、$GOPATH/github.com/[組織名]/[アプリ名]/rpc/[パッケージ名]/main.protoというファイルを作成し、パッケージ名は「github.com/[組織名]/[アプリ名]/rpc/[パッケージ名]」とすることにする。こうすることでプロトコルバッファーを参照する場合「pb "github.com/son-risa/grpc-sample/rpc/helloworld"」と指定することで対応ができる。

