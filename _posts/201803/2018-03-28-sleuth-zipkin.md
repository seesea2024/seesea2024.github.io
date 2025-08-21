sleuth & zipkin
----------
## sleuth
Spring Cloud Sleuth (org.springframework.cloud:spring-cloud-starter-sleuth), once added to the CLASSPATH, automatically instruments common communication channels:

* requests over messaging technologies like Apache Kafka or RabbitMQ (or any other Spring Cloud Stream binder
* HTTP headers received at Spring MVC controllers
* requests that pass through a Netflix Zuul microproxy
* requests made with the RestTemplate, etc.

Spring Cloud Sleuth sets up useful log formatting for you that prints the trace ID and the span ID. 

## zipkin
Data collection is a start but the goal is to understand the data, not just collect it. In order to appreciate the big picture, we need to get beyond individual events.

For this we’ll use the OpenZipkin project. OpenZipkin is the fully open-source version of Zipkin, a project that originated at Twitter in 2010, and is based on the Google Dapper papers.


