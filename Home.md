# Welcome to the jedis wiki!

## Getting started

### Installing Jedis
In order to have Jedis as a dependency in your application you can:

#### Clone the github project and compile the code
That is very easy, you just need to:
    git clone git://github.com/xetorthio/jedis.git

And then package it:
    mvn package

This will run the tests. And the tests use two instances of the latests Redis version (you can have that by compiling the maste branch), these Redis instances should run one of the default port (6379) and the other on (6380) both with the default authentication password enabled (foobared). You can use the provided conf files in the conf folders.

#### Download the JAR from github
Just go to the Downloads section and use the latest Jedis JAR available.

#### Apache Commons Dependency
You will also need to download Apache Commons at:
   http://commons.apache.org/pool/download_pool.cgile   and then import:
import org.apache.commons.pool.impl.GenericObjectPool.Config;

#### Configure a Maven dependency
Jedis is also distributed as a Maven Dependency through Sonatype. To configure that just add the following XML snippet to your pom.xml file.

    <dependency>
        <groupId>redis.clients</groupId>
        <artifactId>jedis</artifactId>
        <version>1.5.0</version>
        <type>jar</type>
        <scope>compile</scope>
    </dependency>

## Advanced usage

### Using Jedis in a multithreaded environment

**Jedis is not threadsafe**. You shouldn't use the same instance from different threads because you'll have strange errors. And sometimes creating lots of Jedis instances is not good enough because it means lots of sockets and connections, which leads to strange errors as well. So you should use in this cases JedisPool, which is a threadsafe pool of reusable Jedis instances. This way you can overcome those strange errors and achieve great performance.

To use it, init a pool with:

```java
JedisPool pool = new JedisPool("localhost");
pool.init();
```
As of jedis 1.5, use (note the dependency on commons-pool):
```java
JedisPool pool = new JedisPool(new Config(), "localhost");
```

You can store the pool somewhere statically, it is threadsafe.

And then you use it by:

```java
Jedis jedis = pool.getResource();
/// ... do stuff here ...
pool.returnResource(jedis);
```

It is important to return the Jedis instance to the pool once you've finished using it. You should also take the time to adjust the Config() settings to your use case. By default, Jedis will close a connection after 300 seconds if it has not been returned. When you close your application it is good to call:

```java
pool.destroy();
```

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

### Pipelining

Sometimes you need to send a bunch of different commands. A very cool way to do that, and have better performance than doing it the naive way, is to use pipelining. This way you send commands without waiting for response, and you actually read the responses at the end, which is faster.
To do that in Jedis you just need to:

```java
List<Object> results = jedis.pipelined(new JedisPipeline() {
    public void execute() {
        client.set("foo", "bar");
        client.get("foo");
    }
});
```

As of jedis 1.5, JedisPipeline is now PipelineBlock and there is a new option for creating pipelines:

```java
// Method #1
Pipeline p = jedis.pipelined();
p.set("foo", "bar");
p.get("foo");
List<Object> results = p.execute();
```

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

###Transactions
To do transactions in jedis, you have to wrap operations in a transaction block:

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


Note2: Versions after 1.5.2 will have improved support for transactions and pipelining:

```java
Transaction t = jedis.multi();
t.set("foo", "bar");
Future<String> result1 = t.get("foo");
Future<Map<String, String>> result2 = t.hgetAll("car");
List<Object> allResults = t.exec();
```
So there will be the optional possibility to get specific entries from the entire transaction. It will be no longer required to deal with positions and conversions.


