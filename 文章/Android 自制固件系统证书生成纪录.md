Android 自制固件系统证书生成纪录

固件工程师提供了 `pk8` `.x509.pem` 文件 ，还有一个jar包，提供了说明

```
将 key.pk8 和 certificate.pem 格式的系统签名转换为 mykey.keystore 格式
需要系统中有openssl 和 jdk，windows 版openssl 可以在http://slproweb.com/products/Win32OpenSSL.html下载

1-
openssl pkcs8 -inform DER -nocrypt -in platform.pk8 -out key.pem
2-
openssl pkcs12 -export -in platform.x509.pem -inkey key.pem -out platform.p12 -password pass:android -name platform
3-
keytool -importkeystore -alias platform -deststorepass android -destkeystore  keys/platform.keystore -srckeystore platform.p12 -srcstoretype  PKCS12 -srcstorepass android

核对
keytool -list -v -keystore mykey.keystore

第一步使用key.pk8生成了key.pem 文件
第二步使用certificate.pem 和key.pem 生成了platform.p12 文件，其中签名的名字是mykey，密码是android
第三步使用platform.p12 生成了mykey.keystore 文件，keystore密码是password
```

我可以在Centos+Java环境中生成系统证书了。

先说曲折的过程，偷懒想直接在现有云服务器装java 之后制作证书，制作出来之后，选择别名时报错，换了java版本之后仍然未解决，折腾几个小时后，决定使用虚拟机来制作。

虚拟机：vmware16 16.2.1 build-18811642

系统： centos8   [磁力链接](magnet:?xt=urn:btih:9DB46A612D04763AA7DB02A0FF63EDE2EA555867&dn=CentOS-8.1.1911-x86_64-dvd1.iso )

Java ：[18](https://download.oracle.com/otn/java/jdk/8u321-b07/df5ad55fdd604472a86a45a217032c7d/jdk-8u321-linux-x64.tar.gz?AuthParam=1644457951_0279349b96237fce58c997453270a78ahttps://download.oracle.com/otn/java/jdk/8u321-b07/df5ad55fdd604472a86a45a217032c7d/jdk-8u321-linux-x64.tar.gz?AuthParam=1644457951_0279349b96237fce58c997453270a78a)

centos [安装Java]([CentOS 7 安装 JAVA环境（JDK 1.8） - 晓晨Master - 博客园](https://www.cnblogs.com/stulzq/p/9286878.html))

安装虚拟机，虚拟机部署centos这个我就不详细说了 ，百度一大堆，甚至不用百度；

装上之后，在物理主机（也就是安装vmware的机器）安装一个ftp客户端（我用的filezilla），通过sftp方式连上，把证书所用的文件 和 Java解压后的文件传到虚拟机中

安装java，安装java的教程，前面附链接了 。

最后执行相关的命令即可。







· 关于安装filezilla，原本我是想在这个虚拟的centos安装[宝塔运维](宝塔服务器面板，一键全能部署及管理，送你3188元礼包，点我领取https://www.bt.cn/?invite_code=MV9lanhpemE=)的，我装的时候遇到了报错，跟前几天部署一台华为云的centos8的宝塔报错一样，华为云的技术说想装宝塔最好用他们的centos7，我一合计，也别再重新安装一个centos7虚拟机了，我只是想传个文件，还是用ftp吧，哈哈。
