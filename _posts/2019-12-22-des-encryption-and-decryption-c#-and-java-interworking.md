---
layout: post
title: DES 加密解密C#和JAVA 互通
date: 2019-12-22
Author: Zhu Kun
tags: [Encryption]
toc: true
comments: true
---

遇到的问题如下：

主要就是C#和JAVA中的DES不一致的问题。

1. 是C#采用CBC Mode，PKCS7 Padding, Java采用CBC Mode，PKCS5Padding Padding,

2. 是C#采用ECB Mode，PKCS7 Padding, Java采用ECB Mode，PKCS5Padding Padding,  

   C#的ECB有IV向量，JAVA中的ECB不支持向量。

 

在实际解决中我采用了DES/CBC/PKCS5Padding的方式，把C#的IV向量和Key公用同一个加密字符串。**在C#和JAVA中都转为16进制**的加密串，解决匹配不上的问题。最后封一层base64。

C#：

```c#
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Security.Cryptography;
using System.Text;
using System.Threading.Tasks;
namespace BiHu.BaoXian.Service.Database.Extension
{
    /** DES加密解密
     * @author  liucx
     * @description  DES+base64加密解密
     */
    public static class EncodHelper
    {
        #region DESEnCode DES加密
        public static string DESEnCode(string pToEncrypt, string sKey)
        {
            DESCryptoServiceProvider des = new DESCryptoServiceProvider();
            byte[] inputByteArray = Encoding.GetEncoding("UTF-8").GetBytes(pToEncrypt);
						//建立加密对象的密钥和偏移量
            //原文使用ASCIIEncoding.ASCII方法的GetBytes方法
            //使得输入密码必须输入英文文本
            des.Key = ASCIIEncoding.ASCII.GetBytes(sKey);
            des.IV = ASCIIEncoding.ASCII.GetBytes(sKey);
            MemoryStream ms = new MemoryStream();
            CryptoStream cs = new CryptoStream(ms, des.CreateEncryptor(), CryptoStreamMode.Write);
						cs.Write(inputByteArray, 0, inputByteArray.Length);
            cs.FlushFinalBlock();
						StringBuilder ret = new StringBuilder();
            //16进制
            foreach (byte b in ms.ToArray())
            {
                ret.AppendFormat("{0:X2}", b);
            }
						//完成加密后再转一次base64
            byte[] bytes = Encoding.UTF8.GetBytes(ret.ToString());
            var result = Convert.ToBase64String(bytes);
            return result;
        }
        #endregion
				#region DESDeCode DES解密
        public static string DESDeCode(string code, string sKey)
        {
            //解密先转码base64
            var pToDecrypt = Encoding.UTF8.GetString(Convert.FromBase64String(code));
            DESCryptoServiceProvider des = new DESCryptoServiceProvider();
						byte[] inputByteArray = new byte[pToDecrypt.Length / 2];
            for (int x = 0; x < pToDecrypt.Length / 2; x++)
            {
                int i = (Convert.ToInt32(pToDecrypt.Substring(x * 2, 2), 16));
                inputByteArray[x] = (byte)i;
            }
						des.Key = ASCIIEncoding.ASCII.GetBytes(sKey);
            des.IV = ASCIIEncoding.ASCII.GetBytes(sKey);
            MemoryStream ms = new MemoryStream();
            CryptoStream cs = new CryptoStream(ms, des.CreateDecryptor(), CryptoStreamMode.Write);
            cs.Write(inputByteArray, 0, inputByteArray.Length);
            cs.FlushFinalBlock();
						StringBuilder ret = new StringBuilder();
						return System.Text.Encoding.UTF8.GetString(ms.ToArray());
        }
        #endregion
    }
}
```

JAVA：

```java
package com.iflytek.test;

import javax.crypto.Cipher;
import javax.crypto.SecretKey;
import javax.crypto.SecretKeyFactory;
import javax.crypto.spec.DESKeySpec;

import javax.crypto.spec.IvParameterSpec;
import org.apache.commons.codec.binary.Base64;

public class DESUtils {
    /**
     * 密钥
     */
    public static final String DEFAULT_KEY = "liucx2018";

    /**
     * 加密
     *
     * @param message
     * @param key
     * @return
     */
    public static byte[] encrypt(String message, String key) throws Exception {
        Cipher cipher = Cipher.getInstance("DES/CBC/PKCS5Padding");

        DESKeySpec desKeySpec = new DESKeySpec(key.getBytes("UTF-8"));

        SecretKeyFactory keyFactory = SecretKeyFactory.getInstance("DES");
        SecretKey secretKey = keyFactory.generateSecret(desKeySpec);
        IvParameterSpec iv = new IvParameterSpec(key.getBytes("UTF-8"));
        cipher.init(Cipher.ENCRYPT_MODE, secretKey, iv);

        return cipher.doFinal(message.getBytes("UTF-8"));
    }

    /**
     * 加密，方式为先DES加密， 再base64转码的字符串
     * @param message
     * @param key
     * @return
     */
    public static String encrypt_base64(String message, String key) throws Exception {
        String a = toHexString(encrypt(message, key)).toUpperCase();
        String base64message =  new String(Base64.encodeBase64(a.getBytes("UTF-8")));
        return base64message;
    }

    /**
     * 解密
     *
     * @param message
     *            加密后的内容
     * @param key
     *            密钥
     * @return
     */
    public static String decrypt(String message, String key) throws Exception {

        byte[] bytesrc = convertHexString(message);
        Cipher cipher = Cipher.getInstance("DES/CBC/PKCS5Padding");
        DESKeySpec desKeySpec = new DESKeySpec(key.getBytes("UTF-8"));
        SecretKeyFactory keyFactory = SecretKeyFactory.getInstance("DES");
        SecretKey secretKey = keyFactory.generateSecret(desKeySpec);
        IvParameterSpec iv = new IvParameterSpec(key.getBytes("UTF-8"));

        cipher.init(Cipher.DECRYPT_MODE, secretKey, iv);

        byte[] retByte = cipher.doFinal(bytesrc);
        return new String(retByte, "UTF-8");
    }




    /**
     * 解密，方式为先解码base64，再解密，转成正常字符串
     *
     * @param base64message base64编码的密文
     * @param key     密钥
     * @return        解密后的文本
     */
    public static String decrypt_base64(String base64message, String key) throws Exception {
        String message = new String(Base64.decodeBase64(base64message.getBytes("UTF-8")));
        String result = decrypt(message, key);
        return result;
    }
    /**
     * 转成16进制
     *
     * @param b
     * @return
     */
    public static String toHexString(byte b[]) {
        StringBuffer hexString = new StringBuffer();
        for (int i = 0; i < b.length; i++) {
            String plainText = Integer.toHexString(0xff & b[i]);
            if (plainText.length() < 2)
                plainText = "0" + plainText;
            hexString.append(plainText);
        }

        return hexString.toString();
    }

    public static byte[] convertHexString(String ss) {
        byte digest[] = new byte[ss.length() / 2];
        for (int i = 0; i < digest.length; i++) {
            String byteString = ss.substring(2 * i, 2 * i + 2);
            int byteValue = Integer.parseInt(byteString, 16);
            digest[i] = (byte) byteValue;
        }

        return digest;
    }
}
```

**Android Studio** **需添加依赖库**：implementation'apache-codec:commons-codec:1.2'

