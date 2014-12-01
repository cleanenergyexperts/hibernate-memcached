# Hibernate-memcached
A library for using Memcached as a second level distributed cache in Hibernate.

  * Based on the excellent spymemcached client
  * Includes support for the Whalin (danga) memcached client
  * Supports entity and query caching.

To use this library in another Maven Project:
---------------------------------------------

Add the following to the pom.xml for releases:

```
<repositories>
    <repository>
        <id>maven.leadoperations.co-release</id>
        <name>AWS S3 Release Repository</name>
        <url>http://maven.leadoperations.co/release</url>
    </repository>
</repositories>
```

or for snapshots:
```
<repositories>
    <repository>
        <id>maven.leadoperations.co-snapshot</id>
        <name>AWS S3 Snapshot Repository</name>
        <url>http://maven.leadoperations.co/snapshot</url>
        <snapshots>
            <enabled>true</enabled>
        </snapshots>
    </repository>
</repositories>
```

Then add the dependency:

```
<dependency>
    <groupId>com.cleanenergyexperts</groupId>
    <artifactId>hibernate-memcached</artifactId>
    <version>1.5</version>
</dependency>
```

Releasing a new version:
------------------------
`mvn release:prepare`
`mvn release:perform`

# Versions

## 1.5
  * Hibernate 3.6
  * Spymemcached 2.8.1

## 1.3
  * [HashCodeKeyStrategy][1] [StringKeyStrategy][2] are now both deprecated.
  * [Sha1KeyStrategy][3] is now the default strategy.
  * [Md5KeyStrategy][4] and [Sha1KeyStrategy][3] both digest the entire combined key now (region, clear index, key)
  * [Md5KeyStrategy][4] and [Sha1KeyStrategy][3] have been re-implemented to hash both the toString() and hashCode() values
    of the Hibernate query key object. This should eliminate collisions when using hashCode() alone. 
    [Issue 22](http://code.google.com/p/hibernate-memcached/issues/detail?id=22).
  * [HashCodeKeyStrategy][1] [StringKeyStrategy][2] will throw exceptions now if the key length is greater than 250.

As a result of these changes hibernate-memcached will miss on all cache requests upon upgrading to this version. This
is due to the switch to Sha1KeyStrategy as the default. Hibernate-memcached will now generate different keys for the
same data you were caching previously. Essentially, your cache will appear empty to Hibernate.

Also, as a result of these changes, 1.3 may not be binary compatible with any subclass hacks you may have written that
extend HashCodeKeyStrategy, StringKeyStrategy, Md5KeyStrategy, or Sha1KeyStrategy. Note that the KeyStrategy interface 
and AbstractKeyStrategy have not changed at all. If you implemented/extended those directly you're fine.

  * Patch from @burtbeckwith to allow for memcached authentication via the spymemcached client.
    This can be specified using "hibernate.memcached.username" and "hibernate.memcached.password"

## 1.2.2
  * Patch from ddlatham to allow the spymemcached library to be put 
    into daemon mode. This is accomplished by setting 
    hibernate.memcached.daemonMode to true. 
  * Updated the maven pom to pull in spymemcached 2.4.2 by default. 

# Note on Patches/Pull Requests

  * Fork the project.
  * Make your feature addition or bug fix.
  * Add tests for it. This is important so I don't break it in a future version unintentionally.
  * Commit, do not mess with pom.xml, version, or history. (if you want to have your own version, that is fine but bump version in a commit by itself I can ignore when I pull)
  * Send me a pull request. Bonus points for topic branches.

[1]: hibernate-memcached/blob/master/src/main/java/com/googlecode/hibernate/memcached/HashCodeKeyStrategy.java
[2]: hibernate-memcached/blob/master/src/main/java/com/googlecode/hibernate/memcached/StringKeyStrategy.java
[3]: hibernate-memcached/blob/master/src/main/java/com/googlecode/hibernate/memcached/Sha1KeyStrategy.java
[4]: hibernate-memcached/blob/master/src/main/java/com/googlecode/hibernate/memcached/Md5KeyStrategy.java
