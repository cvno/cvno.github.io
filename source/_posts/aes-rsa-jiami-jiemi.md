---
title: 加密解密-[AES-RSA]
date: 2018-01-19 11:17:44
tags:
categories:
keywords:
description: 通用数据加密
---

# RSA
加密数据长度是有限制的

```python
import rsa
import base64
 
 
# ######### 1. 生成公钥私钥 #########
pub_key_obj, priv_key_obj = rsa.newkeys(256)
 
pub_key_str = pub_key_obj.save_pkcs1()
pub_key_code = base64.standard_b64encode(pub_key_str)
 
priv_key_str = priv_key_obj.save_pkcs1()
priv_key_code = base64.standard_b64encode(priv_key_str)
 
print(pub_key_code)
print(priv_key_code)
 
# ######### 2. 加密 #########
def encrypt(value):
    key_str = base64.standard_b64decode(pub_key_code)
    pk = rsa.PublicKey.load_pkcs1(key_str)
    val = rsa.encrypt(value.encode('utf-8'), pk)
    return val
 
 
# ######### 3. 解密 #########
def decrypt(value):
    key_str = base64.standard_b64decode(priv_key_code)
    pk = rsa.PrivateKey.load_pkcs1(key_str)
    val = rsa.decrypt(value, pk)
    return val
 
 
# ######### 基本使用 #########
if __name__ == '__main__':
    v = 'wupeiqi'
    v1 = encrypt(v)
    print(v1)
    v2 = decrypt(v1)
    print(v2)
```

## 参考资料
- https://www.cnblogs.com/52python/p/6589869.html


# AES
加密数据长度无限制

```sh
#3.6安装  pip3 install pycryptodome
#mac     pip3 install pycrypto
```


```python
############################### 加密 ##############################
 
from Crypto.Cipher import AES
def encrypt(message):
    key = b'dfdsdfsasdfdsdfs'  # key必须是16的整数倍
    cipher = AES.new(key, AES.MODE_CBC, key)  # 创建对象
    # ----------------------------------------------
    # 先转成字节,把数据拼够16字节的整数倍
    ba_data = bytearray(message, encoding='utf-8')  # 把数据转成bytearray(byte的数组),bytearray只能追加数字,默认把数字转成字节
    v1 = len(ba_data)
    v2 = v1 % 16
    if v2 == 0: # 保证收据长度是 16 的时候数据还是加密的
        v3 = 16
    else:
        v3 = 16 - v2  # v3是追加的长度
    for i in range(v3):
        ba_data.append(v3)  # bytearray只能追加数字,默认把数字转成字节
    final_data = ba_data.decode('utf-8')
    # ----------------------------------------------
    msg = cipher.encrypt(final_data)  # 要加密的字符串，必须是16个字节或16个字节的倍数,加密后是byte格式
    return msg


############################### 解密 ##############################
def decrypt(msg):
    key = b'dfdsdfsasdfdsdfs'
    cipher = AES.new(key, AES.MODE_CBC, key)
    result = cipher.decrypt(msg)  # 把加密后的字节解密成不加密的字节
    data = result[0:-result[-1]]
    return str(data, encoding='utf-8')
```

## 安装
- https://github.com/sfbahr/PyCrypto-Wheels

