# Go-Zero 
[官网](https://go-zero.dev/)
## goctl安装

goctl 是 go-zero 的内置脚手架，是提升开发效率的一大利器，可以一键生成代码、文档、部署 k8s yaml、dockerfile 等。
### golang直装
go 版本在 1.16 及以后，则使用如下命令安装：
```shell
$ go install github.com/zeromicro/go-zero/tools/goctl@latest
```
### 验证
```shell
$ goctl --version
goctl version 1.6.6 windows/amd64
```
## protoc 安装
protoc 是一个**用于生成代码的工具**，它可以根据 proto 文件生成C++、Java、Python、Go、PHP 等多重语言的代码，而gRPC的代码生成还依赖**protoc-gen-go**，**protoc-gen-go-grpc**插件来配合生成Go语言的gRPC代码。
### 一键安装
一键安装 protoc，protoc-gen-go，protoc-gen-go-grpc 相关组件
```shell
$ goctl env check --install --verbose --force
```
```
[goctl-env]: preparing to check env

[goctl-env]: looking up "protoc"
[goctl-env]: "protoc" is not found in PATH
[goctl-env]: preparing to install "protoc"
"protoc" installed from cache
[goctl-env]: "protoc" is already installed in "D:\\GOPATH\\bin\\protoc.exe"

[goctl-env]: looking up "protoc-gen-go"
[goctl-env]: "protoc-gen-go" is installed

[goctl-env]: looking up "protoc-gen-go-grpc"
[goctl-env]: "protoc-gen-go-grpc" is installed

[goctl-env]: congratulations! your goctl environment is ready!
```
## gozero安装
### 安装
```shell
$ mkdir <project name> && cd <project name> # project name 为具体值
$ go mod init <module name> # module name 为具体值
$ go get -u github.com/zeromicro/go-zero@latest
```
## goctl-vscode 安装
打开 Visual Studio Code | Extensions，搜索 goctl，点击 install 安装。