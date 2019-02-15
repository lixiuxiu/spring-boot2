# Spring boot + restful

1，创建一个springboot+maven的项目，并且可以跑起来，pom.xml是maven的配置文件。

```
<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>
</dependencies>
```

一开始是这样的，有spring-boot-starter-web和spring-boot-starter-test两个依赖

2 （optional）如果想要解析json串，在pom.xml里面加入依赖

```
<dependency>
			<groupId>com.jayway.jsonpath</groupId>
			<artifactId>json-path</artifactId>
			<scope>test</scope>
</dependency>
```

JsonPath提供的json解析非常强大，它提供了类似正则表达式的语法，基本上可以满足所有你想要获得的json内容。

但是不加这个依赖，下面的也能照常运行返回json。这是因为Spring’s HTTP message converter support。Spring’s MappingJackson2HttpMessageConverter自动的去把返回的类转换成json。

3，编写实体类，先创建一个包model，然后创建一个Greet类

```
package com.model;

public class Greeting {

        private final long id;
        private final String content;

        public Greeting(long id, String content) {
            this.id = id;
            this.content = content;
        }

        public long getId() {
            return id;
        }

        public String getContent() {
            return content;
        }

    }
```

在control里面返回值就使用greet类，然而浏览器中显示的是json

4，创建control包，然后创建一个GreetController类

```
@RestController
public class GreetController {

    public static final String template="hello, %s";
    private final AtomicLong counter = new AtomicLong(); 
    //AtomicLong是作用是对长整形进行原子操作

    @RequestMapping("/greeting")
    public Greeting greeting(@RequestParam(value="name", defaultValue="World") String name){

        return new Greeting(counter.incrementAndGet(),   //每次加1
                String.format(template, name));
    }
}
```

@RestController将该类标注为一个spring bean，并标记其中的每个方法返回一个实体对象。相当于@Controller+@ResponsBody。

@RequestMapping提供url和方法的映射，当接收到一个/greeting的请求时，能执行greeting()方法。加上mothoed参数写成 @RequestMapping(method=GET)，这样只会接收到GET请求。

@RequestParam通过指定的value绑定**url中的参数**。本例中，url参数是可选的，因为提供了默认值“World”。

5，在浏览器中输入 http://localhost:8080/greeting?name=User

 浏览器页面显示

```
{"id":1,"content":"hello, User"}
```



# Spring boot + Scheduling Tasks

四个注解小知识：

1.initialDelay :初次执行任务之前需要等待的时间

```
@Scheduled(initialDelay =5000)
public void doSomething() {

} 
```

2.fixedDelay:每次执行任务之后间隔多久再次执行该任务。

```
@Scheduled(fixedDelay=5000)
public void doSomething() {
    // something that should execute periodically
}
```

3.fixedRate:执行频率，每隔多少时间就启动任务，不管该任务是否启动完成

```
@Scheduled(fixedRate=5000)
public void doSomething() {

}
```

4.@Scheduled(cron=". . .")

"0 0 * * * *" = the top of every hour of every day."*/10 * * * * *" = every ten seconds.

"0 0 8-10 * * *" = 8, 9 and 10 o'clock of every day.

"0 0 6,19 * * *" = 6:00 AM and 7:00 PM every day.

"0 0/30 8-10 * * *" = 8:00, 8:30, 9:00, 9:30, 10:00 and 10:30 every day.

"0 0 9-17 * * MON-FRI" = on the hour nine-to-five weekdays

"0 0 0 25 12 ?" = every Christmas Day at midnight



我们创建一个类

```
package com.demo;

import java.text.SimpleDateFormat;
import java.util.Date;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.stereotype.Component;

@Component
public class ScheduledTasks {
    //Logger类打印日志
    private static final Logger log = LoggerFactory.getLogger(ScheduledTasks.class);
    private static final SimpleDateFormat dateFormat = new SimpleDateFormat("HH:mm:ss");

    @Scheduled(fixedRate = 5000)    //Scheduled 定义了什么时候运行下面方法，fixedRate定义了执行的时间间隔
    public void reportCurrentTime() {
        log.info("The time is now {}", dateFormat.format(new Date()));
    }
}
```



在有主函数的类中加入：@EnableScheduling

```
@SpringBootApplication
@EnableScheduling
public class TestApplication {

   public static void main(String[] args) {
      SpringApplication.run(TestApplication.class, args);
   }
}
```

