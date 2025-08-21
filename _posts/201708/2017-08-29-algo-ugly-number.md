---
published: true
---
丑数：只能因式分解为2，3，5整除的数

算法：求第index个丑数

## 常规思维
依次遍历，判断每个数是否为丑数。确定是效率低，存在大量重复计算。

```java

public boolean Ugly(int N) {

	//判断N是否只含有质因子2,3,5中的几个
	while(N%2==0)//判断是否有质因子2
		N/=2;//如果N中含有质因子2，把所有的因子2都去掉
        
	while(N%3==0)//执行完上一个while,判断是否有质因子3
		N/=3;//如果N中含有质因子3，把所有的因子3都去掉
        
	while(N%5==0)//执行完上两个while,判断是否有质因子5
		N/=5;//如果N中含有质因子5，把所有的因子5都去掉
        
	return N==1?true:false;//如果最后N除的只剩1，则N就是丑数
}
```

## 优化方法
[参考](https://www.nowcoder.com/questionTerminal/6aa9e04fc3794f68acf8778237ba065b)
利用前面计算的丑数序列，* 2，* 3，* 5取最小值为下一个丑数。

```java

import java.util.*;
 
public class Solution {
    // 求第index个丑数
    public int GetUglyNumber_Solution(int index) {
        if(index<=0)
            return 0;
        ArrayList<Integer> list = new ArrayList<Integer>();
        //add进第一个丑数1
        list.add(1);
        //三个下标用于记录丑数的位置
        int i2=0,i3=0,i5=0;
        while(list.size()<index){
            //三个数都是可能的丑数，取最小的放进丑数数组里面
            int n2=list.get(i2)*2;
            int n3=list.get(i3)*3;
            int n5=list.get(i5)*5;
            int min = Math.min(n2,Math.min(n3,n5));
            list.add(min);
            if(min==n2)
                i2++;
            if(min==n3)
                i3++;
            if(min==n5)
                i5++;
        }
        return list.get(list.size()-1);
    }
}
```
