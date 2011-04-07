# Welcome to the jedis wiki!

## Getting started

check out the release notes here  [[ReleaseNotes]]

### Installing Jedis
In order to have Jedis as a dependency in your application you can:

#### Clone the github project and compile the code
That is very easy, you just need to:
```
    git clone git://github.com/xetorthio/jedis.git
```

Before you package it using maven, for the tests to succeed, you need to have two Redis server instances running. Simply use the two Redis conf files in the conf directory with your up-to-date redis build. On two separate command lines, just run:
```
redis-server jedis/conf/redis.conf
redis-server jedis/conf/redis2.conf
```
(one is on the default port (6379) and the other on 6380, both with "foobared" as password)

then finally run
```
    mvn package
```
to build, run the tests and package. 
 
#### Download the JAR from github
Just go to the Downloads section and use the latest Jedis JAR available.

#### Apache Commons Dependency
You will also need to download Apache Commons [[ http://commons.apache.org/pool/download_pool.cgi]] 
and then: 
```java
import org.apache.commons.pool.impl.GenericObjectPool.Config;
```

#### Configure a Maven dependency
Jedis is also distributed as a Maven Dependency through Sonatype. To configure that just add the following XML snippet to your pom.xml file.

    <dependency>
        <groupId>redis.clients</groupId>
        <artifactId>jedis</artifactId>
        <version>1.5.2</version>
        <type>jar</type>
        <scope>compile</scope>
    </dependency>

## Basic usage


**Warning: regular Jedis is not threadsafe**. You shouldn't use the same instance from different threads because you'll have strange errors. And sometimes creating lots of Jedis instances is not good enough because it means lots of sockets and connections, which leads to strange errors as well. 

### Using Jedis in a multithreaded environment
To avoid problems mentioned above, you should use in this cases JedisPool, which is a threadsafe pool of reusable Jedis instances. This way you can overcome those strange errors and achieve great performance.

To use it, init a pool:
```java
JedisPool pool = new JedisPool(new Config(), "localhost");
```

You can store the pool somewhere statically, it is threadsafe.

And then you use it by:

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

You should also take the time to adjust the Config() settings to your use case. By default, Jedis will close a connection after 300 seconds if it has not been returned.

## Advanced usage

###Transactions
To do transactions in jedis, you have to wrap operations in a transaction block, very similar to pipelining:

```java
jedis.watch (key1, key2, ...);
BinaryTransaction t = jedis.multi();
t.set("foo", "bar");
t.exec();
```

Note: when you have any method that returns values, you have to do like this:

```java
t.get("foo");
t.hgetAll("car");
List<Object> all = t.exec();
String result1 = SafeEncoder.encode(all.get(1)); // get the result of the first get in the transaction.
```



Note 2: From versions 1.5.3 there is much improved support for transactions. It will have the optional possibility to get specific entries directly from the transaction without dealing with positions and conversions after t.exec() :

```java
Transaction t = jedis.multi();
t.set("fool", "bar"); 
Response<String> result1 = t.get("fool");

t.zadd("foo", 1, "barowitch"); t.zadd("foo", 0, "barinsky"); t.zadd("foo", 0, "barikoviev");
Response<Set<String>> sose = t.zrange("foo", 0, -1);   // get the entire sortedset
t.exec();                                              // dont forget it

String foolbar = result1.get());                       // use Response.get() to retrieve things from a Response
int soseSize = sose.get().size();                      // on sose.get() you can directly call Set methods!

// List<Object> allResults = t.exec();    	    	// you could still get all results at once, as before
int wontwork = allResults.get(5).size();                // but this won't work to access the set. see above
```


### Pipelining

Sometimes you need to send a bunch of different commands. A very cool way to do that, and have better performance than doing it the naive way, is to use pipelining. This way you send commands without waiting for response, and you actually read the responses at the end, which is faster. 

Here is how to do it (very similar to transactions):

```java
Pipeline p = jedis.pipelined();
p.set("foo", "bar");
p.get("foo");
List<Object> results = p.exec();
String result1 = SafeEncoder.encode(results.get(1)); // get the result of the first get in the pipeline.

```

From version 1.5.3 there is a much more convenient way for creating pipelines, without the need to deal with positions and conversions after p.exec()  :

```java
Pipeline p = jedis.pipelined();
p.set("fool", "bar"); 
p.zadd("foo", 1, "barowitch");  p.zadd("foo", 0, "barinsky"); p.zadd("foo", 0, "barikoviev");
Response<String> pipeString = p.get("fool");
Response<Set<String>> sose = p.zrange("foo", 0, -1);
p.sync(); 

int soseSize = sose.get().size();
Set<String> setBack = sose.get();
```

For more explanations see code comments in the transaction section.

### Publish/Subscribe

To subscribe to a channel in Redis, create an instance of JedisPubSub and call subscribe on the Jedis instance:

```java
class MyListener extends JedisPubSub {
        public void onMessage(String channel, String message) {
        }

        public void onSubscribe(String channel, int subscribedChannels) {
        }

        public void onUnsubscribe(String channel, int subscribedChannels) {
        }

        public void onPSubscribe(String pattern, int subscribedChannels) {
        }

        public void onPUnsubscribe(String pattern, int subscribedChannels) {
        }

        public void onPMessage(String pattern, String channel,
            String message) {
        }
}

MyListener l = new MyListener();

jedis.subscribe(l, "foo");
```
Note that subscribe is a blocking operation operation because it will poll Redis for responses on the thread that calls subscribe.  A single JedisPubSub instance can be used to subscribe to multiple channels.  You can call subscribe or psubscribe on an existing JedisPubSub instance to change your subscriptions.


### I need to use sharding, but I would like to hint Jedis to force certain keys to go to the same shard

What you need is something called "keytags", and they are supported by Jedis.
To work with keytags you just need to set a pattern when you instance ShardedJedis.
For example:
```java
ShardedJedis jedis = new ShardedJedis(shards,
ShardedJedis.DEFAULT_KEY_TAG_PATTERN);
```
You can create your own pattern if you want. The default pattern is {}, this means that whatever goes inside curly brackets will be used to determine the shard.

So for example:
```java
jedis.set("foo{bar}", "12345");
```
and
```java
jedis.set("car{bar}", "877878");
```
will go to the same shard.
