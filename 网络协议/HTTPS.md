# HTTPS
#### 签名算法的流程
* 一般是对信息做一个 Hash 计算，得到一个 Hash 值，这个过程是不可逆的，也就是说无法通过 Hash 值得出原来的信息内容。在把信息发送出去时，把这个 Hash 值加密后，作为一个签名和信息一起发出去。
* 而是会得到一个证书，这个证书有个发布机构 CA，你只要得到这个发布机构 CA 的公钥，去解密外卖网站证书的签名，如果解密成功了，Hash 也对的上，就说明这个外卖网站的公钥没有啥问题。
#### https握手
* ![7042f5c3d9e3437d5b0b30b30f43c802](media/15428676130140/7042f5c3d9e3437d5b0b30b30f43c802.jpg)
