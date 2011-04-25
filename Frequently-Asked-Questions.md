# Frequently Asked Questions

## If you get `java.net.SocketTimeoutException: Read timed out` exception

Try setting own `timeout` value when constructing `JedisPool` using the following constructor:
```java
JedisPool(GenericObjectPool.Config poolConfig, String host, int port, int timeout)
```

Default `timeout` value is **2 seconds**.