---
title: HTTP/3试水
date: 2023-1-11 21:57:32
---

## HTTP/3试水

### I

小看一眼，发现全是编译啥的。应该会踩很多坑的，这次用国外没东西的小1h512M服务器试水一下。
目前cloudflare给出了一键开启http3。很好用，我在netlify托管的网页一键开开了。
![1](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20230111221046.png)
![2](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20230111221121.png)

### II

现在要编译安装一个nginx，用的是<https://github.com/cloudflare/quiche>方案。
<https://github.com/cloudflare/quiche/tree/master/nginx>处理nginx的教程在这里，一步步跟上

```bash
  curl -O https://nginx.org/download/nginx-1.16.1.tar.gz
  tar xzvf nginx-1.16.1.tar.gz
  git clone --recursive https://github.com/cloudflare/quiche
  cd nginx-1.16.1
  patch -p01 < ../quiche/nginx/nginx-1.16.patch
```

在之前要apt upgrade一下避免有装不上的东西
![3](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20230111221522.png)
外网服务器下的就是快😎
要编译了发现cmake没有  装个cmake先
`apt install cmake`

gcc g++也没有 装个

接下来

``` bash
  ./configure                                 \
       --prefix=$PWD                           \
       --build="quiche-$(git --git-dir=../quiche/.git rev-parse --short HEAD)" \
       --with-http_ssl_module                  \
       --with-http_v2_module                   \
       --with-http_v3_module                   \
       --with-openssl=../quiche/quiche/deps/boringssl \
       --with-quiche=../quiche
  make
```

nginx configure 时候报错，
`the HTTP rewrite module requires the PCRE library.`
搜了 装这个 `apt install libpcre3 libpcre3-dev`

`the HTTP gzip module requires the zlib library`
搜了 装这个 `apt install zlib1g-dev`

又error 没有cargo 装个
`apt install cargo`

make好了

``` bash
nginx -V
```

装是装好了 好像要一个https证书啊
acme签一个
`https://github.com/acmesh-official/acme.sh/wiki/%E8%AF%B4%E6%98%8E`

```conf
events {
    worker_connections  1024;
}

http {
    server {
        # Enable QUIC and HTTP/3.
        listen 443 quic reuseport;

        # Enable HTTP/2 (optional).
        listen 443 ssl http2;

        ssl_certificate      cert.crt;
        ssl_certificate_key  cert.key;

        # Enable all TLS versions (TLSv1.3 is required for QUIC).
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2 TLSv1.3;

        # Add Alt-Svc header to negotiate HTTP/3.
        add_header alt-svc 'h3=":443"; ma=86400';
    }
}
```

签证书签了半天 签好了 放在
``` bash
        ssl_certificate      cert.crt;
        ssl_certificate_key  cert.key;
```
里面
配置好nginx 注意网站不要在root下
在root下nginx访问不到直接403了
建一个`/www/wwwroot/domin`文件夹放东西

### III

最后配置结果是这样滴

#### 第一个

<https://geekflare.com/tools/test/yn22t29e04y7v0u5jl61mcark2dwxmbn>
![6](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20230112004019.png)
这个网站会检测什么h3-xxx的版本协议 （看不懂，看懂了在写一篇
你没有这个版本号的协议这个网站就检测不出来好像 在这里改就行
`add_header alt-svc 'h3=":443"; ma=86400';`
我加了版本号114514🤓
`add_header alt-svc h3=":443"; ma=86400; h3-114514=":443"; ma=86400`
上面这个好像就检测这个alt-svc这个头

#### 第二个

<https://http3check.net/?host=http3.typescriptactions.xyz>
![7](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20230112004253.png)
这个就很正常了 不加版本直接检测h3的

#### 第三个

然后是wappalyzer插件
![8](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20230112004611.png)

#### 第四个

然后是浏览器devtools的协议检查
![9](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20230112004854.png)

### IV

总结一下
2023-1-11 21:57:32 开始的
2023-1-12 00:53:12 写完这篇
🤥感觉没啥可总结的 国外服务器也测不了http3优势啥的
下班！睡觉！😝
