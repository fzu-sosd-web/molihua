---
authors:
  - flyingpig 
date: 2024-02-03
tags:
  - 中间件
categories:
  - 中间件
---

<meta name="referrer" content="no-referrer"/>

一. 初识 MQ
--------

### 1. 同步和异步通讯

同步通讯：就像打电话，需要实时响应。  
异步通讯：就像发邮件，不需要马上回复。

我们之前学习的 Feign 调用就属于同步方式，虽然调用可以实时得到结果，但存在下面的问题：

> 同步调用的优点：
> 
> - 时效性较强，可以立即得到结果
> 
> 同步调用的问题：
> 
> - 耦合度高  
> - 性能和吞吐能力下降  
> - 有额外的资源消耗  
> - 有级联失败问题

异步调用则可以避免上述问题：

![](https://img-blog.csdnimg.cn/direct/f88e58a5eec74e879dcd297c8f788fea.png)

**【1】异步调用简单说就是将一件事拆为两部分，把应该立马做完的【基础部分】先做完，然后剩下的事情【bonus】留到后台慢慢完成。**

**举个例子，我们赶 ddl 的时候，先把必须完成的基础部分先做完提交，然后如果想要继续优化项目在在后面慢慢优化。**

**就像上面那样，把基础部分做完后返回正确结果，将剩下的任务发布到 Broker 中由接收方在后台慢慢完成。**

**这里的 Broker 一般使用的是现在比较常用，比较成熟的技术 message queue，即消息队列。**

**【2】同时，如果是异步的话，交给后台处理的那部分任务，你还可以定义多个接收方 (消费者) 对任务进行处理，提高处理速度。**

![](https://img-blog.csdnimg.cn/direct/2a67319fb06a497da04af3833a6e8dca.png)

**以打电话和发邮件为例，两种方式各有优劣，打电话可以立即得到响应，但是你却不能跟多个人同时通话。发送邮件可以同时与多个人收发邮件，但是往往响应会有延迟。**

> 好处：
> 
> - 吞吐量提升：无需等待订阅者处理完成，响应更快速
> 
> - 故障隔离：服务没有直接调用，不存在级联失败问题  
> - 调用间没有阻塞，不会造成无效的资源占用  
> - 耦合度极低，每个服务都可以灵活插拔，可替换  
> - 流量削峰：不管发布事件的流量波动多大，都由 Broker 接收，订阅者可以按照自己的速度去处理事件
> 
> 缺点：
> 
> - 架构复杂了，业务没有明显的流程线，不好管理  
> - 需要依赖于 Broker 的可靠、安全、性能

### 2. 技术对比

常见的 mq:  
ActiveMQ        RabbitMQ        RocketMQ        Kafka

![](https://img-blog.csdnimg.cn/direct/d8513ddf0a8547b28af583d43f5cf869.png)

这里我们讲解 RabbitMQ.

二. Springboot 快速集成 RabbitMQ 实现消息队列基础模型
--------------------------------------

基础模型：

![](https://img-blog.csdnimg.cn/direct/59b4d9048ad54ead8f3dba3ac787c760.png)

技术选型：SpringAMQP 依赖

rabbitmq 安装：略

### 0. 场景

修改的接口：

```
    @PostMapping
    @ApiOperation("上传音乐")
    public Result addMusic(@RequestHeader String Authorization,
                           @RequestParam String name, @RequestParam String introduce, @RequestParam String singerName,
                           @RequestParam MultipartFile coverFile ,@RequestParam MultipartFile musicFile)throws IOException {
        if(!MultipartFileUtil.isMusicFile(musicFile)||!MultipartFileUtil.isImageFile(coverFile)){
            return Result.error(500,"文件类型错误");
        }
        Music music=new Music(null,name,introduce,null,null,null,null,null,singerName);
        music.setLikeNum(Long.parseLong("0"));
        music.setCollectNum(Long.parseLong("0"));
        music.setUploadUser(Long.parseLong(JwtUtil.parseJwt(Authorization).getSubject()));
        music.setCoverPath(aliOSSUtils.upload(coverFile));
        music.setMusicPath(aliOSSUtils.upload(musicFile));
        musicService.addMusic(music);
        return Result.success();
    }
```

因为音乐是要进行审核的，所以不用立马发表【不要求实时性】。另外上传到阿里云的时间较长，所以我们可以把上传音乐文件到阿里云和添加音乐信息到数据库的步骤放到消息队列里面去执行。

### 1. 引入依赖并进行配置

依赖

```
<!--AMQP依赖，包含RabbitMQ-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-amqp</artifactId>
</dependency>
```

配置类（也可以写着 yml 中）

```
@Configuration
public class RabbitMQConfig {
 
    private final String host = "111.229.173.12";
    private final int port = 5672;
    private final String virtualHost = "/";
    private final String username = "flyingpig";
    private final String password = "Aa123456";
 
    @Bean
    public CachingConnectionFactory connectionFactory() {
        CachingConnectionFactory factory = new CachingConnectionFactory(host);
        factory.setPort(port);
        factory.setVirtualHost(virtualHost);
        factory.setUsername(username);
        factory.setPassword(password);
        return factory;
    }
 
    @Bean
    public RabbitTemplate rabbitTemplate() {
        RabbitTemplate template = new RabbitTemplate(connectionFactory());
        return template;
    }
}
```

### 2. 编写消费者

编写消费者将上传阿里云 OSS 和写入数据库的部分放在其中。

```
@Component
@Slf4j
public class MusicUploadListener {
 
    @Autowired
    private MusicService musicService;
    @Autowired
    AliOSSUtils aliOSSUtils;
    @RabbitListener(queues = "music_queue")
    public void handleMusicUploadRequest(MusicUploadMessage request) throws IOException {
        // 处理文件上传请求
        try {
            String coverPath = aliOSSUtils.upload(request.getCoverFile());
            String musicPath = aliOSSUtils.upload(request.getMusicFile());
            request.getMusic().setCoverPath(coverPath);
            request.getMusic().setMusicPath(musicPath);
            musicService.addMusic(request.getMusic());
        } catch (Exception e) {
            // 异常处理
            log.error("处理音乐上传请求失败");
            throw new AmqpRejectAndDontRequeueException("处理音乐上传请求失败，将消息丢弃");
        }
    }
}
```

这里参数 MusicUpload 是由发送方传递过来的参数（具体定义）：

```
@Data
@NoArgsConstructor
@AllArgsConstructor
public class MusicUploadMessage implements Serializable {
    private Music music;
    private byte[] coverData;
    private byte[] musicData;
 
    public MusicUploadMessage(Music music, MultipartFile coverFile, MultipartFile musicFile) throws IOException {
        this.music = music;
        this.coverData = coverFile.getBytes();
        this.musicData = musicFile.getBytes();
    }
 
    public MultipartFile getCoverFile() {
        String coverFileName = generateRandomFileName("cover.jpg");
        return new MockMultipartFile(coverFileName, coverFileName, "image/jpeg", coverData);
    }
 
    public MultipartFile getMusicFile() {
        String musicFileName = generateRandomFileName("music.mp3");
        return new MockMultipartFile(musicFileName, musicFileName, "audio/mpeg", musicData);
    }
 
    private String generateRandomFileName(String originalFileName) {
        String extension = originalFileName.substring(originalFileName.lastIndexOf('.'));
        return UUID.randomUUID().toString() + extension;
    }
}
```

**注意消息队列在传递参数的时候要将参数序列化，比如上面的类就 implements Serializable。**

**同时里面属性如果有类也要序列化。Music 类也要 implements Serializable，因为 MultipartFile 无法序列化，所以将其转为 byte[] 字符数组这种类型传递。**

### 3. 编写发送者

```
    @Autowired
    RabbitTemplate rabbitTemplate;
 
    @PostMapping
    @ApiOperation("上传音乐")
    public Result addMusic(@RequestParam String name, @RequestParam String introduce, @RequestParam String singerName,
                           @RequestParam MultipartFile coverFile, @RequestParam MultipartFile musicFile) throws IOException {
        if (!MultipartFileUtil.isMusicFile(musicFile) || !MultipartFileUtil.isImageFile(coverFile)) {
            return Result.error(500, "文件类型错误");
        }
        System.out.println(coverFile.getOriginalFilename());
        Music music = new Music(null, name, introduce, null, null, null, null, null, singerName);
        music.setLikeNum(Long.parseLong("0"));
        music.setCollectNum(Long.parseLong("0"));
        music.setUploadUser(UserContext.getUserId());
        // 将文件信息发送到RabbitMQ队列中
        rabbitTemplate.convertAndSend("music_queue", new MusicUploadMessage(music, coverFile, musicFile));
        return Result.success();
    }
```

三. WorkQueue 任务模型
-----------------

### 1. 什么是任务模型

Work queues，也被称为（Task queues），任务模型。简单来说就是 ** 让多个消费者绑定到一个队列，共同消费队列中的消息。

![](https://img-blog.csdnimg.cn/direct/63b153077fd94fe3a26ac1529df8a688.png)

当消息处理比较耗时的时候，可能生产消息的速度会远远大于消息的消费速度。长此以往，消息就会堆积越来越多，无法及时处理。

此时就可以使用 work 模型，多个消费者共同处理消息处理，速度就能大大提高了。

### 2. 实现

只要将之前接收方 / 消费者的代码复制一下改下方法名就可以了。

```
@Component
@Slf4j
public class MusicUploadListener {
 
    @Autowired
    private MusicService musicService;
    @Autowired
    AliOSSUtils aliOSSUtils;
    @RabbitListener(queues = "music_queue")
    public void handleMusicUploadRequest1(MusicUploadMessage request) throws IOException {
        // 处理文件上传请求
        try {
            String coverPath = aliOSSUtils.upload(request.getCoverFile());
            String musicPath = aliOSSUtils.upload(request.getMusicFile());
            request.getMusic().setCoverPath(coverPath);
            request.getMusic().setMusicPath(musicPath);
            musicService.addMusic(request.getMusic());
        } catch (Exception e) {
            // 异常处理
            log.error("处理音乐上传请求失败");
            throw new AmqpRejectAndDontRequeueException("处理音乐上传请求失败，将消息丢弃");
        }
    }
 
    @RabbitListener(queues = "music_queue")
    public void handleMusicUploadRequest2(MusicUploadMessage request) throws IOException {
        // 处理文件上传请求
        try {
            String coverPath = aliOSSUtils.upload(request.getCoverFile());
            String musicPath = aliOSSUtils.upload(request.getMusicFile());
            request.getMusic().setCoverPath(coverPath);
            request.getMusic().setMusicPath(musicPath);
            musicService.addMusic(request.getMusic());
        } catch (Exception e) {
            // 异常处理
            log.error("处理音乐上传请求失败");
            throw new AmqpRejectAndDontRequeueException("处理音乐上传请求失败，将消息丢弃");
        }
    }
}
```

现在就有了两个消费者。

### 3. 能者多劳

消息是平均分配给每个消费者，并没有考虑到消费者的处理能力。这样显然是有问题的。

所以我们可以添加配置，让每个消费者处理完自己的消息后采取获取新的消息，这样就实现了按能力分配。

```
spring:
  rabbitmq:
    listener:
      music_queue:
        prefetch: 1 # 每次只能获取一条消息，处理完成才能获取下一个消息
```

四. 发布模型 / 订阅模型
--------------

发布订阅的模型如图：

![](https://img-blog.csdnimg.cn/direct/4b3ab79a0d1a4e4886e11de75c4b553e.png)

可以看到，在订阅模型中，多了一个 exchange 角色，而且过程略有变化：  
**Publisher：生产者**，也就是要发送消息的程序，**但是不再发送到队列中，而是发送给交换机**  
**Exchange：交换机。**一方面，接收生产者发送的消息。另一方面，知道如何处理消息，例如递交给某个特别队列、递交给所有队列、或是将消息丢弃。到底如何操作，取决于 Exchange 的类型。**Exchange 有以下 3 种类型：  
        Fanout：广播，将消息交给所有绑定到交换机的队列  
        Direct：定向，把消息交给符合指定 routing key 的队列  
        Topic：通配符，把消息交给符合 routing pattern（路由模式） 的队列**  
Consumer：消费者，与以前一样，订阅队列，没有变化  
**Queue：消息队列**也与以前一样，接收消息、缓存消息，**但是我们需要让消息队列和交换机绑定。**

**Exchange（交换机）只负责转发消息，不具备存储消息的能力，因此如果没有任何队列与 Exchange 绑定，或者没有符合路由规则的队列，那么消息会丢失！**

### 1.Fanout

Fanout，英文翻译是扇出，我觉得在 MQ 中叫广播更合适。

![](https://img-blog.csdnimg.cn/direct/6aa6a1eb064d48a6b9c453bbf2b030a8.png)

> 在广播模式下，消息发送流程是这样的：  
> 1） 可以有多个队列  
> 2） 每个队列都要绑定到 Exchange（交换机）  
> 3） 生产者发送的消息，只能发送到交换机，交换机来决定要发给哪个队列，生产者无法决定  
> 4） 交换机把消息发送给绑定过的所有队列  
> 5） 订阅队列的消费者都能拿到消息

修改之前的代码：

#### 1. 注册交换机和队列并将交换机和队列绑定

下面注册了一个交换机和两个队列

```
@Configuration
public class MusicUploadMQConfig {
    //交换机
 
 
    @Bean
    public FanoutExchange fanoutExchange(){
        return new FanoutExchange(MUSIC_UPLOAD_EXCHANGE_NAME);
    }
 
    //队列1
 
 
    @Bean
    public Queue fanoutQueue1() {
        return new Queue(MUSIC_UPLOAD_QUEUE_NAME1);
    }
 
    /**
     * 绑定队列1和交换机
     */
    @Bean
    public Binding bindingQueue1(Queue fanoutQueue1, FanoutExchange fanoutExchange){
        return BindingBuilder.bind(fanoutQueue1).to(fanoutExchange);
    }
 
    //队列2
 
 
    @Bean
    public Queue fanoutQueue2() {
        return new Queue(MUSIC_UPLOAD_QUEUE_NAME2);
    }
 
    /**
     * 绑定队列2和交换机
     */
    @Bean
    public Binding bindingQueue2(Queue fanoutQueue2, FanoutExchange fanoutExchange){
        return BindingBuilder.bind(fanoutQueue2).to(fanoutExchange);
    }
 
}
```

**注：这里为了方便管理交换机名和方法名我把名字都提取到了一个常量类中：**

```
public class RabbitMQConstants {
    public static final String MUSIC_UPLOAD_EXCHANGE_NAME = "music_upload_exchange";
    public static final String MUSIC_UPLOAD_QUEUE_NAME1 = "music_queue1";
    public static final String MUSIC_UPLOAD_QUEUE_NAME2 = "music_queue2";
 
}
```

#### 2. 将发送发从向队列发送消息改为向交换机发送消息

原来：

```
// 将文件信息发送到RabbitMQ队列中
rabbitTemplate.convertAndSend("music_queue", new MusicUploadMessage(music, coverFile, musicFile));
```

现在：

```
// 将文件信息发送到RabbitMQ交换机中
rabbitTemplate.convertAndSend(MUSIC_UPLOAD_EXCHANGE_NAME,"", new MusicUploadMessage());
```

**注：发送到队列是两个参数，发送到交换机是三个参数。  
发送到交换机的第一个参数是交换机名称，第二个参数是路由 key，但是这里用不到。**

#### 3. 修改接收者类给每个队列配一个接受者

```
@Component
@Slf4j
public class MusicUploadListener {
 
    @Autowired
    private MusicService musicService;
    @Autowired
    AliOSSUtils aliOSSUtils;
 
    @RabbitListener(queues = MUSIC_UPLOAD_QUEUE_NAME1)
    public void handleMusicUploadRequest1(MusicUploadMessage request) throws IOException {
        //处理文件上传请求
        try {
            String coverPath = aliOSSUtils.upload(request.getCoverFile());
            String musicPath = aliOSSUtils.upload(request.getMusicFile());
            request.getMusic().setCoverPath(coverPath);
            request.getMusic().setMusicPath(musicPath);
            musicService.addMusic(request.getMusic());
        } catch (Exception e) {
            // 异常处理
            log.error("处理音乐上传请求失败");
            throw new AmqpRejectAndDontRequeueException("处理音乐上传请求失败，将消息丢弃");
        }
    }
 
    @RabbitListener(queues = MUSIC_UPLOAD_QUEUE_NAME2)
    public void handleMusicUploadRequest2(MusicUploadMessage request) throws IOException {
 
        // 处理文件上传请求
        try {
            String coverPath = aliOSSUtils.upload(request.getCoverFile());
            String musicPath = aliOSSUtils.upload(request.getMusicFile());
            request.getMusic().setCoverPath(coverPath);
            request.getMusic().setMusicPath(musicPath);
            musicService.addMusic(request.getMusic());
        } catch (Exception e) {
            // 异常处理
            log.error("处理音乐上传请求失败");
            throw new AmqpRejectAndDontRequeueException("处理音乐上传请求失败，将消息丢弃");
        }
    }
}
```

### 2.Direct

在 Fanout 模式中，一条消息，会被所有订阅的队列都消费。但是，在某些场景下，我们希望不同的消息被不同的队列消费。这时就要用到 Direct 类型的 Exchange。

![](https://img-blog.csdnimg.cn/direct/1104333fe8c34de1ba8459ef5dc4cab9.png)

在 Direct 模型下：

- 队列与交换机的绑定，不能是任意绑定了，而是要指定一个 `RoutingKey`（路由 key）  
- 消息的发送方在 向 Exchange 发送消息时，也必须指定消息的 `RoutingKey`。  
- Exchange 不再把消息交给每一个绑定的队列，而是根据消息的 `Routing Key` 进行判断，只有队列的 `Routingkey` 与消息的 `Routing key` 完全一致，才会接收到消息

#### 1. 修改队列的类型和路由 key

**基于 @Bean 的方式声明队列和交换机比较麻烦，Spring 还提供了基于注解方式来声明。**

**在 consumer 的 SpringRabbitListener 中添加两个消费者，同时基于注解来声明队列和交换机。**

```
@RabbitListener(bindings = @QueueBinding(
    value = @Queue(name = "direct.queue1"),
    exchange = @Exchange(name = "flyingpig.direct", type = ExchangeTypes.DIRECT),
    key = {"red", "blue"}
))
public void listenDirectQueue1(String msg){
    System.out.println("消费者接收到direct.queue1的消息：【" + msg + "】");
}
 
@RabbitListener(bindings = @QueueBinding(
    value = @Queue(name = "direct.queue2"),
    exchange = @Exchange(name = "flyingpig.direct", type = ExchangeTypes.DIRECT),
    key = {"red", "yellow"}
))
public void listenDirectQueue2(String msg){
    System.out.println("消费者接收到direct.queue2的消息：【" + msg + "】");
}
```

#### 2. 消息发送时指定路由 key

```
@Test
public void testSendDirectExchange() {
    // 交换机名称
    String exchangeName = "flyingpig.direct";
    // 消息
    String message = "红色警报！日本乱排核废水，导致海洋生物变异，惊现哥斯拉！";
    // 发送消息
    rabbitTemplate.convertAndSend(exchangeName, "red", message);
}
```

**总结：Direct 交换机根据 RoutingKey 判断路由给哪个队列**

### **3.Topic**

**`Topic` 类型的 `Exchange` 与 `Direct` 相比，都是可以根据 `RoutingKey` 把消息路由到不同的队列。只不过 `Topic` 类型 `Exchange` 可以让队列在绑定 `Routing key` 的时候使用通配符！**

**`Routingkey` 一般都是有一个或多个单词组成，多个单词之间以”.” 分割，例如： `item.insert`**

>  **通配符规则：**
> 
> **`#`：匹配一个或多个词**
> 
> **`*`：匹配不多不少恰好 1 个词**
> 
> **举例：**
> 
> **`item.#`：能够匹配 `item.spu.insert` 或者 `item.spu`**
> 
> **`item.*`：只能匹配 `item.spu`**

![](https://img-blog.csdnimg.cn/direct/8d337335a3704f1d94ccc3bd934a47ba.png)

#### 1. 修改消息队列的类型和路由 key

```
@RabbitListener(bindings = @QueueBinding(
    value = @Queue(name = "topic.queue1"),
    exchange = @Exchange(name = "flyingpig.topic", type = ExchangeTypes.TOPIC),
    key = "china.#"
))
public void listenTopicQueue1(String msg){
    System.out.println("消费者接收到topic.queue1的消息：【" + msg + "】");
}
 
@RabbitListener(bindings = @QueueBinding(
    value = @Queue(name = "topic.queue2"),
    exchange = @Exchange(name = "flyingpig.topic", type = ExchangeTypes.TOPIC),
    key = "#.news"
))
public void listenTopicQueue2(String msg){
    System.out.println("消费者接收到topic.queue2的消息：【" + msg + "】");
}
```

#### 2. 发送测试

```
/**
     * topicExchange
     */
@Test
public void testSendTopicExchange() {
    // 交换机名称
    String exchangeName = "flyingpig.topic";
    // 消息
    String message = "喜报！孙悟空大战哥斯拉，胜!";
    // 发送消息
    rabbitTemplate.convertAndSend(exchangeName, "china.news", message);
}
```

**总结：Topic 交换机根据通配符 RoutingKey 判断路由给哪个队列**

五. 消息转化器
--------

Spring 默认使用的序列化方式是 jdk 序列化。

JDK 序列化存在下列问题：  
数据体积过大  
有安全漏洞  
可读性差

我们将其改为 json 序列化。

引入依赖：

```
<dependency>
    <groupId>com.fasterxml.jackson.dataformat</groupId>
    <artifactId>jackson-dataformat-xml</artifactId>
    <version>2.9.10</version>
</dependency>
```

添加 Bean:

```
@Bean
public MessageConverter jsonMessageConverter(){
    return new Jackson2JsonMessageConverter();
}
```