结果在固定的时间间隔下，日志输出：The time is now 14:24:41。



# Spring boot+devtools

**1 在pom.xml里加入**

```
<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <optional>true</optional>
</dependency>

```

还有下面的配置项

```
<build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <fork>true</fork>
                </configuration>
            </plugin>
        </plugins>
</build>

```

如果没有该项配置，devtools不会起作用，即应用不会restart 

```
<configuration>
         <fork>true</fork>
</configuration>
```

**2 更改idea配置**

　　1） “File” -> “Settings” -> “Build,Execution,Deplyment” -> “Compiler”，选中打勾 “Build project automatically” 。

　　2） 组合键：“Shift+Ctrl+Alt+/” ，mac是“shift+command+option+/” 选择 “Registry” ，选中打勾 “compiler.automake.allow.when.app.running” 。

**3 Chrome禁用缓存**

　　F12或者“Ctrl+Shift+I”，打开开发者工具，“Network” 选项卡下 选中打勾 “Disable Cache(while DevTools is open)” 



然后要改变之后要点击build按钮重新编译，不然没有变化的说



# Spring boot+swagger

 1，在pom.xml文件中添加swagger相关依赖

```
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger2</artifactId>
    <version>2.4.0</version>
</dependency>

<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger-ui</artifactId>
    <version>2.4.0</version>
</dependency>
```

2, SwaggerConfig 类

```
@Configuration
@EnableSwagger2
public class SwaggerConfig {
    @Bean
    public Docket api() {
        return new Docket(DocumentationType.SWAGGER_2).select().apis(RequestHandlerSelectors.any())
                .paths(PathSelectors.any()).build();
    }
}
```

3,访问http://localhost:9090/swagger-ui.html



# application.properties

1，在resource中的application.properties 可以更改访问端口，如果application.properties没有配置server.name那么默认值将是 Windows

```
server.port=9090
server.name=Linux
server.host=192.168.0.1,172.16.0.1
```





# Spring boot+JDBC+mysql

1，建立mysql数据库，和表

sys_user

```
DROP TABLE IF EXISTS `sys_user`;
CREATE TABLE `sys_user`  (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `name` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL,
  `password` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL,
  PRIMARY KEY (`id`) USING BTREE
) ENGINE = InnoDB AUTO_INCREMENT = 3 CHARACTER SET = utf8 COLLATE = utf8_general_ci ROW_FORMAT = Dynamic;


-- ----------------------------
-- Records of sys_user
-- ----------------------------
INSERT INTO `sys_user` VALUES (1, 'admin', '123');
INSERT INTO `sys_user` VALUES (2, 'user', '123');
```

sys_role

```
DROP TABLE IF EXISTS `sys_role`;
CREATE TABLE `sys_role`  (
  `id` int(11) NOT NULL,
  `name` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL,
  PRIMARY KEY (`id`) USING BTREE
) ENGINE = InnoDB CHARACTER SET = utf8 COLLATE = utf8_general_ci ROW_FORMAT = Dynamic;


-- ----------------------------
-- Records of sys_role
-- ----------------------------
INSERT INTO `sys_role` VALUES (1, 'ROLE_ADMIN');
INSERT INTO `sys_role` VALUES (2, 'ROLE_USER');

```

sys_user_role

```

DROP TABLE IF EXISTS `sys_user_role`;
CREATE TABLE `sys_user_role`  (
  `user_id` int(11) NOT NULL,
  `role_id` int(11) NOT NULL,
  PRIMARY KEY (`user_id`, `role_id`) USING BTREE,
  INDEX `fk_role_id`(`role_id`) USING BTREE,
  CONSTRAINT `fk_role_id` FOREIGN KEY (`role_id`) REFERENCES `sys_role` (`id`) ON DELETE CASCADE ON UPDATE CASCADE,
  CONSTRAINT `fk_user_id` FOREIGN KEY (`user_id`) REFERENCES `sys_user` (`id`) ON DELETE CASCADE ON UPDATE CASCADE
) ENGINE = InnoDB CHARACTER SET = utf8 COLLATE = utf8_general_ci ROW_FORMAT = Dynamic;


-- ----------------------------
-- Records of sys_user_role
-- ----------------------------
INSERT INTO `sys_user_role` VALUES (1, 1);
INSERT INTO `sys_user_role` VALUES (2, 2);
```

