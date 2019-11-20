# 秒杀系统设计
##原理
利用redis自带的事务管理实现；使用两个键来统一提交事务，一个做为记数，另一个做为存个人信息的；
##方法步骤
查询记录数是否超过秒杀商品数量；
如果等于商品数量商所有获取商品的用户信息提交给订单服务，并跳过后面程序——没有秒杀机会，
如果大于等于商品数量，并跳过后面程序——没有秒杀机会，
如果小于商品数量——拿到秒杀机会
先关注两个主键；
开始redis事务；
把参与人数加1；
记录参与人信息；
提交redis事务。
##使用redisTemplate与jedis两种方法比较
使用redisTemplate是springboot自带的，框架集成兼容性较好，但是坑也比较多，最重要的事直接使用redisTemplate在提交事务时，会发生错误；
原因是框架每次开启事务都是新建的，因此需要自己写rediscallback：
```java
    SessionCallback sessionCallback = new SessionCallback() {
                   @Override
                   public Object execute(RedisOperations operations) throws DataAccessException {
                       List exec = operations.exec();
                               return exec;
                           }
                       };
                       redisTemplate.execute(sessionCallback);
    }
```
同时要使用redis序列化配置——`com.hejz.seckill.RedisConfiguration`,否则会报序列化错误。