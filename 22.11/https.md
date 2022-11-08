# HTTPS

## 参考资料

- [数字证书原理](http://www.cnblogs.com/JeffreySun/archive/2010/06/24/1627247.html)
- [为什么我们需要数字证书](https://xiehongfeng100.github.io/2016/08/27/security-why-do-we-need-digital-certificate/#:~:text=数字证书是由数字,才是可信的。)

## 基本概念

为了解决http的数据传输安全问题，推出了**Hypertext Transfer Protocol Secure** (**HTTPS**) ，协议使用了 [Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security) (TLS)来加密传输数据，又可以称为**HTTP over TLS**

## 过程

1. client->server: 你好

2. server->client: 你好, 这是我的数字证书

3. client->server: 向我证明你是服务器，这是一串随机数

4. server->client: 用私钥加密{一串随机数}

5. client->server: 我们用这个密钥和算法来做后续通讯，加密{一串随机数}

6. server->client: ok

   > Q：第三步黑客可以发送一条有规律的随机数，可能可以猜到私钥是什么
   >
   > A：服务器加密随机数的hash值，从而让被加密数没有规律
   >
   > Q：客户可以重放这些通讯
   >
   > A：给每条通讯加上唯一值，如果服务端接受到多次，则中断连接
   >
   > Q：黑客可以篡改密文来破坏正常通讯
   >
   > A：加密明文+明文hash值，接收方解密后再检验下数据是否被篡改

## QA

### 为什么用对称加密来加密https数据流

非对称加密无法保证服务端到客户端消息的保密性

### 客户端如何验证数字证书

用CA机构的公钥来解密证书中的签名，解密后的内容与证书内容保持一致，则证明该证书是合法的。

所以要保证CA机构的权威性，CA机构的公钥内置在操作系统中了。

