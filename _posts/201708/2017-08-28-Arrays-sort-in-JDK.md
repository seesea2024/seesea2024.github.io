---
published: true
---

[参考](http://www.cnblogs.com/endlu/p/5166066.html)

### Arrays.sort
jdk7以前用的是merge sort（长度<7时用insert sort）；jdk7以后用的是timsort（优化后的merge sort）

timsort对比较元素要求更严格，需要满足下面的语义：
1. sgn(compare(x, y)) == -sgn(compare(y, x))
2. (compare(x, y)>0) && (compare(y, z)>0) 意味着 compare(x, z)>0
3. compare(x, y)==0 意味着对于任意的z：sgn(compare(x, z))==sgn(compare(y, z)) 均成

一个破坏上面语义的例子是：

`
public int compare(Test o1, Test o2) {
   return o1.getValue() > o2.getValue() ? 1 : -1;

`

正确的比较方法是：

`
public int compare(Test o1, Test o2) {
    return o1.getValue() == o2.getValue() ? 0 : (o1.getValue() > o2.getValue() ? 1 : -1）;
}
`

### Collections.sort 
内部调用的也是Arrays.sort
