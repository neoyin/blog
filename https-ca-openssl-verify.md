---
title: https证书验证失败
date: 2016-10-24 16:10
Author: neoyin
categories: 技术流
tags:
 - https
 - openssl
permalink: https-ca-openssl-verify
---

---

App使用了deeplink 需要访问网站https下`apple-app-site-association`文件
https配置成功 但是客户端访问此文件却提示非安全 

利用`curl`命令返回如下:
```
curl performs SSL certificate verification by default, using a "bundle"
 of Certificate Authority (CA) public keys (CA certs). If the default
 bundle file isn't adequate, you can specify an alternate file
 using the --cacert option.
If this HTTPS server uses a certificate signed by a CA represented in
 the bundle, the certificate verification probably failed due to a
 problem with the certificate (it might be expired, or the name might
 not match the domain name in the URL).
If you'd like to turn off curl's verification of the certificate, use
 the -k (or --insecure) option.
```

一直找不到原因
利用`openssl s_client -connect www.domain.com:443 -bugs`
如下:
```
---
SSL-Session:
    Protocol  : TLSv1
    Cipher    : AES128-SHA
    Session-ID: B5016090D3291093F6DAC2D83F0E511C0E9656039C2DAF4A35C84B69F942961A
    Session-ID-ctx:
    Master-Key: 177D215DA16D9D73B449B400F7526E8522CC1E4EA41D65AE819F1AD25FF922896330D26A2996FF3C93F6DF563A02E6A2
    Key-Arg   : None
    Start Time: 1477389543
    Timeout   : 300 (sec)
    Verify return code: 21 (unable to verify the first certificate)
```

##### 参考
- <http://serverfault.com/questions/509158/unable-to-locally-verify-the-issuers-authority-for-geotrust-ssl-ca>
- <http://www.blogjava.net/ycyk168/archive/2009/11/27/303946.html>
- <http://www.doxer.org/resolved-unable-to-locally-verify-the-issuers-authority/>
- <https://www.virtualmin.com/node/41060>





