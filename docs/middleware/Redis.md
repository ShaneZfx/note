# Redis																														

## 简介



​		由截取自官网的一段描述中看出，`redis（Remote DIctionary Server）`远程字典服务器，是一个工作在内存中的数据结构存储系统，内置了复制、`lua`脚本、`LRU`淘汰算法、事务以及持久化等一些功能。并且通过`sentinel`和`cluster`来提供高可用服务。



> Redis is an open source (BSD licensed), in-memory data structure store, used as a database, cache and message broker. It supports data structures such as strings, hashes, lists, sets, sorted sets with range queries, bitmaps, hyperloglogs, geospatial indexes with radius queries and streams. Redis has built-in replication, Lua scripting, LRU eviction, transactions and different levels of on-disk persistence, and provides high availability via Redis Sentinel and automatic partitioning with Redis Cluster



## 特性

### 数据结构

#### 对外结构

1. **String**
2. **hash**
3. **list**
4. **set**
5. **sorted set**

#### 内部结构

1. **redisDb**

   ```c
   /* Redis database representation. There are multiple databases identified
    * by integers from 0 (the default database) up to the max configured
    * database. The database number is the 'id' field in the structure. */
   typedef struct redisDb {
       dict *dict;                 /* The keyspace for this DB */
       dict *expires;              /* Timeout of keys with a timeout set */
       dict *blocking_keys;        /* Keys with clients waiting for data (BLPOP)*/
       dict *ready_keys;           /* Blocked keys that received a PUSH */
       dict *watched_keys;         /* WATCHED keys for MULTI/EXEC CAS */
       int id;                     /* Database ID */
       long long avg_ttl;          /* Average TTL, just for stats */
       list *defrag_later;         /* List of key names to attempt to defrag one by one, gradually. */
   } redisDb;
   ```

   整个`redis`实例可以划分为多个`database`，类似于`MySQL`，一个实例里可以建多个`database`。一个`redis`实例默认有16个库（0-15），每个库都是一个大型`dict`

2. **dict**

   ```c
   typedef struct dict {
       dictType *type;
       void *privdata;
       dictht ht[2];
       long rehashidx; /* rehashing not in progress if rehashidx == -1 */
       unsigned long iterators; /* number of iterators currently running */
   } dict;
   ```

   `Redis`的一个`database`就是一个`dict`，每个`dict`由两张哈希表`dictht`构成（第二张表用于`rehash`）

3. **dictType**

   ```c
   typedef struct dictType {
       uint64_t (*hashFunction)(const void *key);
       void *(*keyDup)(void *privdata, const void *key);
       void *(*valDup)(void *privdata, const void *obj);
       int (*keyCompare)(void *privdata, const void *key1, const void *key2);
       void (*keyDestructor)(void *privdata, void *key);
       void (*valDestructor)(void *privdata, void *obj);
   } dictType;
   ```

   函数表，提供hash，键值的复制、比较、删除功能

4. **dictht**

   ```c
   /* This is our hash table structure. Every dictionary has two of this as we
    * implement incremental rehashing, for the old to the new table. */
   typedef struct dictht {
       dictEntry **table; // 为什么这样的一个声明是一个数组
       unsigned long size;
       unsigned long sizemask;
       unsigned long used;
   } dictht;
   ```

   哈希表，由数组构成，数组槽元素为`dictEntry`

5. **dictEntry**

   ```c
   typedef struct dictEntry {
       void *key;
       union {
           void *val;
           uint64_t u64;
           int64_t s64;
           double d;
       } v;
       struct dictEntry *next;
   } dictEntry;
   ```

   单链表形式的哈希表实体单元

6. **sds**

   ```c
   /* Note: sdshdr5 is never used, we just access the flags byte directly.
    * However is here to document the layout of type 5 SDS strings. */
   struct __attribute__ ((__packed__)) sdshdr5 {
       unsigned char flags; /* 3 lsb of type, and 5 msb of string length */
       char buf[];
   };
   
   //  __attribute__ ((__packed__)) 关键字用于紧凑排列，与之相对的即是字节对齐
   struct __attribute__ ((__packed__)) sdshdr8 { 
       uint8_t len; /* used */
       uint8_t alloc; /* excluding the header and null terminator */
       unsigned char flags; /* 3 lsb of type, 5 unused bits */
       char buf[];
   };
   struct __attribute__ ((__packed__)) sdshdr16 {
       uint16_t len; /* used */
       uint16_t alloc; /* excluding the header and null terminator */
       unsigned char flags; /* 3 lsb of type, 5 unused bits */
       char buf[];
   };
   struct __attribute__ ((__packed__)) sdshdr32 {
       uint32_t len; /* used */
       uint32_t alloc; /* excluding the header and null terminator */
       unsigned char flags; /* 3 lsb of type, 5 unused bits */
       char buf[];
   };
   struct __attribute__ ((__packed__)) sdshdr64 {
       uint64_t len; /* used */
       uint64_t alloc; /* excluding the header and null terminator */
       unsigned char flags; /* 3 lsb of type, 5 unused bits */
       char buf[];
   };
   
   // 不同类型的sds对应的标志值，存储于flags字段（低三位存储标志数值，高五位未使用）
   #define SDS_TYPE_5  0
   #define SDS_TYPE_8  1
   #define SDS_TYPE_16 2
   #define SDS_TYPE_32 3
   #define SDS_TYPE_64 4
   ```

   `redis`中以**紧凑排列**的方式来存储二进制的数据结构，用于`String`的底层映射实现。

   

7. 

### 持久化

### 淘汰策略

### 事务

### 集群

## 案例