2，pom.xml中加入

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jdbc</artifactId>
</dependency>
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.21</version>
</dependency>
```

3，application.properties中加入数据库配置，我的数据库名称叫lxx,  mysql8还涉及了ssh和一些安全加密的问题，

```
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/lxx?characterEncoding=utf8&useSSL=true&verifyServerCertificate=false
spring.datasource.username=root
spring.datasource.password=778899
```

4，entity类，注意属性名跟数据库中的对应

```
import java.io.Serializable;

public class SysRole implements Serializable {
    static final long serialVersionUID = 1L;

    private Integer id;

    private String name;

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```

```
public class SysUser implements Serializable {
    static final long serialVersionUID = 1L;

    private Integer id;

    private String name;

    private String password;

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }
}
```

```
import java.io.Serializable;

public class SysUserRole implements Serializable {
    static final long serialVersionUID = 1L;

    private Integer userId;

    private Integer roleId;

    public Integer getUserId() {
        return userId;
    }

    public void setUserId(Integer userId) {
        this.userId = userId;
    }

    public Integer getRoleId() {
        return roleId;
    }

    public void setRoleId(Integer roleId) {
        this.roleId = roleId;
    }
}
```

5，dao

```
@Repository
public class AccountDaoImpl implements AccountDAO {

    @Autowired
    private JdbcTemplate jdbcTemplate;
//
//    @Override
//    public int add(Account account) {
//        return jdbcTemplate.update("insert into account(name, password) values(?, ?)",
//                account.getName(), account.getPassword());
//
//    }
//
//    @Override
//    public int update(Account account) {
//        return jdbcTemplate.update("UPDATE  account SET NAME=? ,password=? WHERE id=?",
//                account.getName(), account.getPassword(), account.getId());
//    }
//
//    @Override
//    public int delete(int id) {
//        return jdbcTemplate.update("DELETE from TABLE account where id=?", id);
//    }

//    @Override
//    public Account findAccountById(int id) {
//        List<Account> list = jdbcTemplate.query("select * from account where id = ?", new Object[]{id}, new BeanPropertyRowMapper(Account.class));
//        if(list!=null && list.size()>0){
//            Account account = list.get(0);
//            return account;
//        }else{
//            return null;
//        }
//    }

    @Override
    public List<Account> findAccountList() {
        List<Account> list = jdbcTemplate.query("select * from account", new Object[]{}, new BeanPropertyRowMapper(Account.class));
        if(list!=null && list.size()>0){
            return list;
        }else{
            return null;
        }
    }
}
```

6，service和control略

# Spring boot+security

基于jdbc，mysql连接，假设完成了jdbc

1，pom.xml中引入依赖

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

2，写一个实现UserDetailsService接口的CustomUserDetailsService类，注解为@service。

里面实现loadUserByUsername的方法，根据用户名找到用户信息，返回系统自带的UserDetails对象，里面存放的是该用户的姓名密码和权限信息。

```
@Service("userDetailsService")
public class CustomUserDetailsService implements UserDetailsService {
    @Autowired
    private SysUserService userService;

    @Autowired
    private SysRoleService roleService;

    @Autowired
    private SysUserRoleService userRoleService;

    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        Collection<GrantedAuthority> authorities = new ArrayList<>();
        // 从数据库中取出用户信息
        SysUser user = userService.selectByName(username);

        // 判断用户是否存在
        if(user == null) {
            throw new UsernameNotFoundException("用户名不存在");
        }

        // 添加权限
        List<SysUserRole> userRoles = userRoleService.listByUserId(user.getId());
        for (SysUserRole userRole : userRoles) {
            SysRole role = roleService.selectById(userRole.getRoleId());
            authorities.add(new SimpleGrantedAuthority(role.getName()));
        }

