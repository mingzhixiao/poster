### RabbitMq的使用

#### 1、rabbitmq的安装

https://blog.csdn.net/hzw19920329/article/details/53156015

#### 2、rabbitmq的简单操作

##### a.路由器操作

###### 第一步:

```
//创建一个rabbitmq的增改删的注入管理
@Autowired
AmqpAdmin amqpAdmin;
```

###### 第二步

```
@Test
public void newText(){
    //创建路由器
   // amqpAdmin.declareExchange(new FanoutExchange("mingzhixiao"));广播式
   // amqpAdmin.declareExchange(new TopicExchange("mingzhixiao"));规则式广播
    //amqpAdmin.declareExchange(new DirectExchange("mingzhixiao"));
    //创建消息队列 默认不持久化 选择true的话可以队列一直存在
    //amqpAdmin.declareQueue(new Queue("wozhidao.queue",true));//点对点式
    //申明绑定
    /**
     * @description:  路由器绑定消息队列以及路由件
     * @param:  队列，路由器，路由件，无参数
     */
    amqpAdmin.declareBinding(new Binding("wozhidao.queue",Binding.DestinationType.QUEUE,"mingzhixiao","a",null));
}
```

##### b.消息操作

###### 第一步：消息的发送

```
public void contextLoads() {
    //message 需要自己构造一个；定义消息的内容，和消息头
    //rabbitTemplate.send(exchange,routekey,message);
    //object 默认当成消息体，只需要传入发送的对象，自动序列化发送给rabbit
    //rabbitTemplate.convertAndSend(exchange,routekey,object);
    Map<String ,Object> map = new HashMap<>();
    map.put("msg","只是第一个消息");
    map.put("data", Arrays.asList("helloWorld",123,true));
    rabbitTemplate.convertAndSend("exchange.direct","atguigu.news",map);
}
```

###### 第二步：消息的接受

```
public void  test01(){
    Object receive = rabbitTemplate.receiveAndConvert("mingzhixiao");
    System.out.println(receive.getClass());
    System.out.println(receive);

}
```

#### 3、RabbitMap的应用

```
@Service
public class BookService {
    int i = 0;
    @RabbitListener(queues = "atguigu")
    public void receive(Book book){
        System.out.println("收到了"+ (++i)+"本书"+book);
    }

    @RabbitListener(queues = "mingzhixiao.wzw")//创建一个对于消息队列的监听
    public void receive02(Message message){
        System.out.println(message.getBody());
        System.out.println(message.getMessageProperties());
    }
}
```

在启动类中加入@EnableRabbit  //开启基于注解的rabbitmq模式



#### 4、传递消息json化，这样的配置可以关闭默认的java序列化

```
@Configuration
public class MyAMQPConfig {
    @Bean
    public MessageConverter messageConverter(){
        return new Jackson2JsonMessageConverter();
    }
}

```