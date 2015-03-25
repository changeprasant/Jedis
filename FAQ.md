# Frequently Asked Questions

## If you get `java.net.SocketTimeoutException: Read timed out` exception

Try setting own `timeout` value when constructing `JedisPool` using the following constructor:
```java
JedisPool(GenericObjectPool.Config poolConfig, String host, int port, int timeout)
```

Default `timeout` value is **2 seconds**.

## JedisPool blocks after getting 8 connections

JedisPool defaults to 8 connections, you can change this in the PoolConfig:

```java
JedisPoolConfig poolConfig = new JedisPoolConfig();
poolConfig.setMaxTotal(maxTotal); // maximum active connections
poolConfig.setMaxIdle(maxIdle);  // maximum idle connections
```