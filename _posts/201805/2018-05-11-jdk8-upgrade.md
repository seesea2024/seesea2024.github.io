
## jdk6 升级到 jdk8 注意事项

* 二进制兼容
* 源代码兼容
* 中间件（tomcat/spring）开源库版本升级
* jvm启动参数permSize->meta space
* spring3 -> spring 4
* 代码覆盖率工具 emma -> Jacoco
* 通过设置 GC 停顿的期望时间来调优(-XX:MaxGCPauseMillis)
* jersey client 1.19 -> 2.x (DK1.7以上才可以使用Jersey 2.X)
* G1 GC（适用于大内存/对延迟要求高于吞吐的场景）
