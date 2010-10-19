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

#### Configure a Maven dependency
Jedis is also distributed as a Maven Dependency through Sonatype. To configure that just add the following XML snippet to your pom.xml file.

    <dependency>
        <groupId>redis.clients</groupId>
        <artifactId>jedis</artifactId>
        <version>1.3.0</version>
        <type>jar</type>
        <scope>compile</scope>
    </dependency>

## Advanced usage

### Using Jedis in a multithreaded environment

**Jedis is not threadsafe**. You shouldn't use the same instance from different threads because you'll have strange errors. And sometimes creating lots of Jedis instances is not good enough because it means lots of sockets and connections, which leads to strange errors as well. So you should use in this cases JedisPool, which is a threadsafe pool of reusable Jedis instances. This way you can overcome those strange errors and achieve great performance.

Yo use it need to:

```java
JedisPool pool = new JedisPool("localhost");
pool.init();
```
You can store the pool somewhere statically, it is threadsafe.

And then you use it by:

```java
Jedis jedis = pool.getResource();
/// ... do stuff here ...
pool.returnResource(jedis);
```

It is important to return the Jedis instance to the pool once you've finished using it. And when you close your application it is good to call:

```java
pool.destroy();
```