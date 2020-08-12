#### delayQueue使用

##### 1.delayItem

```
package com.xdd.epr.vshop.service.groupbuy;

import java.util.concurrent.Delayed;
import java.util.concurrent.TimeUnit;

/**
 * @author wzw
 * @date 2019-12-28 14:58
 */
public class DelayItem<T> implements Delayed {

    private final T t;

    private final long expireTime;

    public T getMember() {
        return t;
    }

    public long getExpireTime() {
        return expireTime;
    }


    public DelayItem(T t, long expireTime) {
        this.t = t;
        this.expireTime = expireTime;
    }

    @Override
    public long getDelay(TimeUnit unit) {
        return unit.convert(expireTime - System.currentTimeMillis(), TimeUnit.MILLISECONDS);
    }

    @Override
    public int compareTo(Delayed o) {
        long result = this.getDelay(TimeUnit.MILLISECONDS)
                - o.getDelay(TimeUnit.MILLISECONDS);
        if (result < 0) {
            return -1;
        } else if (result > 0) {
            return 1;
        } else {
            return 0;
        }
    }
}

```

##### 2.生产者

```
package com.xdd.epr.vshop.service.groupbuy;

import java.util.concurrent.DelayQueue;

/**
 * @author wzw
 * @date 2019-12-28 15:57
 */
public class DelayQueueProduce {

    public static DelayQueue DELAY_QUEUE = new DelayQueue();


    public static <T> void add(DelayItem<T> delayItem) {
        DELAY_QUEUE.add(delayItem);
    }
}

```

##### 3.单元测试

```
package com.xdd.epr.vshop.service.groupbuy;

import com.xdd.components.trace.log.XddLOG;
import org.junit.Test;


/**
 * @author wzw
 * @date 2019-12-30 16:27
 */
public class DelayTest {
    private XddLOG logger = new XddLOG(DelayTest.class);

    @Test
    public void produce() throws InterruptedException {
        long now = System.currentTimeMillis();

        DelayItem<Long> delayed = new DelayItem(1L, now + 1000L);
        DelayItem<Long> delayed1 = new DelayItem(2L, now + 2000L);
        DelayItem<Long> delayed2 = new DelayItem(3L, now + 3000L);
        DelayItem<Long> delayed3 = new DelayItem(4L, now + 4000L);
        DelayQueueProduce.add(delayed);
        logger.info("进入本地缓存队列元素->{},过期时间 ->{}",delayed.getMember(),delayed.getExpireTime());
        DelayQueueProduce.add(delayed1);
        logger.info("进入本地缓存队列元素->{},过期时间 ->{}",delayed1.getMember(),delayed1.getExpireTime());
        DelayQueueProduce.add(delayed2);
        logger.info("进入本地缓存队列元素->{},过期时间 ->{}",delayed2.getMember(),delayed2.getExpireTime());
        DelayQueueProduce.add(delayed3);
        logger.info("进入本地缓存队列元素->{},过期时间 ->{}",delayed3.getMember(),delayed3.getExpireTime());

        new Thread(() -> {
            while (true) {
                try {
                    DelayItem take = (DelayItem) DelayQueueProduce.DELAY_QUEUE.take();
                    logger.info("开始消费元素->{}过期时间 ->{}",take.getMember(), take.getExpireTime());

                } catch (Exception e) {
                    logger.error("异常",e);
                }
            }
        }).start();

        Thread.sleep(10*1000);

    }


}

```

##### 4项目启动时立即启动消费

```
package com.xdd.epr.vshop.service.groupbuy;

import com.xdd.epr.vshop.executor.ExecutorPool;
import org.springframework.beans.factory.InitializingBean;
import org.springframework.stereotype.Component;

import javax.annotation.Resource;
import java.util.concurrent.ThreadPoolExecutor;

/**
 * @author wzw
 * @date 2019-12-28 18:00
 */
@Component
public class SpringFinishedListener implements InitializingBean {
    @Resource
    private IGroupBuyEndService groupBuyEndService;

    private ThreadPoolExecutor executor = ExecutorPool.THREAD_POOL_EXECUTOR;

    @Override
    public void afterPropertiesSet() {
        executor.execute(() -> groupBuyEndService.handleEndActivity());
    }
}


    /**
     * 处理拼团结束活动
     */
    @Override
    public void handleEndActivity() {
        //处理拼团结束、智能拼团逻辑
        delayQueueConsume.consume();
    }



    public void consume() {
        while (true) {
            try {
                DelayItem delayItem = (DelayItem) DelayQueueProduce.DELAY_QUEUE.take();
                LOGGER.info("DelayQueueConsume本地延时队列:times={}ms, delayItem={}", delayItem.getExpireTime() - System.currentTimeMillis(),
                        JSON.toJSONString(delayItem));
                if (delayItem == null || delayItem.getMember() == null) {
                    continue;
                }
                Long activityId = (Long) delayItem.getMember();
                GroupBuyActivityDTO groupBuyActivityDTO = groupBuyActivityService.getById(activityId);
                LOGGER.info("DelayQueueConsume发消息队列:groupBuyActivityDTO={}", JSON.toJSONString(groupBuyActivityDTO));
                sendMsg(groupBuyActivityDTO);
            } catch (Exception e) {
                LOGGER.error("本地延时队列异常", e);
            }
        }
    }

```

