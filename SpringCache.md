## Cache基本使用

```java
    /**
     * CacheManager管理多个Cache组件的，对缓存的真正的CURD的操作在Cache组件中，每一个缓存组件有自己唯一的名字
     * 一：指定缓存组件的名字：CacheNames,/value 指定缓存组件的名字；
     * key：缓存数据使用的key；
     * 方法一：key = "#id"//通过Spel表达式
     * 方式二：key = "#root.args[0]"//按位数取
     * condition = "#id>0"指定条件
     * unless当xx条件满足时就不缓存了,比指定条件厉害点，可以得到结果集
     * */
```

1. 

2. Cacheable

   ```java
   @Cacheable(cacheNames = "emp",key = "#id")//将这个方法的运行结果进行缓存
   ```

3. CachePut

   ```java
   @CachePut(vlaue = "emp",key = "#result.id")//执行方法体，再将结果更新到对应仓库
   ```

4. CacheEvict

   ```java
   @CacheEvice(value = "emp" key="#id")//一般在方法体之后执行可加参数
   //allEntries全部删除，在方法执行之前删除所有，优先级最高，谨慎使用
   //beforeInvocation = true在方法之前
   ```

5. Caching

   ```java
       @Caching(
               cacheable = {
                       @Cacheable(value = "emp",key = "#lastname")
               },
               put = {//因为CachePut起作用了，所以方法还是会执行的，但返回结果是之前缓存的
                       @CachePut(value = "emp",key = "#result.id"),
                       @CachePut(value = "emp",key = "#result.email")
               }
       )
   ```

6. ```java
   @CacheConfig(cacheNames = "emp")//抽取公共配置，除了配置当前server的全局仓库，总共有四大功能，这是其一
   ```

## Redis--基于虚拟机docker

1. 基本命令

   ```makefile
   刚配好虚拟机对yum进行换源：https://blog.csdn.net/qq_25760623/article/details/88657491
   Ip addr 查看虚拟机IP地址
   Cd  /etc/docker 进入目录
   Vi /etc/docker/daemon.json 按i进入编辑，按esc输入:wq退出保存
   Tee /etc/docker/daemon.json <<-“EOF” 输入内容 输入如EOF保存退出
   sudo Systemtcl start docker 启动docker
   sudo Systemtcl restart docker重启docker
   sudo Systemtcl daemon-reload重新载入镜像源
   Yum install -y docker-ce 安装docker
   Docker search mysql 搜索容器
   Docker pull mysql 拉取mysql容器
   Docker images 查看所有拉取的镜像
   Docker run –name mytomcat -d tomcat:latest 利用镜像启动tomcat容器
   Docker start 容器的id：····~~启动一个存在的容器
   Docker stop 容器的id：一长串的字符
   Docker ps查看启动的容器，加上-a参数查看所有启动过的容器
   Docker rm 容器的id：~~
   Docker run -p 3306:3306 –name mysql01 -e MYSQL_ROOT_PASSWORD=mysql密码 -d mysql mysql容器的启动需要一个密码，根据官方文档操作
   通过Navicat对mysql进行链接：
   进入mysql容器:docker exec -it mysql02 bash
   对远程链接进行授权:GRANT ALL ON *.* TO ‘ROOT’@’%’
   更改密码的加密规则：ALTER USER 'root'@'%' IDENTIFIED BY 'password' PASSWORD EXPIRE NEVER;
   ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY 'zhengchuang';
   Flush privileges;
   链接地址是虚拟机的地址：ip attr
   
   ```

2. redis换源

   ```c
   docker pull registry.docker-cn.com/library/redis//好像废除掉了;
   某个大佬的阿里源通道：   
   {
   "registry-mirrors": ["https://bga2aar3.mirror.aliyuncs.com"]
   } 
   ```

3. 启动redis

   ```java
   docker run -d -p 6379:6379 --name myredis redis//端口向外映射暴露 docker ps查看
   ```

4. RedisTemplate

   ```java
   @Autowired
   StringRedisTemplate stringRedisTemplate;
   
   @Autowired
   RedisTemplate redisTemplate;
   //Redis常见的五大数据类型
   /**String(字符串）、List(列表）、Set(集合)、Hash(散列）、ZSet(有序集合)
   stringRedisTemplate.opsForValue()[String（字符串）〕
   stringRedisTempLate.opsForList()[List(列表）〕
   *stringRedisTemplate.opsForSet()[set(集合）〕
   stringRedisrempLate.opsForHash()[Hash（散列）j
   *stringRedisTemplate.opsForZSet()[zSet（有序集合）]
   */
   //字符串操作 
   stringTemplate.opsForValue().append("msg","追加内容");
   stringTemplate.opsForValue().get("msg");
   
   ```

5. 自定义对象序列化规则

   ```java
   //在传输对象时要对对应的javabean序列化，不过redisTemplate自带的序列化是jdk的
   //我们要在config下创建一个自定义规则
   @Configuration
   public class RedisConfig {
       @Bean
       public RedisTemplate<Object, Employee> 			                   redisTemplate(RedisConnectionFactory redisConnectionFactory) throws UnknownHostException {
           RedisTemplate<Object,Employee> template = new RedisTemplate<>();
           template.setConnectionFactory(redisConnectionFactory);
           Jackson2JsonRedisSerializer<Employee> ser = new               Jackson2JsonRedisSerializer<Employee>(Employee.class);
           template.setDefaultSerializer(ser);
           return template;
       }
   }
   
   //在测试类中导入自定义的template
   @Autowired
   RedisTemplate<Object, Employee> redisTemplate2;//使用自定义的json序列化
   Employee employeeById = em.getEmployeeById(1);
   redisTemplate2.opsForValue().set("EmpId1",employeeById);
   ```

6. redis和cache搭配

   ```JAVA
   //只要导入了redis的dependency依赖,cache的存储位置自动就变成了redis数据库而不是jvm内存
   //redis的连接地址记得要在properties中配置:spring.redis.host=10.69.17.30
   //2.0以下redis要以json的方式序列化存储对象：
   //这是之前设置的对象json序列化模板
   @Bean
   public RedisTemplate<Object, Employee> redisTemplate(RedisConnectionFactory redisConnectionFactory) throws UnknownHostException {
       RedisTemplate<Object, Employee> template = new RedisTemplate<>();
       template.setConnectionFactory(redisConnectionFactory);
       Jackson2JsonRedisSerializer<Employee> ser = new Jackson2JsonRedisSerializer<Employee>(Employee.class);
       template.setDefaultSerializer(ser);
       return template;
   }
   //引用上面的模板
   @Bean
   public RedisCacheManager employeeCacheManager( RedisTemplate<Object, Employee> redisTemplate){
       RedisCacheManager cacheManager = new RedisCacheManager(redisTemplate);
       cacheManager.setUsePrefix(true);
       return cacheManager;
   }
   //在spring2.0以后redis的rediscachemanager发生了改变,上述方法无法使用，待后续研究
   //当存在需要序列化多个对象时要在config中配置新的rediscachemanager,再在service中配置
   //还不如直接用jdk的算了
   ```

   