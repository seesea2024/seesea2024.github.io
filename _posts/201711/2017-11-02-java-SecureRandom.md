## Java 随机数生成

在Linux操作系统中，有一个特殊的设备文件，可以用作随机数发生器或伪随机数发生器。

/dev/random

在读取时，/dev/random设备会返回小于熵池噪声总数的随机字节。/dev/random可生成高随机性的公钥或一次性密码本。若熵池空了，对/dev/random的读操作将会被阻塞，直到从别的设备中收集到了足够的环境噪声为止。
当然你也可以设置成不堵塞，当你在open 的时候设置参数O_NONBLOCK， 但是当你read的时候，如果熵池空了，会返回-1

/dev/urandom

/dev/random的一个副本是/dev/urandom （"unlocked"，非阻塞的随机数发生器[4]），它会重复使用熵池中的数据以产生伪随机数据。这表示对/dev/urandom的读取操作不会产生阻塞，但其输出的熵可能小于/dev/random的。它可以作为生成较低强度密码的伪随机数生成器，不建议用于生成高强度长期密码。
 
## 设置随机数生成方法

在JAVA中可以通过两种方式去设置指定的随机数发生器
1.      -Djava.security.egd=file:/dev/random或者 -Djava.security.egd=file:/dev/urandom
2.      修改配置文件java.security 在jvm_home\jre\lib\security
参数securerandom.source=file:/dev/urandom

哪怕设置了-Djava.security.egd=file:/dev/urandom,最后的结果一样是读取file:/dev/random

```java
if (egdSource.equals(URL_DEV_RANDOM) || egdSource.equals(URL_DEV_URANDOM)){ //走此分支都读/dev/random文件   
            try {    
                instance = new NativeSeedGenerator();    
                if (debug != null) {    
                  debug.println("the instance:"+instance.getClass());    
                    debug.println("Using operating system seed generator");    
                }    
            } catch (IOException e) {    
                if (debug != null) {    
                    debug.println("Failed to use operating system seed "    
                                  + "generator: "+ e.toString());    
                }    
            }    
        }    
else if (egdSource.length() != 0) {    // 读指定的file:/dev/./urandom 或者 file:/dev/../dev/urandom文件
            try {    
                instance = new URLSeedGenerator(egdSource);    
                if (debug != null) {    
                    debug.println("Using URL seed generator reading from "    
                                  + egdSource);    
                }    
            } catch (IOException e) {    
                if (debug != null)    
                    debug.println("Failed to create seed generator with "    
                                  + egdSource + ": " + e.toString());    
            }    
        }    

```
因此要这样设置：`-Djava.security.egd=file:/dev/./urandom`
