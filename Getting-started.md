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