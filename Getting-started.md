# Getting started

# Installing Jedis
In order to have Jedis as a dependency in your application you can:
## Use the jar files
download the [jedis jar](https://github.com/xetorthio/jedis/archives/master) and the [Apache Commons dependency](http://commons.apache.org/pool/download_pool.cgi)

## build from source 
This gives you the most recent version. 
### Clone the github project. 
That is very easy, on the commandline you just need to:
```
    git clone git://github.com/xetorthio/jedis.git
```
### build
Before you package it using maven, you have to pass the tests. For that to succeed, you need to have two Redis server instances running. Simply use the two Redis conf files in the conf directory with your up-to-date redis build. On two separate command lines, just run:
```
redis-server jedis/conf/redis.conf
redis-server jedis/conf/redis2.conf
```
Note: With recent changes to Redis (v2.4), you may need to edit two copies of the config file provided with Redis such that one is on the default port (6379) and the other on 6380, both with "foobared" as password.

to finally build, run the tests and package, run
```
    mvn package
```
 
### Configure a Maven dependency
Jedis is also distributed as a Maven Dependency through Sonatype. To configure that just add the following XML snippet to your pom.xml file.

```xml
<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
    <version>2.0.0</version>
    <type>jar</type>
    <scope>compile</scope>
</dependency>
```
## Basic usage example - using Jedis in a multithreaded environment
You shouldn't use the same instance from different threads because you'll have strange errors. And sometimes creating lots of Jedis instances is not good enough because it means lots of sockets and connections, which leads to strange errors as well. A single Jedis instance is not threadsafe!
To avoid these problems, you should use JedisPool, which is a threadsafe pool of network connections. You can use the pool to reliably create several Jedis instances, given you return the Jedis instance to the pool when done. This way you can overcome those strange errors and achieve great performance.

To use it, init a pool:
```java
JedisPool pool = new JedisPool(new JedisPoolConfig(), "localhost");
```

You can store the pool somewhere statically, it is thread-safe. 

JedisPoolConfig includes a number of helpful Redis-specific connection pooling defaults. For example, Jedis with JedisPoolConfig will close a connection after 300 seconds if it has not been returned.

You use it by:

```java
Jedis jedis = pool.getResource();
try {
  /// ... do stuff here ... for example
  jedis.set("foo", "bar");
  String foobar = jedis.get("foo");
  jedis.zadd("sose", 0, "car"); jedis.zadd("sose", 0, "bike"); 
  Set<String> sose = jedis.zrange("sose", 0, -1);
} finally {
  /// ... it's important to return the Jedis instance to the pool once you've finished using it
  pool.returnResource(jedis);
}
/// ... when closing your application:
pool.destroy();
```
