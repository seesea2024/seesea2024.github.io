---
layout: post
title: prometheus quantile 
categories: [prometheus quantile 分位数]
---
## histogram

```
Imagine that you create a histogram with 5 buckets with values: 0.5, 1, 2, 3, 5. Let’s call this histogram http_request_duration_seconds and 3 requests come in with durations 1s, 2s, 3s. Then you would see that /metrics endpoint contains:

# HELP http_request_duration_seconds request duration histogram
# TYPE http_request_duration_seconds histogram
http_request_duration_seconds_bucket{le="0.5"} 0
http_request_duration_seconds_bucket{le="1"} 1
http_request_duration_seconds_bucket{le="2"} 2
http_request_duration_seconds_bucket{le="3"} 3
http_request_duration_seconds_bucket{le="5"} 3
http_request_duration_seconds_bucket{le="+Inf"} 3
http_request_duration_seconds_sum 6
http_request_duration_seconds_count 3

Here we can see that:
sum is 1s + 2s + 3s = 6,
count is 3, because of 3 requests
bucket {le=”0.5″}  is 0, because none of the requests where <= 0.5 seconds

# average
http_request_duration_seconds_sum / http_request_duration_seconds_count

# quantile, Which results in 1.5.
histogram_quantile(0.5, rate(http_request_duration_seconds_bucket[10m])

```
* 注意这里算出来的quantile跟下面的并不一致（不是2），解释是这里是*近似值*。参考：https://povilasv.me/prometheus-tracking-request-duration/

## summary

```
http_request_duration_seconds{quantile="0.5"} 2
http_request_duration_seconds{quantile="0.9"} 3
http_request_duration_seconds{quantile="0.99"} 3
http_request_duration_seconds_sum 6
http_request_duration_seconds_count 3
So we can see that:

sum is 1s + 2s + 3s = 6,

count is 3, because of 3 requests.

{quantile=”0.5″} is 2, meaning 50th percentile is 2.

```



## 分位数 - quantile

分位数（quantile）：把一组按照升序排列的数据分割成n个等份区间并产生n-1个等分点后每个等分点所对应的数据。按照升序排列称作第一至第n-1的n分位数。（注：如果等分点在其左右两个数据的中间，那么该等分点所对应的数就是其左右两数的平均数）确定等分位点位置的其中一种常用公式：
```
（n+1）*p/q 
```
其中n表示一共有多少数据，p表示第几分位，q表示是几分位数。

```
例如四分位数（quartile；4-quantile）
1，1，3，6，7，12，14，17，25，28，29
上述数据集中共有11个数据。将这组已排列好的数据等分成4等份后产生3个等分点。
其中第一四分位数Q1=（11+1）*（1/4）=3，对应的数据为3
第二四分位数（即中位数）Q2=（11+1）*（2/4）=6，对应的数据为12
第三四分位数Q3=（11+1）*（3/4）=9，对应的数据为25

以此类推百分位数（percentile）就是把数据等分成100等份后所获得的数。根据分数性质，Q1就是25%百分位数（左右数据数量1:3），Q2中位数就是50%（1:1），Q3就是75%（3:1）
```