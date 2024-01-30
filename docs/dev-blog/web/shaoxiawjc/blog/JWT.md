# JWT(JSON Web Token)

# JWT的结构

令牌组成

* 标头head
* 有效负载payload
* 签名signature

head.payload.signature

> header

包含俩部分，令牌的类型和所使用的签名算法

同时对这俩部分使用一个Base64编码（可解码）使之成为字符串

> payload

有效负载，包含声明，即有关实体（通常是用户实体）和其他数据的声明

同样的，它也会被base64编码

尽量不要放用户的敏感信息（密码之类的）

> signature

通过编码后的header和payload加上一个密匙（随机盐）

使用header里提供的算法进行签名

目的是确保header和payload没有被修改过，防止内容被篡改



# 使用jwt

## 引入依赖

```xml
<dependency>
    <groupId>com.auth0</groupId>
    <artifactId>java-jwt</artifactId>
    <version>3.4.0</version>
</dependency>
```

## 测试

测试生成token代码

```java
@Test
void contextLoads() {
    HashMap<String, Object> map = new HashMap<>();

    Calendar instance = Calendar.getInstance();
    instance.add(Calendar.SECOND,20); // 20秒
    String token = JWT.create()
          .withHeader(map) // 修改header
          .withClaim("userId", 123) // 设置payload也就是声明
          .withClaim("username", "shaoxia")
          .withExpiresAt(instance.getTime()) // 指定令牌的过期时间
          .sign(Algorithm.HMAC256("#!(jjQHIHOhjioeeigo"));// 设置签名

    System.out.println(token);
}
```

生成结果

```markdown
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.
eyJleHAiOjE3MDUxNTgwMTcsInVzZXJJZCI6MTIzLCJ1c2VybmFtZSI6InNoYW94aWEifQ.
y350Y9GTkaat6IUmAAeM3vv52M2_TWPPSRqohSA7IkI
```

---

测试验证token的代码

```java
@Test
void test02(){
    // 创建验证对象
    JWTVerifier jwtVerifier = JWT.require(Algorithm.HMAC256("#!(jjQHIHOhjioeeigo")).build();
    DecodedJWT verify = jwtVerifier.verify("eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHAiOjE3MDUxNTg5NzgsInVzZXJJZCI6MTIzLCJ1c2VybmFtZSI6InNoYW94aWEifQ.XGllQPvYbllEtmvPCK841iHzMbm0USJHnwMNE2bSmFw");
    // 要把类型指对
    Integer userId = verify.getClaim("userId").asInt();
    String username = verify.getClaim("username").asString();
    System.out.println(userId+"\n"+username);
}
```

测试结果

```markdown
123
shaoxia
```

常见异常

> SignatureVerificationException 签名异常
>
> AlgorithmMismatchException 算法异常
>
> TokenExpiredException 过期异常



## jwt常用类

```java
// 密匙
private static final String SIGNATURE = "!@#$%^&DGUYIVEIUV@^^&&*^&&^&%JKUUJEBV";

/**
 * token的生成
 * */
public static String getToken(Map<String,String> map){
    Calendar instance = Calendar.getInstance();
    instance.add(Calendar.DATE,14); // 设置14天过期

    JWTCreator.Builder builder = JWT.create();

    // 设置声明里的信息
    map.forEach((k,v)->{
       builder.withClaim(k,v);
    });

    // 设置过期时间和签名的算法
    String token = builder.withExpiresAt(instance.getTime())
          .sign(Algorithm.HMAC256(SIGNATURE));

    return token;
}

/**
 * 验证token并返回token信息
 * token要是不合法该方法就会抛出异常
 * */
public static DecodedJWT verify(String token){
    return JWT.require(Algorithm.HMAC256(SIGNATURE)).build().verify(token);
}
```



## jwt整合springboot

业务层

```java
@Override
public String loginToken(String name,String password) {
    QueryWrapper<User> wrapper = new QueryWrapper<>();
    wrapper.select("id","name");
    wrapper.eq("name",name)
          .eq("password",password);
    User userDB = userMapper.selectOne(wrapper);
    if (userDB == null){
       throw new RuntimeException("登录失败---");
    }

    HashMap<String, String> payload = new HashMap<>();
    payload.put("userId",userDB.getId().toString());
    payload.put("name",userDB.getName());
    String token = JwtUtils.getToken(payload);

    return token;
}
```

控制层

```java
@RestController
public class UserController {
    @Autowired
    private UserService userService;

    @PostMapping("/user/login")
    public ResponseEntity<Map<String, Object>> userLogin(String name, String password){
       HashMap<String, Object> map = new HashMap<>();
       try {
          String token = userService.loginToken(name, password);
          map.put("status",true);
          map.put("token",token);
       }catch (Exception e){
          map.put("status",false);
          map.put("token",null);
       }
       return ResponseEntity.ok(map);
    }
}
```

在实际开发里，如果有多个接口都需要token,我们可以使用拦截器进行统一管理，在分布式系统里，我们可以在网关进行操作

后端在登录成功后会返回给前端token后，前端负责把token存储，并且在每次请求里都附带token

>  拦截器

```java
@Override
public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
    HashMap<String, Object> map = new HashMap<>();
    // 获取请求头里的令牌
    String token = request.getHeader("token");
    //
    try {
       DecodedJWT verify = JwtUtils.verify(token);
       return true;
    }catch (SignatureVerificationException e){
       e.printStackTrace();
       map.put("msg","无效签名");
    }catch (AlgorithmMismatchException e){
       e.printStackTrace();
       map.put("msg","算法错误");
    }catch (TokenExpiredException e){
       e.printStackTrace();
       map.put("msg","token过期");
    }catch (Exception e){
       e.printStackTrace();
       map.put("msg","token异常");
    }
    map.put("status",false);
    // 将map转化为json放到respond里
    String jsonString = JSON.toJSONString(map);
    response.setContentType("application/json;charset=UTF-8");
    response.getWriter().println(jsonString);
    return false;
}
```

> 拦截器配置

```java
@Override
public void addInterceptors(InterceptorRegistry registry) {
    registry.addInterceptor(new MyWebInterceptor())
          .addPathPatterns("/**")
          .excludePathPatterns("/user/login");
}
```





















































