        // 返回UserDetails实现类
        return new User(user.getName(), user.getPassword(), authorities);
    }
}
```

3，写一个config类 extends  WebSecurityConfigurerAdapter

实现 configure(AuthenticationManagerBuilder auth)方法，把上面得到的用户信息放入auth中，用来实现判断登陆时的账户是否正确。并且在我们新版本中这里密码一定要用passwordEncoder来加密的。这里重写使其不加密。

和configure(HttpSecurity http)方法来根据角色来判断是否有权限访问不同的路径和登录登出默认路径，//antMatchers在这里用的有点僵硬。也可以不用它，直接在control里的requestMapping注解上加上@PreAuthorize("hasRole('ROLE_ADMIN')")来限定只有amdin才能访问这个路径

```
@Configuration
@EnableWebSecurity
@EnableGlobalMethodSecurity(prePostEnabled = true)
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {
    @Autowired
    private CustomUserDetailsService userDetailsService;

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        //重写返回没有加密的密码，正常情况不要重写，直接对其进行加密
        auth.userDetailsService(userDetailsService).passwordEncoder(new PasswordEncoder() {
            @Override
            public String encode(CharSequence charSequence) {
                return charSequence.toString();
            }

            @Override
            public boolean matches(CharSequence charSequence, String s) {
                return s.equals(charSequence.toString());
            }
        });
    }


    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.authorizeRequests()
                .antMatchers("/user/**").hasAnyRole("USER")
                .antMatchers("/admin/**").hasAuthority("ROLE_ADMIN")
                .anyRequest().permitAll()
                .and()
                .formLogin()
                .loginPage("/login")
                .defaultSuccessUrl("/")
                .permitAll()
                .and()
                .logout()   //退出相关
                .permitAll();


        // 关闭CSRF跨域
        http.csrf().disable();
    }

    @Override
    public void configure(WebSecurity web) throws Exception {
        // 设置拦截忽略文件夹，可以对静态资源放行
        web.ignoring().antMatchers("/css/**", "/js/**");
    }
}
```

4，退出登录logout.html

```
<h1>退出登录</h1>

<button onclick="window.location.href='/logout'">退出登录</button>

</body>
</html>
```

在controll类中，一定要是"/logout"这个路径，然后不需要写退出逻辑，点击按钮系统自动退出

```
@RequestMapping(value = "/logout",method = RequestMethod.POST)
public String logout( ){
    return "logout";
}
```

login.html 页面，这里用到thymeleaf，略

```
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml"
      xmlns:th="http://www.thymeleaf.org"
      xmlns:sec="http://www.thymeleaf.org/thymeleaf-extras-springsecurity3">
<head>
    <title>Spring Security Example </title>
</head>
<body>
<div th:if="${param.error}">
    用户名或密码错
</div>
<div th:if="${param.logout}">
    您已注销成功
</div>
<form th:action="@{/login}" method="post">
    <div><label> 用户名 : <input type="text" name="username"/> </label></div>
    <div><label> 密  码 : <input type="password" name="password"/> </label></div>
    <div><input type="submit" value="登录"/></div>
</form>
</body>
</html>
```

contrler中的方法，也不用写登入逻辑

```
//不能指定post方法，会报错
@RequestMapping(value = "/login")
public String login() {
    return "login";
}
```

spring boot+mybatis

```
server:
  port: 8080

spring:
    datasource:
        url: jdbc:mysql://localhost:3306/lxx?characterEncoding=utf8&useSSL=true&verifyServerCertificate=false
        username: root
        password: Lxx19941114
        # 使用druid数据源
        #type: com.alibaba.druid.pool.DruidDataSource
        driver-class-name: com.mysql.jdbc.Driver


