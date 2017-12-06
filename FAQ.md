# Frequently Asked Questions

## If you get `java.net.SocketTimeoutException: Read timed out` exception

Try setting own `timeout` value when constructing `JedisPool` using the following constructor:
```java
JedisPool(GenericObjectPoolConfig poolConfig, String host, int port, int timeout)
```

Default `timeout` value is **2 seconds**.

## JedisPool blocks after getting 8 connections

JedisPool defaults to 8 connections, you can change this in the PoolConfig:

```java
JedisPoolConfig poolConfig = new JedisPoolConfig();
poolConfig.setMaxTotal(maxTotal); // maximum active connections
poolConfig.setMaxIdle(maxIdle);  // maximum idle connections
```

Take into account that `JedisPool` inherits commons-pool [BaseObjectPoolConfig](https://commons.apache.org/proper/commons-pool/api-2.3/org/apache/commons/pool2/impl/BaseObjectPoolConfig.html) which has a lot of configuration parameters. 
We've set some defined ones which suit most of the cases. In case, you experience [issues](https://github.com/xetorthio/jedis/issues?utf8=%E2%9C%93&q=is%3Aissue+is%3Aopen+JedisPool) tuning these parameters may help.