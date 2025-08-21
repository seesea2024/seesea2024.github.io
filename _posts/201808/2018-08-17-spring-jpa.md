---
layout: post
title: spring JPA笔记
categories: [JPA]
---
Spring data JPA 笔记

# JPA开发笔记

## 属性注解和ManyToOne关系注解冲突

下面代码中的studentId和student会冲突，造成应用不能启动

```java
    private Long studentId;
    private Student student;

    @ManyToOne(cascade=CascadeType.ALL)
    @JoinColumn(name = "student_id", referencedColumnName = "id")
    public Student getStudent() {
        return student;
    }
```

## 怎么启用Audit自动更新updatedAt

```java
@SpringBootApplication
@EnableJpaAuditing // 这里，启用审计
class Application { ...... }
```

```java
@Entity
@EntityListeners(AuditingEntityListener.class)
public class SomeEntity {
    @Id
    private long id;
    @CreatedDate // 创建的时间
    private Date createTime;
    @LastModifiedDate // 最后修改的时间
    private Date updateTime;
    ......
}

```

## findBy查询字段解析规则

框架在进行方法名解析时，会先把方法名多余的前缀截取掉，比如find、findBy、read、readBy、get、getBy，然后对剩下部分进行解析。并且如果方法的最后一个参数是Sort或者Pageable类型，也会提取相关的信息，以便按规则进行排序或者分页查询。

在创建查询时，我们通过在方法名中使用属性名称来表达，比如findByUserAddressZip()。框架在解析该方法时，首先剔除findBy，然后对剩下的属性进行解析，详细规则如下（此处假设该方法针对的域对象为 AccountInfo 类型）：

先判断 userAddressZip （根据POJO规范，首字母变为小写，下同）是否为AccountInfo的一个属性，如果是，则表示根据该属性进行查询；如果没有该属性，继续第二步；

从右往左截取第一个大写字母开头的字符串（此处为Zip），然后检查剩下的字符串是否为AccountInfo的一个属性，如果是，则表示根据该属性进行查询；如果没有该属性，则重复第二步，继续从右往左截取；最后假设 user 为 AccountInfo 的一个属性；

接着处理剩下部分（ AddressZip），先判断user所对应的类型是否有addressZip属性，如果有，则表示该方法最终是根据 "AccountInfo.user.addressZip" 的取值进行查询；否则继续按照步骤 2 的规则从右往左截取，最终表示根据 "AccountInfo.user.address.zip" 的值进行查询。

可能会存在一种特殊情况，比如 AccountInfo 包含一个 user 的属性，也有一个 userAddress 属性，此时会存在混淆。读者可以明确在属性之间加上 "_" 以显式表达意图，比如 "findByUser_AddressZip()" 或者 "findByUserAddress_Zip()"。