# HTTP 实体数据
#### 类别 MIME type
* Multipurpose Internet Mail Extensions MIME
* text
* image
* audio/video
* application

#### 压缩算法Encoding type
* gzip
* deflate
* br HTTP优化的新压缩算法

#### 内容协商
* Accept-Encoding 客户端支持的压缩格式
* Content-Encoding 服务器端支持的压缩格式
* Accept-Language 自然语言
* Content-Language 实际语言
* Vary表示服务器依据了 Accept-Encoding、User-Agent 和 Accept 这三个头字段，然后决定了发回的响应报文

#### 请求头
```
Accept-Encoding: gzip, deflate;q=1.0, *;q=0.5, br;q=0
```
* 服务器优先按照gzip和deflate压缩，否则用其他压缩算法，但是不用brotli算法
* 