## 该配置节点为独立的节点，有很多同学容易将这个配置放在spring的节点下，导致配置无法被识别
mybatis:
  mapper-locations: classpath:mapper/*.xml  #注意：一定要对应mapper映射xml文件的所在路径
  type-aliases-package: com.example.mybatis.model  # 注意：对应实体类的路径

#pagehelper分页插件
pagehelper:
    helperDialect: mysql
    reasonable: true
    supportMethodsArguments: true
    params: count=countSql
```



# Spring boot +AOP

1，在pom.xml中添加aop依赖

```
<!--aop 依赖-->
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-aop</artifactId>
</dependency>
```

2，model包下有user类，get和set略

```
public class User {
    private int id;
    private String username;
    private  String note;
    }
```

3,service包下

```
public interface UserService {
    public void printUser(User user);
}
```

```
@Service
public class UserServiceImpl implements UserService {
    @Override
    public void printUser(User user) {
        if (user == null)
            throw new RuntimeException("检查参数是否为空。。");
        System.out.println("id ="+user.getId());
        System.out.println("name="+user.getUsername());
        System.out.println("note="+user.getNote());

    }
}
```

4，增强类在validator包下，用于为原来的类添加新功能。

```
public interface UserValidator {
    public boolean validate(User user);
}
```

```
public class UserValidatorImpl implements UserValidator{
    @Override
    public boolean validate(User user) {
        System.out.print("引入新的接口："+UserValidator.class.getSimpleName());
        return user != null;
    }
}
```

5，aspect包下是aspect类，重点

```
@Aspect
public class MyAspect {
    //引入新类来增强服务，value指向要争强的目标对象，defaultImpl引入增强功能的类
    @DeclareParents(value = "com.springaop.service.UserServiceImpl",
            defaultImpl = UserValidatorImpl.class)
    public UserValidator userValidator;

    //execution表示在执行时拦截匹配的方法 * 表示返回任意类型 （..）表示任意参数进行匹配
    @Pointcut("execution(* com.springaop.service.UserServiceImpl.printUser(..))")
    public void pointCut(){
        System.out.print("before...");
    }

    @Before("pointCut()")
    public void before(){
        System.out.print("before...");
    }

    @After("pointCut()")
    public void after(){
        System.out.print("after....");
    }

    //通过他的proceed方法来回调目标对象的方法printUser()，也可以对其方法进行其他操作然后调用
    @Around("pointCut()")
    public void around(ProceedingJoinPoint jp) throws Throwable{
        System.out.print("around before..");
        jp.proceed();
        System.out.print("around after..");
    }

    @AfterReturning("pointCut()")
    public void afterReturning(){
        System.out.print("AfterReturning...");
    }

    @AfterThrowing("pointCut()")
    public void afterThrowing(){
        System.out.print("AfterThrowing....");
    }
}
```

6，controller下面

```
@Controller
public class UserController {

    @Autowired
    private UserService userService=null;

    @RequestMapping("/print")
    @ResponseBody
    public User printUser(){
        User user=new User();
        user.setId(1);
        user.setNote("aahahha");
        user.setUsername("lixiuxiu");
        userService.printUser(user);
        return user;
    }

    @RequestMapping("/vp")
    @ResponseBody
    public User validateAndPrint(){
        User user =new User();
        user.setUsername("lixiuxiu");
        user.setId(1);
        user.setNote("heihei");
        UserValidator userValidator=(UserValidator)userService; //强制类型转换
        if(userValidator.validate(user)){  //可以使用验证方法验证user
            userService.printUser(user);
        }
        return user;
    }
}
```

6，在主类中加入

```
@SpringBootApplication
public class SpringAopApplication {
//定义切面
	@Bean(name="myAspect")
	public MyAspect initMyAspect(){
		return new MyAspect();
	}
//启动切面
	public static void main(String[] args) {
		SpringApplication.run(SpringAopApplication.class, args);
	}
}
```

运行结果：

```
引入新的接口：UserValidator
around before..   //按理说应该在before下面
before...
id =1
name=lixiuxiu
note=heihei
around after..  
after....
AfterReturning...
```



# Spring boot+mybatis（mapper）

使用mapper方式的mybatis，另外一种方式为注解方式

同时使用druid，Druid是Java语言中最好的数据库连接池。Druid能够提供强大的监控和扩展功能

1，pom.xml

```
<dependency>
   <groupId>mysql</groupId>
   <artifactId>mysql-connector-java</artifactId>
   <scope>runtime</scope>
</dependency>

<!--mybatis-->
<dependency>
   <groupId>org.mybatis.spring.boot</groupId>
   <artifactId>mybatis-spring-boot-starter</artifactId>
   <version>1.3.2</version>
</dependency>

<!-- alibaba的druid数据库连接池 -->
<dependency>
   <groupId>com.alibaba</groupId>
   <artifactId>druid-spring-boot-starter</artifactId>
   <version>1.1.9</version>
</dependency>
```

2,application.yml

```
server:
  port: 8080

spring:
    datasource:
        url: jdbc:mysql://localhost:3306/sys?characterEncoding=utf8&useSSL=false
        username: root
        password: Lxx19941114
        # 使用druid数据源
        type: com.alibaba.druid.pool.DruidDataSource
        driver-class-name: com.mysql.jdbc.Driver
        filters: stat
        maxActive: 20
        initialSize: 1
        maxWait: 60000
        minIdle: 1
        timeBetweenEvictionRunsMillis: 60000
        minEvictableIdleTimeMillis: 300000
        validationQuery: select 'x'
        testWhileIdle: true
        testOnBorrow: false
        testOnReturn: false
        poolPreparedStatements: true
        maxOpenPreparedStatements: 20

mybatis:
  mapper-locations: classpath:mapper/*.xml     #这里是mapper的配置文件
  type-aliases-package: com.example.mybatispaper.model    #这里是实体类的包

```

3，model/UserInfo

跟数据库中的字段自动驼峰映射。

```
public class UserInfo {
    private Integer id;

    private String userName;

    private String password;

    private String sex;

    private String content;

    private Date createDate;

    private Date updateDate;
    }
```

4，dao/UserMapper 这个接口跟同名的xml文件对映，这样有了实体的方法。注意头上要加@Mapper，否则就要在启动类上加上扫描mapper类的注解。

```
@Mapper
public interface UserMapper {
    UserInfo findByUsername(String id);
}
```

5,resource/mapper/UserMapper.xml. 这个xml的名字要跟dao下面的类一样

<result column="user_name" property="userName" jdbcType="VARCHAR" />

column的值是数据库中的字段名称，property的值是UserInfo类的名称

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="com.example.mybatispaper.dao.UserMapper">
    <resultMap id="BaseResultMap" type="com.example.mybatispaper.model.UserInfo" >
        <id column="id" property="id" jdbcType="BIGINT" />
        <result column="password" property="password" jdbcType="VARCHAR" />
        <result column="user_name" property="userName" jdbcType="VARCHAR" />
        <result column="sex" property="sex" jdbcType="VARCHAR" />
        <result column="content" property="content" jdbcType="VARCHAR" />
        <result column="create_date" property="createDate" jdbcType="DATE" />
        <result column="update_date" property="updateDate" jdbcType="DATE" />
    </resultMap>

    <sql id="BASE_TABLE">
        user_info
    </sql>

    <sql id="BASE_COLUMN">
        id,user_name,password,sex,content,create_date,update_date
    </sql>

    <select id="findByUsername" resultMap="BaseResultMap" parameterType="string">
        SELECT
        <include refid="BASE_COLUMN"/>
        FROM
        <include refid="BASE_TABLE"/>
        WHERE id = #{id}
    </select>
</mapper>
```

6.service和controller，注意这里id是int型，但是传入方法是要用string 型的，不然会报错

```
public interface UserService {
    UserInfo findByUsername(String id);
}
```

```
@Service
public class UserServiceImpl implements UserService{
    @Autowired
    private UserMapper sysUserMapper;

    @Override
    public UserInfo findByUsername(String id) {
        return sysUserMapper.findByUsername(id);
    }
}
```

```
@Controller
@RequestMapping("/")
public class UserController {
       @Autowired
        private UserService userService;

        @RequestMapping("test")
        @ResponseBody
        public UserInfo test(@RequestParam String id) {
                return userService.findByUsername(id);
        }


}
```



Spring boot +mybatis (generator)

1pom.xml略，下面是resources/generator/generatorConfig.xml。在edit config的maven里用mybatis-generator:generate -e命令生成，只能运行一次，不然配置文件会内容会重复，然后运行会报错

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
<generatorConfiguration>
    <context id="DB2Tables" targetRuntime="MyBatis3">
        <commentGenerator>
            <property name="suppressDate" value="true"/>
            <!--是否去除自动生成的注释-->
            <property name="suppressAllComments" value="true"/>
        </commentGenerator>
        <jdbcConnection driverClass="com.mysql.jdbc.Driver" connectionURL="jdbc:mysql://localhost:3306/lxx?useSSL=false" userId="root" password="Lxx19941114"></jdbcConnection>
        <javaTypeResolver>
            <property name="forceBigDecimals" value="false"/>
        </javaTypeResolver>
        <!-- 生成模型的包名和位置-->
        <javaModelGenerator targetPackage="me.freelee.mybatisdruid.model" targetProject="src/main/java">
            <property name="enableSubPackages" value="true"/>
            <property name="trimStrings" value="true"/>
        </javaModelGenerator>
        <!-- 生成映射文件的包名和位置-->
        <sqlMapGenerator targetPackage="mapper" targetProject="src/main/resources">
            <property name="enableSubPackages" value="true"/>
        </sqlMapGenerator>
        <!-- 生成DAO的包名和位置-->
        <javaClientGenerator type="XMLMAPPER" targetPackage="me.freelee.mybatisdruid.mapper" targetProject="src/main/java">
            <property name="enableSubPackages" value="true"/>
        </javaClientGenerator>
        <table tableName="tb_user" domainObjectName="User" enableCountByExample="false" enableUpdateByExample="false" enableDeleteByExample="false" enableSelectByExample="false"></table>
    </context>

</generatorConfiguration>
```

2，会生成三个文件， 

 me.freelee.mybatisdruid.mapper/UserMappe.java

me.freelee.mybatisdruid.model/User.java

resources/mapper/UserMapper.xml如下：

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="me.freelee.mybatisdruid.mapper.UserMapper">
  <resultMap id="BaseResultMap" type="me.freelee.mybatisdruid.model.User">
    <id column="id" jdbcType="INTEGER" property="id" />
    <result column="username" jdbcType="VARCHAR" property="username" />
    <result column="address" jdbcType="VARCHAR" property="address" />
  </resultMap>
  <sql id="Base_Column_List">
    id, username, address
  </sql>
  <select id="selectAllUser" resultType="me.freelee.mybatisdruid.model.User">
    select
    <include refid="Base_Column_List"/>
    from tb_user
  </select>
  <select id="selectByPrimaryKey" parameterType="java.lang.Long" resultMap="BaseResultMap">
    select 
    <include refid="Base_Column_List" />
    from tb_user
    where id = #{id,jdbcType=INTEGER}
  </select>
  <delete id="deleteByPrimaryKey" parameterType="java.lang.Integer">
    delete from tb_user
    where id = #{id,jdbcType=INTEGER}
  </delete>
  <insert id="insert" parameterType="me.freelee.mybatisdruid.model.User">
    insert into tb_user (id, username, address
      )
    values (#{id,jdbcType=INTEGER}, #{username,jdbcType=VARCHAR}, #{address,jdbcType=VARCHAR}
      )
  </insert>
  <insert id="insertSelective" parameterType="me.freelee.mybatisdruid.model.User">
    insert into tb_user
    <trim prefix="(" suffix=")" suffixOverrides=",">
      <if test="id != null">
        id,
      </if>
      <if test="username != null">
        username,
      </if>
      <if test="address != null">
        address,
      </if>
    </trim>
    <trim prefix="values (" suffix=")" suffixOverrides=",">
      <if test="id != null">
        #{id,jdbcType=INTEGER},
      </if>
      <if test="username != null">
        #{username,jdbcType=VARCHAR},
      </if>
      <if test="address != null">
        #{address,jdbcType=VARCHAR},
      </if>
    </trim>
  </insert>
  <update id="updateByPrimaryKeySelective" parameterType="me.freelee.mybatisdruid.model.User">
    update tb_user
    <set>
      <if test="username != null">
        username = #{username,jdbcType=VARCHAR},
      </if>
      <if test="address != null">
        address = #{address,jdbcType=VARCHAR},
      </if>
    </set>
    where id = #{id,jdbcType=INTEGER}
  </update>
  <update id="updateByPrimaryKey" parameterType="me.freelee.mybatisdruid.model.User">
    update tb_user
    set username = #{username,jdbcType=VARCHAR},
      address = #{address,jdbcType=VARCHAR}
    where id = #{id,jdbcType=INTEGER}
  </update>

</mapper>
```

3，application.yml

```
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/lxx?useSSL=false
    driver-class-name: com.mysql.jdbc.Driver
    username: root
    password: Lxx19941114
    type: com.alibaba.druid.pool.DruidDataSource
    initialSize: 5
    minIdle: 5
    maxActive: 20
    maxWait: 60000
    timeBetweenEvictionRunsMillis: 60000
    minEvictableIdleTimeMillis: 300000
    validationQuery: SELECT 1 FROM DUAL
    testWhileIdle: true
    testOnBorrow: false
    testOnReturn: false
    poolPreparedStatements: true
    #配置监控统计拦截的filters，去掉后监控界面后sql无法统计
    filters: stat,wall,log4j
    maxPoolPreparedStatementPerConnectionSize: 20
    useGlobalDataSourceStat: true
    connectionProperties: druid.stat.mergeSql=true;druid.stat.slowSqlMillis=500
  thymeleaf:
    cache: false

mybatis:
  config-location: classpath:mybatis-config.xml
  mapper-locations: classpath:/mapper/*.xml
  type-aliases-package: me.freelee.mybatisdruid.model

server:
  port: 8080

pagehelper:
  auto-dialect: mysql
  reasonable: true
```

4,mybatis-config.xml (可选)

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <settings>
        <!-- 全局启用或禁用延迟加载。禁用后，所有关联对象都会即使加载 -->
        <setting name="lazyLoadingEnabled" value="true" />
        <!-- 使用驼峰命名法-->
        <setting name="mapUnderscoreToCamelCase" value="true" />
    </settings>
</configuration>
```

# Spring boot+mybatis注解+pagehelper

1.pom.xml

```
<dependency>
   <groupId>org.mybatis.spring.boot</groupId>
   <artifactId>mybatis-spring-boot-starter</artifactId>
   <version>1.3.2</version>
</dependency>

<!--spring boot 用这个paperhelper依赖,而spring用的又不一样-->
<dependency>
   <groupId>com.github.pagehelper</groupId>
   <artifactId>pagehelper-spring-boot-starter</artifactId>
   <version>1.2.5</version>
</dependency>

<dependency>
   <groupId>mysql</groupId>
   <artifactId>mysql-connector-java</artifactId>
   <scope>runtime</scope>
</dependency>
```

2，application.properties

```
##端口号
server.port=8888

##日志级别
logging.level.com.dalaoyang.dao.UserMapper=debug

##数据库url
spring.datasource.url=jdbc:mysql://localhost:3306/sys?characterEncoding=utf8&useSSL=false
##数据库用户名
spring.datasource.username=root
##数据库密码
spring.datasource.password=Lxx19941114
##数据库驱动
spring.datasource.driver-class-name=com.mysql.jdbc.Driver



#pagehelper分页插件配置
pagehelper.helperDialect=mysql
```

3，model/UserInfo

跟数据库中的字段自动驼峰映射。

```
public class UserInfo {
    private Integer id;

    private String userName;

    private String password;

    private String sex;

    private String content;

    private Date createDate;

    private Date updateDate;
    }
```

4,dao/UserMapper

```
@Mapper
public interface UserMapper {
    UserInfo findByUsername(String id);
}
```

5,controller/UserController

```
@RestController
    public class UserController {

        @Autowired
        UserMapper userMapper;

        //http://localhost:8888/getUserList?pageNum=1&pageSize=2
        @RequestMapping("/getUserList")
        public Page<UserInfo> getUserList(Integer pageNum, Integer pageSize){
            PageHelper.startPage(pageNum, pageSize);  //设置参数，页数和一页的大小
            Page<UserInfo>  userList= userMapper.getUserList();  //  自带的Page
            return userList;
        }
    }
```



另外一种方法：PageInfo ，service，contorller，和html

```
@Override
public PageInfo<User> queryPage(Integer pageNum) {
    //引入PageHelper分页插件
    //startPage方法后紧跟查询语句,这样才能实现分页查询
    PageHelper.startPage(pageNum,2);
    List<User> list=userMapper.selectAllUser();
    //PageInfo构造函数第二个参数是显示导航页的数量
    PageInfo<User> pageInfo=new PageInfo<>(list,2);
    return pageInfo;
}
```

```
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>

<body>
    <h2>User List</h2>
    <table>
        <tr>
            <th>#</th>
            <th>Name</th>
            <th>Adress</th>

        </tr>
        <tbody>
            <tr th:each="user:${pageInfo.list}">
                <td th:text="${user.id}"></td>
                <td th:text="${user.username}"></td>
                <td th:text="${user.address}"></td>

            </tr>
        </tbody>
    </table>


    <p>当前 <span th:text="${pageInfo.pageNum}"></span> 页,总 <span th:text="${pageInfo.pages}"></span> 页,共 <span th:text="${pageInfo.total}"></span> 条记录</p>
    <a th:href="@{/user(pageNo=1)}">首页</a>
    <a th:href="@{/user(pageNo=${pageInfo.hasPreviousPage}?${pageInfo.prePage}:1)}">上一页</a>
    <a th:href="@{/user(pageNo=${pageInfo.hasNextPage}?${pageInfo.nextPage}:${pageInfo.pages})}">下一页</a>
    <a th:href="@{/user(pageNo=${pageInfo.pages})}">尾页</a>

</body>
</html>
```

```
 @ApiOperation(value = "获取用户列表", notes = "获取用户列表")
    @GetMapping("")
    public String list(@RequestParam(value = "pageNo",defaultValue = "1") Integer pageNo
                , Model model){
        PageInfo<User> pageInfo=userService.queryPage(pageNo);
        model.addAttribute("pageInfo",pageInfo);
        return "list";
    }
```

