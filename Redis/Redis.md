# Redis 语法

## String

- set key value					设置 key 对应的值为 string 类型的 value
- get key
- mset key1 val1 ... keyN valN 	一次设置多个 key 的值
- mget key1 key2 ... keyN 		一次获取多个 key 的值
- incr key 						对 key 的值做加加操作, 并返回新的值
- decr key 						对 key 的值做减减操作, 并返回新的值
- incrby key integer 			同 incr, 加指定值
- decrby key integer 			同 decr, 减指定值
- append key value 				给指定 key 的字符串追加 value
- substr key start end 			返回截取过的 key 的字符串的值

## Hash

- hset key field value 			设置 hash field 为指定值, 如果 key 不存在, 则先创建
- hget key field 				获取指定的 hash field
- hmget key field1 ... fieldN 	获取全部指定的 hash field
- hmset key field1 value1 ... fieldN valueN 	
								同时设置 hash 的多个 field
- hincrby key field integer 	将指定的 hash field 加上给定值
- hexists key field 			测试指定 field 是否存在
- hdel key field 				删除指定的 hash field
- hlen key 						返回指定 hash 的 field 数量
- hkeys key 					返回 hash 的所有 field
- hvals key 					返回 hash 的所有 value
- hgetall key 					返回 hash 的所有 field 和 value

## List(列表)

