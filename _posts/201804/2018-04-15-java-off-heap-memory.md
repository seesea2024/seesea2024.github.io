# Java堆外内存
----
Java可以通过设置-XX:MaxDirectMemorySize=10M控制堆外内存的大小

## NIO ByteBuffer
JDK NIO的ByteBuffer类提供了一个接口allocateDirect(int capacity)进行堆外内存的申请，底层通过unsafe.allocateMemory(size)实现

ByteBuffer.allocateDirect分配的堆外内存不需要我们手动释放，而且ByteBuffer中也没有提供手动释放的API。也即是说，使用ByteBuffer不用担心堆外内存的释放问题，除非堆内存中的ByteBuffer对象由于错误编码而出现内存泄露。

## Unsafe
Unsafe allocateMemory和freeMemory，相当于C语音中的malloc和free，必须手动释放分配的内存。

引用Unsafe的java对象是在堆内存中分配的，当该对象被垃圾回收的时候，并不会释放堆外内存。因为使用Unsafe获取的堆外内存，必须由程序显示的释放，JVM不会帮助我们做这件事情。可以覆写Object.finalize()，当堆中的对象即将被垃圾回收器释放的时候，会调用该对象的finalize。
