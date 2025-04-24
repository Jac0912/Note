redis数据库为键值数据库，默认有16个数据库，且开始使用0号数据库
```sql
redis-cli  --进入redis客户端
FLUSHALL  --清理所有键的缓存

select <序号>  --切换数据库
set <key> <value>  --设置键值对
get <key>  --取key所对应的value
set user:info:用户ID:name  lbw  --实例，key的命名规范
set <key> <value> EX <秒>  --设置过期时间(秒)
set <key> <value> PX <毫秒>  --设置过期时间(毫秒)
expire <key> <秒>  --设置过期时间(秒)
ttl <key>  --查看过期时间
persist <key>  --转换为永久
del <key>  --删除
keys *  --查看所有key
exists <key>  --查看key是否存在
randomkey  --随机拿一个key
move <key> <序号>  --移动key到另一个数据库
rename <序号> <新名称>  --重命名key
renamenx <序号> <新名称>  --重命名key（并检查key是否已经存在）
incr <key>  --等价于 a = a + 1
incrby <key> b  --等价于 a = a + b
decr <key>  --等价于 a = a - 1
type <key>  --查看数据类型

--Hash
hset <key> [<字段> <值> ……]  --添加Hash类型的数据
hget <key> <字段>  --获取
hgetall <key>  --获取所有字段和值
hexists <key> <字段>  --判断某个字段是否存在
hdel <key>  --删除某个字段
hlen <key>  --存放键值对的个数
hvals <key> --获取所有字段的值

--List
lpush <key> <element> ……  --向列表头部添加数据
rpush <key> <element> ……  --向列表尾部添加数据
linsert <key> before/after <指定元素> <element> ……  --向指定元素前/后添加数据
lindex <key> <下标>  --根据下标获取元素
lpop <key>  --获取并移除头部元素
rpop <key>  --获取并移除尾部元素
lrange <key> start stop  --获取指定范围内的元素
rpoplpush <当前数组> <目标数组>  --将前一个数组的最后一个数取出来放到另一个数组的头部，并放回元素
blpop <key> …… timeout  --如果列表(可多个)中没有元素就等待指定秒，只要有一个列表中有元素就立即取出

--Set(无序，唯一，支持高性能访问)
sadd <key> <value> ……  --添加一个或多个值
scard <key>  --查看set集合中有多少个值
sismember <key> <value>  --是否包含指定值
smembers <key>  --列出所有值
sdiff <key1> <key2>  --差集：<key1> - <key2>
sinter <key1> <key2>  --交集
sunion <key1> <key2>  --并集
sdiffstore <目标> <key1> <key2>  --将差集存入目标集合
sinterstore <目标> <key1> <key2>  --将交集存入目标集合
sunionstore <目标> <key1> <key2>  --将并集存入目标集合
smove <key> <目标> <value>  --移动指定值到另一个集合
spop <key>  --随机移除
srem <key> <value>  --移除指定值

--SortedSet(有序的Set)
zadd <key> [<value> <score> ……]  --添加带分数的值
zcard <key>  --查询有多少个值
zrem <key> <value>  --移除
zrange <key> start stop  --获取下标区间内所有
zrangebyscore <key> start stop [withscores] [limit] <起始下标> 个数  --获取分数区间内所有
zcount <key> start stop  --统计分数段内的数量
zrank <key> <value>  --根据分数获取指定值的排名

--持久化(RDB)
save  --当前进程保存
bgsave  --开子进程保存
--可通过配置文件定时保存

--持久化(AOF)
--以日志的形式将命令全部保存，解决实时性存储问题
--三种策略：always(每次执行写操作保存一次)、everysec(每秒保存一次)、no(系统决定)
--通过配置文件设置
bgrewriteaof  --进行重写日志操作，自动合并可精简的命令(也可通过配置文件设置定时重写)

```

设置密码
`/etc/redis/redis.conf`
```sql
requirepass
```