# Java ssl版本配置

配置文件地址：${Java_home}/jre/lib/security/java.security

配置项：

jdk.tls.disabledAlgorithms=SSLv3, RC4, DES, MD5withRSA, DH keySize < 1024, \

  EC keySize < 224, 3DES_EDE_CBC, anon, NULL

禁用的tls算法，有些版本的JDK会带上SSLv1 SSLv1.1 导致你即使在代码中明确制定了SSLv1版本，但是实际上没有使用SSLv1算法。

