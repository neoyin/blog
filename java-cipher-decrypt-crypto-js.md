---
title: 使用Java-Cipher解密js-crypto
date: 2021-10-23 17:10
Author: neoyin
categories: 技术流
tags:
 - crypto-js
 - java-cipher
permalink: java-cipher-decrypt-crypto-js

---



开发用有时前后端会对一些信息进行加解密，如一些API需要对一些敏感信息做加密，html端使用`crypto-js`做解密如下：

```js
// JS 使用 crypto-js 对秘文解密
var decrypted = CryptoJS.AES.decrypt(CryptoJS.enc.Base64.stringify(CryptoJS.enc.Hex.parse(string)),
            CryptoJS.enc.Utf8.parse(kkk), {
                mode: CryptoJS.mode.CBC,
                padding: CryptoJS.pad.Pkcs7,
                iv: CryptoJS.enc.Utf8.parse(vvv),
            });
return decrypted.toString(CryptoJS.enc.Utf8).toString()
```

使用JAVA代码实现如下:

```java
    /**
     * AES解密
     * @param encryptStr 密文
     * @param decryptKey 秘钥，必须为16个字符组成
     * @return 明文
     * @throws Exception
     */
    public static String aesDecrypt(String encryptStr, String decryptKey,String vectorKey) throws Exception {
        if (StringUtils.isEmpty(encryptStr) || StringUtils.isEmpty(decryptKey)) {
            return null;
        }
        byte[] bytes = Hex.decodeHex(encryptStr.toCharArray());
        SecretKey secretKey = new SecretKeySpec(decryptKey.getBytes(), "AES");
        IvParameterSpec iv = new IvParameterSpec(vectorKey.getBytes());
        Cipher cipher = Cipher.getInstance("AES/CBC/PKCS5Padding");
        cipher.init(Cipher.DECRYPT_MODE, secretKey, iv);
        byte[] decryptBytes = cipher.doFinal(bytes);
        return new String(decryptBytes);
    }
```

##### Cipher类提供了加密和解密的功能。

该项目使用Cipher类完成aes，des，des3和rsa加密.
> 获取Cipher类的对象：`Cipher cipher = Cipher.getInstance("DES/CBC/PKCS5Padding");` 参数按"算法/模式/填充模式"，有以下的参数

- AES/CBC/NoPadding (128)
- AES/CBC/PKCS5Padding (128)
- AES/ECB/NoPadding (128)
- AES/ECB/PKCS5Padding (128)
- DES/CBC/NoPadding (56)
- DES/CBC/PKCS5Padding (56)
- DES/ECB/NoPadding (56)
- DES/ECB/PKCS5Padding (56)
- DESede/CBC/NoPadding (168)
- DESede/CBC/PKCS5Padding (168)
- DESede/ECB/NoPadding (168)
- DESede/ECB/PKCS5Padding (168)
- RSA/ECB/PKCS1Padding (1024, 2048)
- RSA/ECB/OAEPWithSHA-1AndMGF1Padding (1024, 2048)
- RSA/ECB/OAEPWithSHA-256AndMGF1Padding (1024, 2048)
1. 加密算法有：AES，DES，DESede(DES3)和RSA 四种
2. 模式有CBC(有向量模式)和ECB(无向量模式)，向量模式可以简单理解为偏移量，使用CBC模式需要定义一个IvParameterSpec对象
3. 填充模式:
- NoPadding: 加密内容不足8位用0补足8位, Cipher类不提供补位功能，需自己实现代码给加密内容添加0, 如{65,65,65,0,0,0,0,0}
- PKCS5Padding: 加密内容不足8位用余位数补足8位, 如{65,65,65,5,5,5,5,5}或{97,97,97,97,97,97,2,2}; 刚好8位补8位8

##### Cipher对象需要初始化

`init(int opmode, Key key, AlgorithmParameterSpec params)`
1. opmode ：Cipher.ENCRYPT_MODE(加密模式)和 Cipher.DECRYPT_MODE(解密模式)
2. key ：密匙，使用传入的盐构造出一个密匙，可以使用SecretKeySpec、KeyGenerator和KeyPairGenerator创建密匙，其中
- SecretKeySpec和KeyGenerator支持AES，DES，DESede三种加密算法创建密匙
- KeyPairGenerator支持RSA加密算法创建密匙
3. params ：使用CBC模式时必须传入该参数，该项目使用IvParameterSpec创建iv 对象



##### 参考

1. <https://www.cnblogs.com/caizhaokai/p/10944667.html>
