## 基于注解的rabbitmq消息队列操作

1. docker 安装

   ```java
   docker pull rabbitmq:3-management
   docker run -d -p 5672:5672 -p 15672:15672 --name myrabbitm
   q 68898be27496
   
   pom添加dependency：
       <dependency>
           <groupId>org.springframework.amqp</groupId>
           <artifactId>spring-rabbit-test</artifactId>
           <scope>test</scope>
       </dependency>    
   properties配置环境：
   spring:
     rabbitmq:
       host: 10.69.17.30
       username: guest
       password: guest
           
   ```

2. 基本操作

   ```java
   //访问对应端口跳转到rabbitmq控制台界面,默认密码/账户guest
   //添加三个交换机
   //1.direct 2.fanout 3.topic 分别对应，全匹配;全部发送;模糊匹配
   //添加queues队列，并绑定到对应的交换机中
   
   设置json序列化
   //    @Bean
   //    public MessageConverter messageConverter(){
   //        return new Jackson2JsonMessageConverter();
   //    }
   
   //自动注入    
   @Autwired
   RabbitTemplate rabbitTemplate;
   rabbitTemplate.convertAndSend("exchange-direct","atguigu.news",new Book("天佐","罗海"));
   //发送自定义消息体
   Object o = rabbitTemplate.receiveAndConvert("atguigu.news");
   System.out.println(o);
   //直接接收,会自动反序列化
   ```

3. 添加监听

   ```java
   //在Application中开启rabbit注解 @EnableRabbit
   //在Service中添加@RabbitListener(queues = "atguigu.news")
   ```

   