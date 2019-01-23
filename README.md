# grpc-weapp

- support wechat miniprogram  
- 增加微信小程序支持 
- 增加fromObject方法

## MacOS

```sh
$ sudo xcode-select --install 
$ sudo /opt/local/bin/port install autoconf automake libtool 
```

## grpc-web

```sh
$ git clone https://github.com/11os/grpc-web.git 
$ cd grpc-web 
$ make install-plugin 
```

## protoc

```sh
$ git clone https://github.com/11os/protobuf.git 
$ cd protobuf 
$ git submodule update --init --recursive 
$ ./autogen.sh 
$ ./configure 
$ make 
```

export
  ./src/protoc    # copy to /usr/local/bin <br/>
  ./src/.lib      # copy to /usr/local/lib
   

## Example

### Genreate pb & grpc_web_pb

```sh
$ protoc ./*.proto \
--js_out=import_style=weapp:./src \
--grpc-web_out=import_style=weapp,mode=grpcweb:./src
```

### Before rpcCall

```sh
const client = new service.IAdminUserServicePromiseClient(
  `${hostUrlwithPort}`
)

let newXhr = client.delegateClient_.client_.newXhr_ // hack newXhr function
client.delegateClient_.client_.newXhr_ = () => {
  let xhr = newXhr()
  xhr.xmlHttpFactory_ = { // replace grpc-web XmlHttpRequest 
    createInstance: () => {
      return new parent.__global.XMLHttpRequest() // make sure parent = window
    },
    getOptions: () => {
      return {}
    }
  }
  xhr.setProgressEventsEnabled(true)
  return xhr
}

client.login(request, {}).then((resp) => {
  console.log(resp)
})

```
