# Welcome to the jedis wiki!

## Getting started

Check out the [[ReleaseNotes]].


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

#### Configure a Maven dependency
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

## Basic usage
### Using Jedis in a multithreaded environment
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


## Advanced usage

###Transactions
To do transactions in Jedis, you have to wrap operations in a transaction block, very similar to pipelining:

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



Note 2: From version 2.0 there is a much improved support for transactions. It provides a convenient access the results of the transaction without the need to extract them of the List<Object> as showed above. Note that a Response Object does not contain the result before t.exec() is called (it is a kind of a Future).

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

### ShardedJedis
####Motivation
In the normal master slave approach, you have many slaves that serve read requests but only one master that serves write requests. Furthermore, you have to come up with your own plan to actually distribute the load on the slaves. In Sharded jedis you achieve scalability for both reads and writes.  Sharding assigns the keys equally on a set of redis servers according to some hash algorithm (md5 and murmur, the latter being less standard, but faster). A node like this is then called a "shard". 

####The downside 
is that, since each shard is a separate master, sharding has limited functionality: i.e. you cannot do transactions, pipelining, pub/sub across shards! However, generally it is feasible to do a not allowed operation, as long as the concerned keys are on the same shard (check / ask the forum). You can influence which key go to which shard by keytags (see below).

#### Redis Cluster
Sometime later 2011, there will be first versions of "redis cluster" which will be a much improved Sharded Jedis and should give back some if not all of the Redis functionalities you cannot have with shardedJedis. If you want to know more about redis cluster, youtube has a presentation of Salvatore Sanfilippo (the creator of Redis).

#### A compromise
If you want easy load distribution of ShardedJedis, but still need transactions/pipelining/pubsub etc, you can also mix the normal and the sharded approach: define a master as normal Jedis, the others as sharded Jedis. Then make all the shards slaveof master. In your application, direct your write requests to the master, the read requests to ShardedJedis. Your writes don't scale anymore, but you gain good read distribution, and you have transactions/pipelining/pubsub. Remember that you can improve performance of the master a lot, if you let the slaves do the persistance for the master!

Here is the general proceeding:

#### 1. Define your shards:
```java
List<JedisShardInfo> shards = new ArrayList<JedisShardInfo>();
JedisShardInfo si = new JedisShardInfo("localhost", 6379);
si.setPassword("foobared");
shards.add(si);
si = new JedisShardInfo("localhost", 6380);
si.setPassword("foobared");
shards.add(si);
```

Then, there are two ways of using ShardedJedis. Direct connections or by using ShardedJedisPool. For reliable operation, the latter has to be used in a multithreaded environment.

#### 2.a) Direct connection:
```java
ShardedJedis jedis = new ShardedJedis(shards);
jedis.set("a", "foo");
jedis.disconnect;
```

#### 2.b) Pooled connection:
```java
ShardedJedisPool pool = new ShardedJedisPool(new Config(), shards);
ShardedJedis jedis = pool.getResource();
jedis.set("a", "foo");
.... // do your work here
pool.returnResource(jedis);
.... // a few moments later
ShardedJedis jedis2 = pool.getResource();
jedis.set("z", "bar");
pool.returnResource(jedis);
pool.destroy();
```

#### 3. Disconnect / returnRessource
pool.returnResource should be called as soon as you are finished using jedis in a particular moment. If you don't, the pool may get slower after a while. getResource and returnResource are fast, since no new connection have to be created. Creation and destruction of a pool are slower, since theses are the actual network connections. Forgetting pool.destroy keeps the connection open until timeout is reached.


#### Determine information of the shard of a particular key

```java
ShardInfo si = jedis.getShardInfo(key);
si.getHost/getPort/getPassword/getTimeout/getName
```

#### Force certain keys to go to the same shard
What you need is something called "keytags", and they are supported by Jedis. To work with keytags you just need to set a pattern when you instance ShardedJedis.
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

### Monitoring
To use the monitor command you can do something like the following:
```java
new Thread(new Runnable() {
    public void run() {
        Jedis j = new Jedis("localhost");
        for (int i = 0; i < 100; i++) {
            j.incr("foobared");
            try {
                Thread.sleep(200);
            } catch (InterruptedException e) {
            }
        }
        j.disconnect();
    }
}).start();

jedis.monitor(new JedisMonitor() {
    public void onCommand(String command) {
        System.out.println(command);
    }
});
```

##  Misc 

### A note about String and Binary - what is native?
Redis/Jedis talks a lot about Strings. And here [[http://redis.io/topics/internals]] it says Strings are the basic building block of Redis. However, this stress on strings may be misleading. Redis' "String" refer to the C char type (8 bit), which is incompatible with Java Strings (16-bit). Redis sees only 8-bit blocks of data of predefined length, so normally it doesn't interpret the data (it's "binary safe"). Therefore in Java, byte[] data is "native", whereas Strings have to be encoded before being sent, and decoded after being retrieved by the SafeEncoder. This can have a considerable performance impact.
In short: if you have binary data, don't encode it into String, but use the binary versions.

### A note on Redis' master/slave distribution
A Redis network consists of redis servers, which can be either masters or slaves. Slaves are synchronized to the master (master/slave replication). However, master and slaves look identical to a client, and slaves do accept write requests, but they will not be propagated "up-hill" and could eventually be overwritten by the master. It makes sense to route reads to slaves, and write demands to the master. Furthermore, being a slave doesn't prevent from being considered master by another slave. 

####[Redis: under the hood](http://pauladamsmith.com/articles/redis-under-the-hood.html)
#### Reading [[FAQ]] might be useful.
