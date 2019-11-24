# HTTP优化
#### 消耗
* 产生用于密钥交换的临时公私钥对（ECDHE）；
* 验证证书时访问 CA 获取 CRL 或者 OCSP；
* 非对称加密解密处理“Pre-Master”。

#### 硬件优化
* CPU内建AES优化
* SSL加速卡 Tengine
* SSl加速服务器

#### 软件优化
* 协议优化
* 证书优化 证书传输和验证

#### False start
* False Start 有抢跑的意思，意味着不按规则行事。TLS False Start 是指客户端在发送 Change Cipher Spec Finished 同时发送应用数据（如 HTTP 请求），服务端在 TLS 握手完成时直接返回应用数据（如 HTTP 响应）。这样，应用数据的发送实际上并未等到握手全部完成，故谓之抢跑。这个过程如下图所示：

#### 会话复用
* 第一种叫“Session ID”，就是客户端和服务器首次连接后各自保存一个会话的 ID 号，内存里存储主密钥和其他相关的信
* session ticket 存储的责任由服务器转移到了客户端，服务器加密会话信息，用“New Session Ticket”消息发给客户端，让客户端保存
* PSK 发送 Ticket 的同时会带上应用数据（Early Data），免去了 1.2 里的服务器确认步骤，这种方式叫“Pre-shared Key”，简称为“PSK”

