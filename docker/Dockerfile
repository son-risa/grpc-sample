# protoc-allを使ってprotocol bufferファイルを生成
FROM namely/protoc-all AS protoc
ADD rpc /go/src/github.com/son-risa/grpc-sample/rpc
WORKDIR /go/src/github.com/son-risa/grpc-sample/rpc
RUN protoc -I helloworld --go_out=plugins=grpc:helloworld helloworld/main.proto

# server/webclientをビルド
FROM golang:alpine AS build
RUN apk add --no-cache git
RUN go get "github.com/golang/protobuf/proto"
RUN go get "google.golang.org/grpc"
RUN go get "google.golang.org/grpc/codes"
RUN go get "google.golang.org/grpc/status"
# grpcをコンパイル
WORKDIR /go/src/github.com/son-risa/grpc-sample/rpc/helloworld
COPY --from=protoc /go/src/github.com/son-risa/grpc-sample/rpc/helloworld/main.pb.go .
# serverをコンパイル
ADD server /go/src/github.com/son-risa/grpc-sample/server
WORKDIR /go/src/github.com/son-risa/grpc-sample/server
RUN go build
# webclientをコンパイル
ADD webclient /go/src/github.com/son-risa/grpc-sample/webclient
WORKDIR /go/src/github.com/son-risa/grpc-sample/webclient
RUN go build

FROM alpine
COPY --from=build /go/src/github.com/son-risa/grpc-sample/server/server /usr/local/bin/server
COPY --from=build /go/src/github.com/son-risa/grpc-sample/webclient/webclient /usr/local/bin/webclient
