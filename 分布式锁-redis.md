#### 分布式锁-redis

##### 1.获取锁lua脚本

```lua
local key = KEYS[1]
local threadId = ARGV[1]
local releaseTime = ARGV[2]
if (redis.call('exists' ,key) == 0) then
	redis.call('hset' ,key ,threadId ,'1')
	redis.call('expire' ,key ,releaseTime)
	return 1
end;
if (redis.call('hexists' ,key ,threadId) == 1) then
	redis.call('hincrby' ,key ,threadId ,'1')
	redis.call('expire' ,key ,releaseTime)
	return 1
end;
return 0
```

##### 2.释放锁lua脚本

```lua
local key = KEYS[1]
local threadId = ARGV[1]
local releaseTime = ARGV[2]
if (redis.call('hexists' ,key ,threadId) == 0) then
	return nil
end;
local count = redis.call('hincrby' ,key ,threadId ,-1);
if (count > 0) then
	redis.call('expire' ,key ,releaseTime)
	return nil
else
	redis.call('del' ,key)
	return nil
end;
```

##### 3.设置可重入式锁接口

```java
package com.wzw.redislock.lock;

/**
 * @Classname RedisLock
 * @Description TODO
 * @Date 2019/8/25 23:24
 * @Created by wzw
 */
public interface RedisLock {
    /**
     * 获取锁
     * @param releaseTime 锁的自动释放时间
     * @return 获取锁是否成功
     */
    Boolean tryLock(long releaseTime);

    /**
     * 释放锁
     */
    void unLock();
}
```

##### 4.设置可重入式锁实现

```java
package com.wzw.redislock.lock;

import org.springframework.core.io.ClassPathResource;
import org.springframework.data.redis.core.StringRedisTemplate;
import org.springframework.data.redis.core.script.DefaultRedisScript;
import org.springframework.scripting.support.ResourceScriptSource;

import java.util.Collections;
import java.util.UUID;

/**
 * @Description TODO
 * @Date 2019/8/25 23:28
 * @Created by wzw
 */
public class ReentrantRedisLock implements RedisLock {
    private StringRedisTemplate stringRedisTemplate;
    private String key;

    private final String ID_PREFIX = UUID.randomUUID().toString();

    private static final DefaultRedisScript<Long> LOCK_SCRIPT;
    private static final DefaultRedisScript<Object> UNLOCK_SCRIPT;

    private String releaseTime;

    static {
        LOCK_SCRIPT = new DefaultRedisScript<>();
        LOCK_SCRIPT.setScriptSource(new ResourceScriptSource(new ClassPathResource("lock.lua")));
        LOCK_SCRIPT.setResultType(Long.class);

        UNLOCK_SCRIPT = new DefaultRedisScript<>();
        UNLOCK_SCRIPT.setScriptSource(new ResourceScriptSource(new ClassPathResource("unlock.lua")));
    }
    public ReentrantRedisLock(StringRedisTemplate stringRedisTemplate , String key) {
        this.stringRedisTemplate = stringRedisTemplate;
        this.key = key;
    }

    @Override
    public Boolean tryLock(long releaseTime) {
        this.releaseTime = String.valueOf(releaseTime);
        Long result = stringRedisTemplate.execute(LOCK_SCRIPT ,
                Collections.singletonList(key),
                ID_PREFIX + Thread.currentThread().getId(),
                this.releaseTime);
        return result != null && result.intValue() == 1;

    }

    @Override
    public void unLock() {
        stringRedisTemplate.execute(
                UNLOCK_SCRIPT,
                Collections.singletonList(key),
                ID_PREFIX + Thread.currentThread().getId(),
                this.releaseTime
        );
    }
}
```

##### 5.设置可重入锁工厂

```java
package com.wzw.redislock.lock;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.redis.core.StringRedisTemplate;
import org.springframework.stereotype.Component;

/**
 * @Description TODO
 * @Date 2019/8/26 0:15
 * @Created by wzw
 */
@Component
public class RedisLockFactory {
    @Autowired
    private StringRedisTemplate stringRedisTemplate;

    public RedisLock getReentrantRedisLock(String key){
        return new ReentrantRedisLock(stringRedisTemplate,key);
    }
}
```

##### 6.设置定时清理订单的任务

```java
package com.wzw.redislock.lock;

import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.stereotype.Component;

/**
 * @Description TODO
 * @Date 2019/8/26 0:19
 * @Created by wzw
 */
@Slf4j
@Component
public class ClearOrderTask {
    @Autowired
    private RedisLockFactory factory;

    @Scheduled(cron = "0/10 * * ? * *")
    public void clearOrderTask() throws InterruptedException {
        RedisLock lock = factory.getReentrantRedisLock("lock1");
        Boolean isLock = lock.tryLock(50);
        if (!isLock) {
            log.error("获取锁失败,定时任务失败");
            return;
        }
        try {
            log.info("获取锁成功");
            clearOrder();
        } finally {
            log.warn("任务执行完毕，释放锁");
            lock.unLock();
        }
    }

    public void clearOrder() throws InterruptedException {
      log.info("开始清理订单");
      Thread.sleep(500);
      log.info("开始恢复库存");
    }
}
```

