---
published: true
---
## AES 加密

```java

/** 
 * 加密 
 *  
 * @param content 需要加密的内容 
 * @param password  加密密码 
 * @return 
 */  
public static byte[] encrypt(String content, String password) {  
        try {             
                KeyGenerator kgen = KeyGenerator.getInstance("AES");  
                kgen.init(128, new SecureRandom(password.getBytes()));  
                SecretKey secretKey = kgen.generateKey();  
                byte[] enCodeFormat = secretKey.getEncoded();  
                SecretKeySpec key = new SecretKeySpec(enCodeFormat, "AES");  
                Cipher cipher = Cipher.getInstance("AES");// 创建密码器  
                byte[] byteContent = content.getBytes("utf-8");  
                cipher.init(Cipher.ENCRYPT_MODE, key);// 初始化  
                byte[] result = cipher.doFinal(byteContent);  
                return result; // 加密  
        } catch (NoSuchAlgorithmException e) {  
                e.printStackTrace();  
        } catch (NoSuchPaddingException e) {  
                e.printStackTrace();  
        } catch (InvalidKeyException e) {  
                e.printStackTrace();  
        } catch (UnsupportedEncodingException e) {  
                e.printStackTrace();  
        } catch (IllegalBlockSizeException e) {  
                e.printStackTrace();  
        } catch (BadPaddingException e) {  
                e.printStackTrace();  
        }  
        return null;  
} 
```

## AES解密

```java
/**解密 
 * @param content  待解密内容 
 * @param password 解密密钥 
 * @return 
 */  
public static byte[] decrypt(byte[] content, String password) {  
        try {  
                 KeyGenerator kgen = KeyGenerator.getInstance("AES");  
                 kgen.init(128, new SecureRandom(password.getBytes()));  
                 SecretKey secretKey = kgen.generateKey();  
                 byte[] enCodeFormat = secretKey.getEncoded();  
                 SecretKeySpec key = new SecretKeySpec(enCodeFormat, "AES");              
                 Cipher cipher = Cipher.getInstance("AES");// 创建密码器  
                cipher.init(Cipher.DECRYPT_MODE, key);// 初始化  
                byte[] result = cipher.doFinal(content);  
                return result; // 加密  
        } catch (NoSuchAlgorithmException e) {  
                e.printStackTrace();  
        } catch (NoSuchPaddingException e) {  
                e.printStackTrace();  
        } catch (InvalidKeyException e) {  
                e.printStackTrace();  
        } catch (IllegalBlockSizeException e) {  
                e.printStackTrace();  
        } catch (BadPaddingException e) {  
                e.printStackTrace();  
        }  
        return null;  
}  

```

## 分析

该加解密算法**SecureRandom**存在坑。在不同平台window／linux，不同jdk 6/7/8， 不同系统配置时多台分布式服务器参加加解密时可能会出现：在一台服务加密的文件在另外一台服务器上不能解密。

原因跟SecureRandom实现的PRNG随机算法有关。PRNG是Java加密服务提供商（CSP）的一部分。在Sun的Java实现中，默认情况下使用SUN CSP。在Windows上，SUN CSP默认使用在sun.security.provider.SecureRandom中实现的**SHA1PRNG**。在Solaris和Linux上，SUN CSP默认是使用sun.security.provider.**NativePRNG**，它只提供操作系统提供的/dev/urandomPRNG 的输出。

经测试，指定SecureRandom算法SHA1PRNG没有问题。在我们环境（linux／jdk6/8）中没有显式指定默认走NativePRNG。SecureRandom默认算法选择逻辑：

```java
     static void More ...putEntries(Map<Object, Object> map) {
86 
87         /*
88          * SecureRandom
89          *
90          * Register these first to speed up "new SecureRandom()",
91          * which iterates through the list of algorithms
92          */
93         // register the native PRNG, if available
94         // if user selected /dev/urandom, we put it before SHA1PRNG,
95         // otherwise after it
96         boolean nativeAvailable = NativePRNG.isAvailable();
97         boolean useNativePRNG = seedSource.equals(URL_DEV_URANDOM) ||
98             seedSource.equals(URL_DEV_RANDOM);
99 
100        if (nativeAvailable && useNativePRNG) {
101            map.put("SecureRandom.NativePRNG",
102                "sun.security.provider.NativePRNG");
103        }
104        map.put("SecureRandom.SHA1PRNG",
105             "sun.security.provider.SecureRandom");
106        if (nativeAvailable && !useNativePRNG) {
107            map.put("SecureRandom.NativePRNG",
108                "sun.security.provider.NativePRNG");
109        }
  
  		  ......

```
从上面可以看出，linux下指定了/dev/urandom或者/dev/random采用优先级高的NativePRNG。否则SHA1PRNG优先级高。

至于通过-Djava.security.egd=file:/dev/./urandom 而不是 **file:/dev/urandom** 那又是另外一个忧伤的故事。<http://blog.csdn.net/raintungli/article/details/42876073>
