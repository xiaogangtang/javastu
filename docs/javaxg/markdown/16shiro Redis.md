# shiro

## 一、复习

Vue：v-html，它能把html标签解析成页面标签，而不是字符串。

 v-show，通过css的display：nono，表示标签的隐藏。

 v-if，直接干掉的是整个dom节点。

 v-for，遍历数组或者对象，数组可以使用 for-of，对象是使用for-in

 v-bind，单向绑定我们的model中的数据。缩写 ：

 v-on，绑定事件，缩写是@，[@input](https://github.com/input) [@change](https://github.com/change)

 v-model，双向绑定，可以实时修改model中的数据。语法糖，实际是v-bind和v-on的结合。

三大必备技术：组件，路由，axios。

 组件：可以复用的页面。 声子，挂子，用子。

 父传子：通过子组件中自定义属性 props:{} 或者 props:[]，在父页面中，直接使用标签属性即可。

 子传父：通过自定义事件来传上去。$emit(‘自定义事件名’,需要传的参数)

 路由：路由出口 ，<router-link to=””>，

 路由嵌套，是在router/index.js 文件中，通过 children属性配置子路由。

 路由传参，params，query，两种形式传参。

 axios：类似ajax的用法。this.$axios.get(‘url’).then(res=>{}).catch(err=>{});

 跨域：我们是定义了一个config/index.js，我们是配置了一个api，代理服务器。

 为什么会出现跨域，域名还有端口号不一致，会出现跨域。

 生命周期：beforeCreate created beforeMount，mounted，beforeUpdate，updated，beforeDestroy，destroyed。

## 二、shiro

接下来：`shiro`+linux+`redis`+nginx+git的详细用法，全阶段复习(1~4阶段)。

基于JWT+前后端分离的 权限认证框架。

springsecurity重量级框架，它和spring是高度集成的。简单来说，就是引入一个依赖，就能用了。

## 三、RBAC概念

基于角色的权限访问控制(Role-Based Access Control)。

角色和权限是什么？RBAC重点就是我们角色和权限之间的关联关系。

第一类权限信息：

用户直接关联权限。

老张，老李，老王，他们都是普通的白领，他们拥有基础的登录权限，查看表单，新增表单的权限，但是没有编辑和修改的权限。

用户表和权限表：1对多的关联关系。

老张改成了可以编辑，老王可以删除权限。我们的操作，必须一个一个的用户对象，找到这个对象，然后操作这个对象所拥有的权限。这个的操作工作量很大。

第二类有`中间表`：

用户表——中间表 ——-权限表 多对多

第三类有`角色表`。

用户表——->角色表——>中间表——->权限表 解耦用户和权限直接的关系。

一星白领，二星白领 —->权限表 多对多。

一星能够编辑表单。 某些用户一旦拥有一星白领的角色后，他们是不是都可以拥有对应的权限。以后，我们权限发生变化，是不是仅仅只需要修改这个一星白领角色所拥有的权限即可。

![image-20210425225959659](../images/20210425225959.png)

在RBAC模型中，角色是系统根据管理中相对稳定的职权和责任来划分，每种角色可以完成一定的职能。用户通过饰演不同的角色获得角色所拥有的权限，一旦某个用户成为某角色的成员，则此用户可以完成该角色所具有的职能。通过将权限指定给角色而不是用户，在权限分派上提供了极大的灵活性和极细的权限指定粒度。

第四类：SAAS系统 PAAS系统

第五类：中台

第六类：DDD 领域驱动模型

CRM客户关系管理系统 ERP 企业资源计划管理系统 OA 办公系统：泛微、蓝凌、致远。

财务系统：金蝶和NC用友

## 四、常见的认证机制

### 1.HTTP Basic Auth

HTTP Basic Auth简单来说，就是每次请求API时，我们都要提供用户的username和password。这就是我们2阶段的认证机制，RESTful进行的简单权限认证方式。

缺点，把用户名和密码暴露给第三方客户端(浏览器)，所以在生产环节中使用越来越少。

### 2.Cookie Auth

Cookie认证机制就是为了一次请求认证在服务端创建一个Session对象，同时也会在浏览器端创建一个Cookie对象。通过客户端带过来的Cookie对象类与服务端的Session对象进行匹配，来实现一个状态的管理。默认的，当我们关闭浏览器的时候，cookie会删除。可以通过修改cookie的expire time来使cookie在一定时间内有效。

### 3.Oauth

Oauth(开放授权)是一个开放的授权标准，允许用户让通过第三方应用访问该用户存储在服务器端的私密资源(如照片，视频，联系人列表)，而无需将用户名和密码提供给第三方应用。

核心内容，令牌，而不是用户名和密码。

做第三方认证！

![image-20210429011812526](../images/20210429011812.png)

![image-20210429154047759](../images/20210429154047.png)

### 4.Token Auth

使用`基于 Token 的身份验证方法，在服务端不需要存储用户的登录记录`。大概的流程是这样的：

1. 客户端使用用户名跟密码请求登录
2. 服务端收到请求，去验证用户名与密码 + 盐 salt
3. 验证成功后，服务端会签发一个 Token，再把这个 Token 发送给客户端
4. 客户端收到 Token 以后可以把它存储起来，比如放在 Cookie 里
5. 客户端每次向服务端请求资源的时候需要带着服务端签发的 Token
6. 服务端收到请求，然后去验证客户端请求里面带着的 Token，如果验证成功，就向客户端返回请求的数据

![image-20210429011902435](../images/20210429011902.png)

Token Auth的优点:

1.支持跨域访问: Cookie是不允许垮域访问的，这一点对Token机制是不存在的，前提是传输的用户认证信息通过HTTP请求头传输.

2.无状态(也称：服务端可扩展行):Token机制在服务端不需要存储session信息，因为Token 自身包含了所有登录用户的信息，只需要在客户端的cookie或本地介质存储状态信息.

3.更适用CDN: 可以通过内容分发网络请求你服务端的所有资料（如：javascript，HTML,图片等），而你的服务端只要提供API即可.

4.去耦: 不需要绑定到一个特定的身份验证方案。Token可以在任何地方生成，只要在你的API被调用的时候，你可以进行Token生成调用即可.

5.更适用于移动应用: 当你的客户端是一个原生平台（iOS, Android，Windows 8等）时，Cookie是不被支持的（你需要通过Cookie容器进行处理），这时采用Token认证机制就会简单得多。

6.CSRF:因为不再依赖于Cookie，所以你就不需要考虑对CSRF（跨站请求伪造）的防范。

7.性能: 一次网络往返时间（通过数据库查询session信息）总比做一次HMACSHA256计算 的Token验证和解析要费时得多.

8.不需要为登录页面做特殊处理: 如果你使用postman做功能测试的时候，不再需要为登录页面做特殊处理.

9.基于标准化:你的API可以采用标准化的 JSON Web Token (JWT). 这个标准已经存在多个后端库（.NET, Ruby,Java,Python, PHP）和多家公司的支持（如：Firebase,Google, Microsoft）.

## 三、JWT入门概念

`JsonWeb Token`  是一种令牌!

### 1. 快速入门

#### 1.1 token的创建

（1）创建maven工程，引入依赖

```xml
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt</artifactId>
    <version>0.9.1</version>
</dependency>
```

（2）创建类CreateJwtTest，用于生成token

```java
import io.jsonwebtoken.JwtBuilder;
import io.jsonwebtoken.Jwts;
import io.jsonwebtoken.SignatureAlgorithm;
import java.util.Date;
/**
 * @author: mayuhang  <br/>
 * Date: 2021/4/29:1:45  <br/>
 * Description:
 */
public class CreateJwtTest {
        public static void main(String[] args) {
            //建造者模式
            JwtBuilder builder= Jwts.builder().setId("888")
                    .setSubject("管理员")
                    .setIssuedAt(new Date())
                	//加密方式--密钥
                    .signWith(SignatureAlgorithm.HS256,"woniuxy");
            System.out.println( builder.compact() );
        }
}
```

（3）测试运行，输出如下:

eyJhbGciOiJIUzI1NiJ9.eyJqdGkiOiI4ODgiLCJzdWIiOiLnrqHnkIblkZgiLCJpYXQiOjE2MTk2MzIwMzJ9.5de6yHqZn7SMBduX9Xxu04w4WfREjyRLp5e9iqzSnjE

> token由头、body、尾三部分组成，中间的body可以解密

#### 1.2 token的解析

我们刚才已经创建了token ，在web应用中这个操作是由服务端进行然后发给客户端，客户端在下次向服务端发送请求时需要携带这个token（这就好像是拿着一张门票一样），那服务端接到这个token 应该解析出token中的信息（例如用户id）,根据这些信息查询数据库返回相应的结果。
创建ParseJwtTest

```java
import io.jsonwebtoken.Claims;
import io.jsonwebtoken.Jwts;
import java.io.UnsupportedEncodingException;
/**
 * @author: mayuhang  <br/>
 * Date: 2021/4/29:1:48  <br/>
 * Description:
 */
public class ParseJwtTest {
    public static void main(String[] args) {
        String token="eyJhbGciOiJIUzI1NiJ9.eyJqdGkiOiI4ODgiLCJzdWIiOiJBRE1JTiIsImlhdCI6MTYxOTYzMjQ3NH0.-TJRtXOjhjoU1oD7Y1VLAp2dSQeaVK7aBzRj0ZtTfPw";
        //"woniuxy"即盐，就是加密时的密钥
        Claims claims = Jwts.parser().setSigningKey("woniuxy").parseClaimsJws(token).getBody();
        System.out.println("id:"+claims.getId());
        System.out.println("subject:"+claims.getSubject());
        System.out.println("IssuedAt:"+claims.getIssuedAt());
    }
}
```

#### 1.3 自定义claims

setId 、setSubject两个信息，如果想要存储更多的信息 角色信息 权限信息，自定义的claims。

自定义claim属性：加密过程

```java
package com.woniuxy.utils;
import io.jsonwebtoken.JwtBuilder;
import io.jsonwebtoken.Jwts;
import io.jsonwebtoken.SignatureAlgorithm;
import java.util.Date;
public class JWTUtil {
    public static void main(String[] args) {
        long now = System.currentTimeMillis();
        long exp = now + 1000*60;//60s过期
        JwtBuilder admin = Jwts.builder()
                .setId("999")
                .setSubject("admin")
                .claim("password","123456")
                .claim("createtime",new Date())
                .claim("roles","edit")
                .setExpiration(new Date(exp))
                .setIssuedAt(new Date())
                .signWith(SignatureAlgorithm.HS256,"woniuxy");
        System.out.println(admin.compact());
    }
}
```

解密过程：

```java
package com.woniuxy.utils;
import io.jsonwebtoken.Claims;
import io.jsonwebtoken.Jwt;
import io.jsonwebtoken.Jwts;
import io.jsonwebtoken.impl.TextCodec;
import java.util.Arrays;
public class ParseJwtTest {
    public static void main(String[] args) {
        String token="eyJhbGciOiJIUzI1NiJ9.eyJqdGkiOiI5OTkiLCJzdWIiOiJhZG1pbiIsInBhc3N3b3JkIjoiMTIzNDU2IiwiY3JlYXRldGltZSI6MTYzNDUzODUwMjgzMSwicm9sZXMiOiJlZGl0IiwiZXhwIjoxNjM0NTM4NTYyLCJpYXQiOjE2MzQ1Mzg1MDJ9.SbUPaO2LEj8uXdrEeoxJ8llD_psVLYzK0Ta2qqL6b0U";
        Claims woniuxy = Jwts.parser().setSigningKey("woniuxy").parseClaimsJws(token)
                .getBody();
        System.out.println(woniuxy.getId());
        System.out.println(woniuxy.getSubject());
        System.out.println(woniuxy.getIssuedAt());
        System.out.println(woniuxy.get("password"));
        System.out.println(woniuxy.get("roles"));
    }
}
```

> 对称加密：异或(位运算：就是在不添加第三个参数，交换两个变量的值)
>
> 可以加密同时可以解密。
>
> 非对称加密：MD5
>
> 只能加密，无法解密的加密算法。
>
> jwt：可以生成一个三个部分的加密串，而且是字符串。可以在这个token中添加自定义的信息，可以反向解密出来。
>
>  还可以添加过期时间。
>
>  可以加密可以解密。

## 四、Shiro权限框架

现在系统需要实现：
超级管理员登录系统，显示所有的功能
而普通管理员登录系统，显示他对应的功能

如果没有Shiro这种权限框架的情况，创建 role_info 、 menu_info 、role_menu中间表

登录的时候，查询后端的权限，并根据权限展示页面

但这套逻辑需要自己写，很麻烦！

### 1. Shiro的简介

公司项目中，常见的权限框架：shiro | spring security
Apache Shiro是一个功能强大且灵活的开源安全框架，可以清晰地处理身份验证，授权，企业会话管理和加密。

Apache Shiro 的首要目标是易于使用和理解。权限是很复杂的，甚至是痛苦的，但它没有必要这样。框架应该尽可能掩盖( 黑盒 | 空调)复杂的地方，露出一个干净而直观的 API(遥控器)，来简化开发人员在使他们的应用程序安全上的努力。

Shiro能帮系统做什么：
1、做用户的身份认证(登录)，判断用户是否系统用户（重点）
2、给系统用户授权，用来帮助系统实现不同的用户展示不同的功能（重点）
3、针对密码等敏感信息，进行加密处理（明文变成密文）（重点）
4、提供了Session管理，但是它的Session不是HttpSession，是它自己自带的
5、做授权信息的缓存管理，降低对数据库的授权访问
6、提供测试支持，因为它也是一个轻量级框架，它也可以直接针对代码进行使用Junit单元测试
7、提供Remeber me的功能，可以做用户无需再次登录即可访问某些页面
8、启用单点登录（SSO）功能。

### 2. Shiro提供的10大功能

![img](../images/%E5%9B%BE%E7%89%871.png)

1. `Authentication`身份认证/登录，验证用户是不是拥有相应的身份；C
2. `Authorization` Z授权，即权限验证，验证某个已认证的用户是否拥有某个权限；即判断用户是否能做事情，常见的如：验证某个用户是否拥有某个角色。或者细粒度的验证某个用户对某个资源是否具有某个权限；
3. Session Management 会话管理，即用户登录后就是一次会话，在没有退出之前，它的所有信息都在会话中；会话可以是普通JavaSE环境的，也可以是如Web环境的；
4. `Cryptography` 加密，保护数据的安全性，如密码加密存储到数据库，而不是明文存储；
5. Web Support Web支持，可以非常容易的集成到Web环境；
6. `Caching` 缓存，比如用户登录后，其用户信息、拥有的角色/权限不必每次去查，这样可以提高效率；
7. Concurrency shiro支持多线程应用的并发验证，即如在一个线程中开启另一个线程，能把权限自动传播过去；
8. Testing 提供测试支持；
9. Run As : 可以模拟转换成其他角色信息；
10. Remember Me 记住我，这个是非常常见的功能，即一次登录后，下次再来的话不用登录了

### 3. Shiro的架构图

接下来从Shiro内部来看下Shiro的架构，如下图所示：
![img](../images/%E5%9B%BE%E7%89%871-163460552731515.png)

**Subject主体：**可以看到主体可以是任何可以与应用交互的“用户”；

**SecurityManager：** 相当于SpringMVC中的DispatcherServlet或者Struts2中的FilterDispatcher；是Shiro的心脏；所有具体的交互都通过SecurityManager进行控制；它管理着所有Subject、且负责进行认证和授权、及会话、缓存的管理。

**Authenticator认证器：**负责主体认证的，这是一个扩展点，如果用户觉得Shiro默认的不好，可以自定义实现；其需要认证策略（Authentication Strategy），即什么情况下算用户认证(登录)通过了；

**Authrizer授权器：** 或者访问控制器，用来决定主体是否有权限进行相应的操作；即控制着用户能访问应用中的哪些功能；

**Realm域(面试常问)：** 可以有1个或多个Realm，可以认为是安全实体数据源，即用于获取安全实体的；可以是JDBC实现，也可以是LDAP实现，或者内存实现等等；由用户提供；注意：Shiro不知道你的用户/权限存储在哪及以何种格式存储；所以我们一般在应用中都需要实现自己的Realm；

**SessionManager：** 如果写过Servlet就应该知道Session的概念，Session呢需要有人去管理它的生命周期，这个组件就是SessionManager；而Shiro并不仅仅可以用在Web环境，也可以用在如普通的JavaSE环境、EJB等环境；所有呢，Shiro就抽象了一个自己的Session来管理主体与应用之间交互的数据；这样的话，比如我们在Web环境用，刚开始是一台Web服务器；
接着又上了台EJB服务器；这时想把两台服务器的会话数据放到一个地方，这个时候就可
以实现自己的分布式会话（如把数据放到Memcached服务器）；

**SessionDAO：** DAO大家都用过，数据访问对象，用于会话的CRUD，比如我们想把Session保存到数据库，那么可以实现自己的SessionDAO，通过如JDBC写到数据库；比如想把Session放到Memcached中，可以实现自己的Memcached SessionDAO；另外SessionDAO中可以使用Cache进行缓存，以提高性能；

**CacheManager** 缓存控制器，来管理如用户、角色、权限等的缓存的；因为这些数据基本上很少去改变，放到缓存中后可以提高访问的性能

**Cryptography** 密码模块，Shiro提高了一些常见的加密组件用于如密码加密/解密的。

### 4. Shiro框架的3个核心类

首先，从外部来看Shiro，即从应用程序角度的来观察如何使用Shiro完成工作。如下图：

![image-20210429004301470](../images/20210429004301.png)

**Subject主体：** 需要登录系统的东西，都是主体。 代表了当前“用户”，这个用户不一定是一个具体的人，与当前应用交互的任何东西都是Subject，如网络爬虫，机器人等；即一个抽象概念；所有Subject都绑定到SecurityManager，与Subject的所有交互都会委托给SecurityManager，可以把Subject认为是一个门面；SecurityManager才是实际的执行者；

**SecurityManager安全管理器：** 即所有与安全有关的操作都会与SecurityManager交互；且它管理着所有Subject；可以看出它是Shiro的核心，它负责与其他组件进行交互，相当于DispatcherServlet前端控制器；

**Realm域：** 一个用来做身份认证，以及授权的对象 Shiro从Realm获取安全数据（如用户、角色、权限），就是说SecurityManager要验证用户身份，那么它需要从Realm获取相应的用户进行比较以确定用户身份是否合法；也需要从Realm得到用户相应的角色/权限进行验证用户是否能进行操作；可以把Realm看成DataSource，就是一个跟权限数据有关的数据源。

**记住一点：** Shiro不会去维护用户、维护权限；这些需要我们自己去设计/提供；然后通过相应的接口注入给Shiro即可。

![image-20210430113415901](../images/20210430113416.png)

## 五、shiro入门程序

### 1、基于ini配置文件的权限管理

#### 1.1 引入pom依赖

```xml
<!--     入门程序需要引入的代码   -->
<dependency>
    <groupId>org.apache.shiro</groupId>
    <artifactId>shiro-core</artifactId>
    <version>1.7.1</version>
</dependency>
```

#### 1.2 用户认证 BasicIniEnvironment

认证：用户登录用身份认证，检验用户是不是拥有相应身份，能否登录。

subject主体，里面有个方法login，来验证`数据库`(ini配置文件中查)，有没有这个账号密码。

1. 写配置文件 resources/shiro-auth.ini这个文件

   ```ini
   [users]
   #模拟从数据库中查询用户 判断密码是否正确
   #数据格式  用户名=密码
   mayun=123456
   mayuhang=654321
   ```

2. 写测试类

   ```java
   package com.woniuxy.shirotest;
   import org.apache.shiro.SecurityUtils;
   import org.apache.shiro.authc.UsernamePasswordToken;
   import org.apache.shiro.config.IniSecurityManagerFactory;
   import org.apache.shiro.env.BasicIniEnvironment;
   import org.apache.shiro.mgt.SecurityManager;
   import org.apache.shiro.subject.Subject;
   public class ShiroAuth {
       public static void main(String[] args) {
   //        1.加载配置文件 ini文件
           BasicIniEnvironment basicIniEnvironment = new BasicIniEnvironment("classpath:shiro-auth.ini");
   //        2.通过这个环境来获取 SecurityManager对象
           SecurityManager securityManager = basicIniEnvironment.getSecurityManager();
   //        3.把SecurityManager绑定到当前的运行环境中
           SecurityUtils.setSecurityManager(securityManager);
   //        4.创建主体 subject！！！！！核心代码 我们一定 要有操作主体！！
           Subject subject = SecurityUtils.getSubject();
           //上述代码就是 框架底层自己写好的代码 实际开发中，环境和SecurityManager
           //是框架帮我们实现 我们只需要用第四步操作即可
   //        5.构造登录主体的凭证，提供用户名和密码  后端的一个实体类，用来封装我们的账号密码
           UsernamePasswordToken usernamePasswordToken = new UsernamePasswordToken("mayuhang","654321");
   //        6.通过主体的login方法，和我们封装好的对象，系统自动帮我们进行认证判断
           subject.login(usernamePasswordToken);
           //
           System.out.println("用户登录成功，认证状态是："+subject.isAuthenticated());
           //获取一下用户信息
           System.out.println("从主体中获取校长："+subject.getPrincipal());
       }
   }
   ```

#### 第一个总结：

> 必须要依托于 SecurityManager对象，SecurityManager对象是怎么创建的怎么生成的我们都不要去操心。
>
> 官方提供了一个工具类，SecurityUtils，工具类此时，啥都没有。工具类对应需要一个管理器SecurityManager。
>
> 如果是在项目中使用，SecurityUtils里面自动会绑定上我们的SecurityManager。
>
> 我们只需要进行一步：
>
> SecurityUtils就可以用它来获取主体Subject。
>
> 主体，可以调用login来执行登录认证。
>
> if(账号.equels(数据库账号)){
>
>  if(密码判断)
>
> }

#### 1.3 用户授权

授权，即权限验证，验证某个已认证的用户是否拥有某个权限；即判断用户是否能做事情，常见的如：验证某个用户是否拥有某个角色。或者细粒度的验证某个用户对某个资源是否具有某个权限
(1)在resource目录下创建shiro的ini配置文件构造模拟数据（shiro-prem.ini）

```ini
[users]
#模拟从数据库查询的用户
#数据格式 用户名=密码,角色1,角色2..
mayun=123456,role1,role2
mayuhang=654321,role3
[roles]
#模拟从数据库查询的角色和权限列表
#数据格式 角色名=权限1，权限2
role1=user:save,user:update
role2=user:update,user.delete
role3=user:find
```

````java
package com.woniuxy.shirotest;
import org.apache.shiro.SecurityUtils;
import org.apache.shiro.authc.UsernamePasswordToken;
import org.apache.shiro.env.BasicIniEnvironment;
import org.apache.shiro.mgt.SecurityManager;
import org.apache.shiro.subject.Subject;
import java.util.Set;
public class ShiroPerm {
    public static void main(String[] args) {
//        1.加载配置文件 ini文件
        BasicIniEnvironment basicIniEnvironment = new BasicIniEnvironment("classpath:shiro-perm.ini");
//        2.通过这个环境来获取 SecurityManager对象
        SecurityManager securityManager = basicIniEnvironment.getSecurityManager();
//        3.把SecurityManager绑定到当前的运行环境中
        SecurityUtils.setSecurityManager(securityManager);
//        4.创建主体 subject！！！！！核心代码 我们一定 要有操作主体！！
        Subject subject = SecurityUtils.getSubject();
        //上述代码就是 框架底层自己写好的代码 实际开发中，环境和SecurityManager
        //是框架帮我们实现 我们只需要用第四步操作即可
//        5.构造登录主体的凭证，提供用户名和密码  后端的一个实体类，用来封装我们的账号密码
        UsernamePasswordToken usernamePasswordToken = new UsernamePasswordToken("mayuhang","654321");
//        6.通过主体的login方法，和我们封装好的对象，系统自动帮我们进行认证判断
        subject.login(usernamePasswordToken);
        //只要第6步能够完成，那么就能进行权限判断
        boolean permitted = subject.isPermitted("user:delete");
        boolean permitted1 = subject.isPermitted("user:find");
        System.out.println("该用户："+subject.getPrincipal()+"有没有user:delete权限："+permitted);
        System.out.println("该用户："+subject.getPrincipal()+"有没有user:find权限："+permitted1);
        boolean role3 = subject.hasRole("role3");
        System.out.println("该用户："+subject.getPrincipal()+"有没有role3角色："+role3);
        System.out.println("用户登录成功，认证状态是："+subject.isAuthenticated());
        //获取一下用户信息
        System.out.println("从主体中获取校长："+subject.getPrincipal());
    }
}
````

#### 1.4 自定义域

Realm域：Shiro从Realm获取安全数据（如用户、角色、权限），就是说SecurityManager要验证用户身份，那么它需要从Realm获取相应的用户进行比较以确定用户身份是否合法；也需要从Realm得到用户相应的角色/权限进行验证用户是否能进行操作；可以把Realm看成DataSource，即安全数据源。

1. 配置shiro的ini配置文件（shiro-realm.ini）

   ```ini
   [main]
   #声明realm
   permReam=com.woniuxy.shirotest.MyRealm
   #注册realm到securityManager中
   securityManager.realms=$permReam
   ```

   编写自定义域：

   ```java
   package com.woniuxy.shirotest;
   import org.apache.shiro.authc.*;
   import org.apache.shiro.authz.AuthorizationInfo;
   import org.apache.shiro.authz.SimpleAuthorizationInfo;
   import org.apache.shiro.realm.AuthorizingRealm;
   import org.apache.shiro.subject.PrincipalCollection;
   import java.util.*;
   public class MyRealm extends AuthorizingRealm {
       /**
        * 授权用 授权信息
        * @param principals
        * @return
        */
       @Override
       protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principals) {
           String primaryPrincipal = principals.getPrimaryPrincipal()+"";
           System.out.println(primaryPrincipal);
           //1.模拟去数据库中找一下 我这个校长所拥有的角色和权限
           Set<String> list =new HashSet<>();
           list.add("role1");
           list.add("role2");
           list.add("role3");
           HashSet<String> hashSet = new HashSet<>();
           hashSet.add("user:add");
           hashSet.add("user:delete");
           hashSet.add("user:update");
           hashSet.add("user:find");
           SimpleAuthorizationInfo simpleAuthorizationInfo = new SimpleAuthorizationInfo();
           simpleAuthorizationInfo.setRoles((Set<String>) list);
           simpleAuthorizationInfo.setStringPermissions(hashSet);
           return simpleAuthorizationInfo;
       }
       /**
        * 认证信息
        * @param token
        * @return
        * @throws AuthenticationException
        */
       @Override
       protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken token) throws AuthenticationException {
   //        1.简单的账号密码登录
           UsernamePasswordToken usernamePasswordToken = (UsernamePasswordToken) token;
           String username = usernamePasswordToken.getUsername();
           char[] s = usernamePasswordToken.getPassword();
           String password = new String(s);
           if(username.equals("mayuhang")){
               if(password.equals("654321")){
                    new SimpleAuthenticationInfo(username,password,"myRealm");
               }else{
                   throw new RuntimeException("密码错误");
               }
           }else{
               throw new RuntimeException("账号错误");
           }
       }
   }
   ```

   编写测试类：

   ```java
   package com.woniuxy.shirotest;
   import org.apache.shiro.SecurityUtils;
   import org.apache.shiro.authc.UsernamePasswordToken;
   import org.apache.shiro.env.BasicIniEnvironment;
   import org.apache.shiro.mgt.SecurityManager;
   import org.apache.shiro.subject.Subject;
   public class ShiroRealm {
       public static void main(String[] args) {
   //        1.加载配置文件 ini文件
           BasicIniEnvironment basicIniEnvironment = new BasicIniEnvironment("classpath:shiro-realm.ini");
   //        2.通过这个环境来获取 SecurityManager对象
           SecurityManager securityManager = basicIniEnvironment.getSecurityManager();
   //        3.把SecurityManager绑定到当前的运行环境中
           SecurityUtils.setSecurityManager(securityManager);
   //        4.创建主体 subject！！！！！核心代码 我们一定 要有操作主体！！
           Subject subject = SecurityUtils.getSubject();
           //上述代码就是 框架底层自己写好的代码 实际开发中，环境和SecurityManager
           //是框架帮我们实现 我们只需要用第四步操作即可
   //        5.构造登录主体的凭证，提供用户名和密码  后端的一个实体类，用来封装我们的账号密码
           UsernamePasswordToken usernamePasswordToken = new UsernamePasswordToken("mayuhang","654321");
   //        6.通过主体的login方法，和我们封装好的对象，系统自动帮我们进行认证判断
           subject.login(usernamePasswordToken);
           //只要第6步能够完成，那么就能进行权限判断
           boolean permitted = subject.isPermitted("user:delete");
           boolean permitted1 = subject.isPermitted("user:findAll");
           System.out.println("该用户："+subject.getPrincipal()+"有没有user:delete权限："+permitted);
           System.out.println("该用户："+subject.getPrincipal()+"有没有user:find权限："+permitted1);
           boolean role3 = subject.hasRole("role3");
           System.out.println("该用户："+subject.getPrincipal()+"有没有role3角色："+role3);
           System.out.println("用户登录成功，认证状态是："+subject.isAuthenticated());
           //获取一下用户信息
           System.out.println("从主体中获取校长："+subject.getPrincipal());
       }
   }
   ```

#  shiro整合

## 一、复习

RBAC：用户——角色——权限 ，最少的权限设计表，5张表， 用户——>用户角色关联表——>角色——>角色权限关联表——>权限表。

认证机制：HTTP basic auth(基于账号密码的认证)；Cookie Auth(前后端不分离的认证方式)；Oauth(开放授权标准，第三方认证)；JWT认证(类似第一种账号密码认证，唯一不同的是加密了，还能设置过期时间)

JWT ：json web token;就是个字符串，它有3个部分。里面可以存放任意数据，但是一般不会放敏感信息，这个数据是能被解析出来的，明文。

shiro: 基于ini配置文件的3种配置方式。

```java
Subject subject = SecurityUtils.getSubject();
```

第一种， 仅仅使用认证，判断账号密码是否ini配置文件中(数据库)有。

```Java
subject.login(new UsernamePasswordToken("admin","123456"));
subject.isAuthenticated();//true 登录认证成功
```

第二种，认证+授权

```java
subject.login(new UsernamePasswordToken("admin","123456"));
subject.isAuthenticated();//true 登录认证成功
subject.isPermitted("user:edit")；//是否有这个权限
subject.hasRole("role1")；//是否有某个权限
```

第三种，自定义域的形式

```java
自定义域类 继承 AuthorizingRealm；
    重写2个方法，一个是认证，返回SimpleAuthenticationInfo它就是一个实体类，一个是授权。返回一个SimpleAuthorizationInfo。
```

测试方法一样的不用改。

ini配置文件不用记。

## 二、基于JWT+shiro实现springboot整合

#### 1、引入依赖

```xml
<!--      基于springboot的shiro整合  -->
<dependency>
    <groupId>org.apache.shiro</groupId>
    <artifactId>shiro-spring-boot-starter</artifactId>
    <version>1.7.1</version>
</dependency>
<!--        引入jwt-->
<dependency>
    <groupId>com.auth0</groupId>
    <artifactId>java-jwt</artifactId>
    <version>3.18.2</version>
</dependency>
```

#### 2.用MyJsonWebToken替换UsernamePasswordToken实体类

```java
package com.woniuxy.shiro.utils;
import lombok.Data;
import org.apache.shiro.authc.AuthenticationToken;
@Data
public class MyJsonWebToken implements AuthenticationToken {
    String token;//我们的认证仅仅只需要提供token即可不需要账号密码
    @Override
    public Object getPrincipal() {
        return token;
    }
    @Override
    public Object getCredentials() {
        return token;
    }
}
```

#### 3.JWT工具类的应用(了解即可)

直接使用即可。

```java
package com.woniuxy.utils;
import com.auth0.jwt.JWT;
import com.auth0.jwt.JWTVerifier;
import com.auth0.jwt.algorithms.Algorithm;
import com.auth0.jwt.interfaces.Claim;
import com.auth0.jwt.interfaces.DecodedJWT;
import java.util.Date;
/**
 * 在项目中jwt的定义
 */
public class JWTUtil {
    /**
     * 过期时间 5min过期
     */
    private static final long EXPIRE_TIME=5*60*1000;
    private static final String SECRIT = "WONIUXY";
    /**
     * @todo  这个验证方法是再生成一次秘钥，自动比较 当前token是不是之前生成的token
     * @param token  生成的JWT
     * @param username 账号
     * @param password 密码
     * @return
     */
    public static boolean verify(String token,String username,
                                 String password){
        //这里需要加密算法来对我们的woniuxy进行加密
        Algorithm algorithm = Algorithm.HMAC256(SECRIT);
        //重新生成一个token
        JWTVerifier verifier = JWT.require(algorithm).withClaim("username", username)
                .build();
        //比较两个token的内容
        verifier.verify(token);
        return true;
    }
    /**
     * 签名生成jwt
     * @param username
     * @param password
     * @return token
     */
    public static String sign(String username,String password){
        //过期时间
        Date exprietime = new Date(System.currentTimeMillis() + EXPIRE_TIME);
        //对唯一秘钥进行加密使用
        Algorithm algorithm = Algorithm.HMAC256(SECRIT);
        return JWT.create()
                .withClaim("username",username)
                .withExpiresAt(exprietime)
                .sign(algorithm);
    }
    /**
     * 用来传入token返回一个该token的用户名
     * @param token
     * @return
     */
    public static String getUserName(String token){
        DecodedJWT jwt = JWT.decode(token);
        Claim username = jwt.getClaim("username");
        return username.asString();
    }
}
```

#### 4.JWTFilter过滤器

```java
package com.woniuxy.shiro.utils;
import org.apache.shiro.web.filter.authc.BasicHttpAuthenticationFilter;
import org.springframework.http.HttpStatus;
import org.springframework.stereotype.Component;
import org.springframework.web.bind.annotation.RequestMethod;
import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
/**
 * @author: mayuhang  <br/>
 * Date: 2021/4/23:0:24  <br/>
 * Description:继承官方的BasicHttpAuthenticationFilter，并且重写鉴权的方法
 * 这个过滤器，是登录成功以后，使用JWT进行请求访问的时候，执行的过滤器
 * shiro自带的过滤器BasicHttpAuthenticationFilter它只支持账号密码验证，不支持我们的JWT
 * 代码的执行流程preHandle->isAccessAllowed->isLoginAttempt->executeLogin
 */
@Component
public class JWTFilter extends BasicHttpAuthenticationFilter {
    /**
     * 在这里需要解决的其实只有跨域的问题，全局跨域问题的解决方案
     * 虽然是前端解决跨域，但是我们还是要知道这里怎么配置
     */
    @Override
    protected boolean preHandle(ServletRequest request, ServletResponse response) throws Exception {
        HttpServletRequest httpServletRequest = (HttpServletRequest) request;
        HttpServletResponse httpServletResponse = (HttpServletResponse) response;
        httpServletResponse.setHeader("Access-control-Allow-Origin", httpServletRequest.getHeader("Origin"));
        httpServletResponse.setHeader("Access-Control-Allow-Methods", "GET,POST,OPTIONS,PUT,DELETE");
        httpServletResponse.setHeader("Access-Control-Allow-Headers", httpServletRequest.getHeader("Access-Control-Request-Headers"));
        // 跨域时会首先发送一个option请求，这里我们给option请求直接返回正常状态
        if (httpServletRequest.getMethod().equals(RequestMethod.OPTIONS.name())) {
            httpServletResponse.setStatus(HttpStatus.OK.value());
            return false;
        }
        return super.preHandle(request, response);
    }
    /**
     * 这里我们详细说明下为什么最终返回的都是true，即允许访问
     * 例如我们提供一个地址 GET /article
     * 登入用户和游客看到的内容是不同的
     * 如果在这里返回了false，请求会被直接拦截，用户看不到任何东西
     * 所以我们在这里返回true，Controller中可以通过 subject.isAuthenticated() 来判断用户是否登入
     * 如果有些资源只有登入用户才能访问，我们只需要在方法上面加上 @RequiresAuthentication 注解即可
     */
    @Override
    protected boolean isAccessAllowed(ServletRequest request, ServletResponse response, Object mappedValue) {
        if (isLoginAttempt(request, response)) {
            try {
                //如果有token
                executeLogin(request, response);
            } catch (Exception e) {
                //如果请求头没有带Authorization属性的token 就到这里咯
                response401(request, response);
            }
        }
        return true;
    }
    /**
     * 判断用户是否想要登入。
     * 检测header里面是否包含Authorization字段即可
     */
    @Override
    protected boolean isLoginAttempt(ServletRequest request, ServletResponse response) {
        HttpServletRequest hsr = (HttpServletRequest) request;
        String authorization = hsr.getHeader("Authorization");
        return authorization!=null;
    }
    @Override
    protected boolean executeLogin(ServletRequest request, ServletResponse response) throws Exception {
        HttpServletRequest httpServletRequest = (HttpServletRequest) request;
        //从请求头中获取token
        String authorization = httpServletRequest.getHeader("Authorization");
        //放入对象中, redis或者数据库查询  等同于之前的UsernamePasswordToken
        MyJsonWebToken token = new MyJsonWebToken(authorization);
        // 提交给realm进行登入，如果错误他会抛出异常并被捕获
        System.out.println("JWTFilter.executeLogintoken交给realm判断:"+token);
        //这个等同于直接的 subject.login(token)
        getSubject(request, response).login(token);
        // 如果没有抛出异常则代表登入成功，返回true
        return true;
    }
        /**
         * 将非法请求跳转到 /401
         */
    private void response401(ServletRequest req, ServletResponse resp) {
        try {
            HttpServletResponse httpServletResponse = (HttpServletResponse) resp;
            httpServletResponse.sendRedirect("/401");
        } catch (IOException e) {
            System.err.println(e.getMessage());
        }
    }
}
```

![img](../images/%E5%9B%BE%E7%89%871-16346957749411.png)

#### 5. 编写自定义域

```Java
package com.woniuxy.shiro;

import com.woniuxy.entity.RbacManager;
import com.woniuxy.entity.RbacPerm;
import com.woniuxy.service.RbacPermService;
import com.woniuxy.service.impl.UserService;
import com.woniuxy.utils.JWTUtil;
import org.apache.shiro.authc.*;
import org.apache.shiro.authz.AuthorizationInfo;
import org.apache.shiro.authz.SimpleAuthorizationInfo;
import org.apache.shiro.crypto.hash.Md5Hash;
import org.apache.shiro.crypto.hash.SimpleHash;
import org.apache.shiro.realm.AuthorizingRealm;
import org.apache.shiro.subject.PrincipalCollection;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Configuration;
import java.util.Arrays;
import java.util.HashSet;
import java.util.List;
/**
 * @author: mayuhang  <br/>
 * Date: 2021/4/23:0:02  <br/>
 * Description:自定义域  自己写登录验证 和授权
 */
@Configuration
public class MyRealm extends AuthorizingRealm {
    @Autowired
    UserService userService;
    @Autowired
    RbacPermService rbacPermService;
    @Override
    public void setName(String name){
        super.setName("myRealm");
    }
    // 这个需要注意下, 多域模式中, 每个域里面 请自己配置自己的token来源
    // 这里的token是来自 自定义的JWTToken
    // 必须重写此方法，不然 传的jwt, shiro却使用的是UsernamePasswordToken进行解析,
    // 会报一个错, 你token格式不对(JWT的值是3部分, UsernamePasswordToken只有1节....)
    @Override
    public boolean supports(AuthenticationToken token) {
        return token instanceof MyJsonWebToken;
    }
    /**
     * 授权信息
     * 在前后端不分离的情况下, 在html页面使用<shiro>|后端使用shiro的@checkRole,@checkPermission相关注解才能触发
     * 前后端分离的模式, 就直接用注解验证权限即可, 根据需求来
     */
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principals) {
        System.out.println("MyRealm.doGetAuthorizationInfo, 授权方法进入!!!");
        //这里得去获取到这个token  不用token过滤器 直接从redis里面拿数据!!
        String userName = JWTUtil.getUserName(principals.toString());
        //去数据库查出用户信息, 含角色(也可含权限, 看你实体怎么定义咯= =)
        RbacManager user = userService.getUser(userName);
        //授权信息集合
        SimpleAuthorizationInfo simpleAuthorizationInfo = new SimpleAuthorizationInfo();
        //1.如果需求是角色可以配置多个,则如下获取所有角色 并放入授权信息集合(模拟多角色查询,虽然目前是单的)
        String roles = String.valueOf(user.getRoleId());
        if (!"".equals(roles)&&roles!=null){
            String[] roles1 = roles.split(",");
            for (String role: roles1) {
                simpleAuthorizationInfo.addRole(role);
            }
        }
        //2.目前是单个角色则直接这样用吧.....
//       simpleAuthorizationInfo.addRole(user.getRoleId()+"");
        //3.通过角色, 去查询中间表 找到所有的权限  获取tyep字段,a接口权限link细节信息(同第1步)
        List<RbacPerm> forRole = rbacPermService.findForRole(roles);
        //4.转成HashSet去重存放
        HashSet<String> permissions = new HashSet<>();
        for (RbacPerm rbacPerm : forRole) {
            if(rbacPerm.getCode()!=null){
                permissions.add(rbacPerm.getCode());
            }
        }
        //5.放入simpleAuthorizationInfo对象中
        simpleAuthorizationInfo.addStringPermissions(permissions);
        return simpleAuthorizationInfo;
    }
    /**
     * 身份认证方法
     * 需要在用户登录系统时触发
     * 该方法将是我们主要的方法, 流程是登录后, 携带token 与权限信息 发送到 vue中 存储浏览器
     * 在页面渲染时,直接传入菜单和 页面按钮显示权限
     * @return
     * @throws AuthenticationException
     */
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken auth) throws AuthenticationException {
        System.out.println("MyRealm.doGetAuthenticationInfo, 用户认证方法进入!");
        String myJWT = (String) auth.getCredentials();
        String username = JWTUtil.getUserName(myJWT);
        if (username==null){
            throw new AuthenticationException("token无效");
        }
        //通过用户名,查出当前用户密码
        RbacManager user = userService.getUser(username);
        if (user==null){
            throw new AuthenticationException("User 不存在");
        }
        //通过存入的myJWT与传入的账号密码进行验证, 账号密码是否正确(类比之前手动比较密码~~~~)
        if (JWTUtil.verify(myJWT,username,user.getPassword())) {
            return new SimpleAuthenticationInfo(myJWT,myJWT,this.getName());
        }else {
            throw new AuthenticationException("用户名或者密码错误");
        }
    }
}
6. 编写配置类ShiroConfig
6.1 需要配置禁用session,安全管理器,shiro过滤器, 注解支持 ,生命周期,
  package com.woniuxy.shiro.utils;
import org.apache.shiro.mgt.SessionStorageEvaluator;
import org.apache.shiro.spring.LifecycleBeanPostProcessor;
import org.apache.shiro.spring.security.interceptor.AuthorizationAttributeSourceAdvisor;
import org.apache.shiro.spring.web.ShiroFilterFactoryBean;
import org.apache.shiro.web.mgt.DefaultWebSecurityManager;
import org.apache.shiro.web.mgt.DefaultWebSessionStorageEvaluator;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import javax.servlet.Filter;
import java.util.HashMap;
import java.util.Map;
/**
 * @author: mayuhang  <br/>
 * Date: 2021/4/23:0:35  <br/>
 * Description: shiro核心配置, 中央大脑 SecurityManager 相关配置 等同于之前玩 shiro-realm.ini的时候
 */
@Configuration
public class ShiroConfig {
    /**
     * 禁用session, 不保存用户登录状态。保证每次请求都重新认证。
     * 需要注意的是，如果用户代码里调用Subject.getSession()还是可以用session
     */
    @Bean
    protected SessionStorageEvaluator sessionStorageEvaluator() {
        DefaultWebSessionStorageEvaluator sessionStorageEvaluator = new DefaultWebSessionStorageEvaluator();
        sessionStorageEvaluator.setSessionStorageEnabled(false);
        return sessionStorageEvaluator;
    }
    //配置安全管理器, 传入自己写的自定义域
    @Bean
    public DefaultWebSecurityManager securityManager(MyRealm realm) {
        //使用默认的安全管理器
        DefaultWebSecurityManager securityManager = new
                DefaultWebSecurityManager(realm);
        //将自定义的realm交给安全管理器统一调度管理
        securityManager.setRealm(realm);
        return securityManager;
    }
    //Filter工厂，设置对应的过滤条件和跳转条件
    @Bean("shiroFilterFactoryBean")
    public ShiroFilterFactoryBean shirFilter(DefaultWebSecurityManager securityManager) {
        //1.创建shiro过滤器工厂
        ShiroFilterFactoryBean filterFactory = new ShiroFilterFactoryBean();
        //2.设置安全管理器
        filterFactory.setSecurityManager(securityManager);
        //3.通用配置,如果没有前后端分离配置这个（配置登录页面，登录成功页面，验证未成功页面）
//        filterFactory.setLoginUrl("/autherror?code=1"); //设置登录页面
//        filterFactory.setUnauthorizedUrl("/autherror?code=2"); //授权失败跳转页面
        //4.配置过滤器集合
        /**
         * key ：访问连接
         * 支持通配符的形式
         * value：过滤器类型
         * shiro常用过滤器类型
         * anno ：匿名访问（表明此链接所有人可以访问）
         * authc ：认证后访问（表明此链接需登录认证成功之后可以访问）
         */
//        Map<String,String> filterMap = new LinkedHashMap<String,String>();
        // 配置不会被拦截的链接 顺序判断
//        filterMap.put("/user/home", "anon");
//        filterMap.put("/login", "anon");
//        filterMap.put("/user/**", "authc");
        //5.设置自定义jwt过滤器
        Map<String, Filter> jwt = new HashMap<>();
        jwt.put("jwt",new JWTFilter());
        filterFactory.setFilters(jwt);
        /*
         * 6设置所有的请求都经过我们的JWTfilter
         * 自定义url规则
         * http://shiro.apache.org/web.html#urls-
         */
        Map<String, String> filterRuleMap = new HashMap<>();
        // 所有请求通过我们自己的JWT Filter
        filterRuleMap.put("/**", "jwt");
        // 访问401和404页面不通过我们的Filter
        filterRuleMap.put("/401", "anon");
        filterFactory.setFilterChainDefinitionMap(filterRuleMap);
        return filterFactory;
    }
    //开启shiro注解支持
    @Bean
    public AuthorizationAttributeSourceAdvisor
    authorizationAttributeSourceAdvisor(DefaultWebSecurityManager securityManager) {
        AuthorizationAttributeSourceAdvisor advisor = new
                AuthorizationAttributeSourceAdvisor();
        advisor.setSecurityManager(securityManager);
        return advisor;
    }
    /**
     * Description : 管理Subject主体对象,生命周期的组件,用户只是打印下生产销毁日志什么的,请参考spring中bean的生命周期   <br/>
     * ChangeLog : 1. 创建 (2021/4/27 0:25 [mayuhang]);
     *
     * @return org.apache.shiro.spring.LifecycleBeanPostProcessor
     **/
    @Bean
    public LifecycleBeanPostProcessor lifecycleBeanPostProcessor() {
        return new LifecycleBeanPostProcessor();
    }
}
```

#### 6. 编写配置类ShiroConfig

6.1 需要配置禁用session,安全管理器,shiro过滤器, 注解支持 ,生命周期,

```Java
package com.woniuxy.config;

import com.woniuxy.shiro.utils.MyRealmToken;
import com.woniuxy.utils.JWTFilter;
import org.apache.shiro.spring.LifecycleBeanPostProcessor;
import org.apache.shiro.spring.security.interceptor.AuthorizationAttributeSourceAdvisor;
import org.apache.shiro.spring.web.ShiroFilterFactoryBean;
import org.apache.shiro.web.mgt.DefaultWebSecurityManager;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import javax.servlet.Filter;
import java.util.HashMap;
import java.util.Map;

@Configuration//表示当前类是 配置类
public class ShiroConfig {

    /**
     * 配置安全管理器 SecurityManager
     * 参数，会自动通过spring容器注入DI，在SecurityManager中注入我们自己的realm
     */
    @Bean
    public DefaultWebSecurityManager securityManager(MyRealmToken myRealmToken){
        DefaultWebSecurityManager defaultWebSecurityManager = new DefaultWebSecurityManager();
        defaultWebSecurityManager.setRealm(myRealmToken);
        return defaultWebSecurityManager;
    }
    /**
     * Filter工厂，我们写了JWTFilter，但是对于shiro来说，shiro有自己的一套过滤器
     * 我们需要把我们的自定义过滤器，添加到shiro的过滤器链中 ==>Springsecurity类似
     */
    @Bean
    public ShiroFilterFactoryBean shiroFilterFactoryBean(DefaultWebSecurityManager securityManager){
        ShiroFilterFactoryBean shiroFilterFactoryBean = new ShiroFilterFactoryBean();
        //之前已经把自定义的域 放入了SecurityManager中，现在又把SecurityManager放入过滤器工厂中
        shiroFilterFactoryBean.setSecurityManager(securityManager);
        Map<String, Filter> filterMap = new HashMap<>();
        //放入我们的自定义过滤器
        filterMap.put("JWT",new JWTFilter());
        //放入我们自定义的过滤器 命名为JWT
        shiroFilterFactoryBean.setFilters(filterMap);
        //url访问过滤配置
        Map<String, String> urlMap = new HashMap<>();
        urlMap.put("/**","JWT");//所有请求都需要经过JWT过滤器
        urlMap.put("/login","anon");//允许匿名访问
        urlMap.put("/401","anon");
        //使用我们自定义过滤器的情况，是在/**表示所有页面都必须先经过过滤器的方法进行token认证
        shiroFilterFactoryBean.setFilterChainDefinitionMap(urlMap);
        return shiroFilterFactoryBean;
    }
    //开启shiro注解支持
    @Bean
    public AuthorizationAttributeSourceAdvisor
    authorizationAttributeSourceAdvisor(DefaultWebSecurityManager securityManager) {
        AuthorizationAttributeSourceAdvisor advisor = new
                AuthorizationAttributeSourceAdvisor();
        advisor.setSecurityManager(securityManager);
        return advisor;
    }
    /**
     * Description : 管理Subject主体对象,生命周期的组件,用户只是打印下生产销毁日志什么的,
     * 请参考spring中bean的生命周期   <br/>
     * ChangeLog : 1. 创建 (2021/4/27 0:25 [mayuhang]);
     *
     * @return org.apache.shiro.spring.LifecycleBeanPostProcessor
     **/
    @Bean
    public LifecycleBeanPostProcessor lifecycleBeanPostProcessor() {
        return new LifecycleBeanPostProcessor();
    }
}
```

6.2 Shiro中过滤器的类型，以及ShiroConfig配置

![img](../images/%E5%9B%BE%E7%89%871-16346957749411.png)

#### 7. Shiro 登录认证操作

完整的登录controller代码

```java
package com.woniuxy.Controller;
import com.woniuxy.entity.RbacManager;
import com.woniuxy.entity.ResponseEntity;
import com.woniuxy.exception.UnauthorizedException;
import com.woniuxy.service.impl.UserService;
import com.woniuxy.utils.JWTUtil;
import org.apache.shiro.SecurityUtils;
import org.apache.shiro.authc.UsernamePasswordToken;
import org.apache.shiro.authz.annotation.Logical;
import org.apache.shiro.authz.annotation.RequiresAuthentication;
import org.apache.shiro.authz.annotation.RequiresPermissions;
import org.apache.shiro.authz.annotation.RequiresRoles;
import org.apache.shiro.crypto.hash.SimpleHash;
import org.apache.shiro.subject.Subject;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.HttpStatus;
import org.springframework.web.bind.annotation.*;
/**
 * @author: mayuhang  <br/>
 * Date: 2021/4/22:23:52  <br/>
 * Description:
 */
@RestController
public class UserController {
    @Autowired
    UserService userService;
    @PostMapping("/login")
    public ResponseEntity login(@RequestParam("username") String username,
                                @RequestParam("password") String password) {
        //根据注册的时候 或者修改manager里面的加密方式来, 只要一样即可
            SimpleHash md5 = new SimpleHash("MD5", password, username);
            String newPassword = md5.toHex();
            RbacManager user = userService.getUser(username);
            if (user.getPassword().equals(newPassword)) {
             //1.用了jwt而且不用多域模式  就不要用这个来登录了
//            Subject subject = SecurityUtils.getSubject();
//            UsernamePasswordToken token = new UsernamePasswordToken(username, newPassword);
//            System.out.println("UserController.login:"+token.toString());
//            subject.login(token);
                return new ResponseEntity(200, "登录成功", JWTUtil.sign(username, newPassword));
            } else {
                throw new UnauthorizedException();
            }
    }
    /**
     * Description : 不携带token直接请求的话 就是游客咯  <br/>
     * ChangeLog : 1. 创建 (2021/5/3 22:15 [mayuhang]);
     *
     * @return com.woniuxy.entity.ResponseEntity
     **/
    @GetMapping("/article")
    public ResponseEntity article() {
        Subject subject = SecurityUtils.getSubject();
        if (subject.isAuthenticated()) {
            return new ResponseEntity(200, "您已经登录", null);
        } else {
            return new ResponseEntity(200, "You are guest", null);
        }
    }
    /**
     * Description : 加注解 判断是否认证过的, 等同于之前的subject.isAuthenticated()  <br/>
     * ChangeLog : 1. 创建 (2021/5/3 22:51 [mayuhang]);
     *
     * @return com.woniuxy.entity.ResponseEntity
     **/
    @GetMapping("/require_auth")
    @RequiresAuthentication
    public ResponseEntity requireAuth() {
        return new ResponseEntity(200, "恭喜您登录成功,可以获取菜单数据了!", null);
    }
    /**
     * Description : 是否有某个角色, 等同于subject.hasRole,有多个的话 参考下面权限判断  <br/>
     * ChangeLog : 1. 创建 (2021/5/3 22:53 [mayuhang]);
     *
     * @return com.woniuxy.entity.ResponseEntity
     **/
    @GetMapping("/require_role")
    @RequiresRoles("1")
    public ResponseEntity requireRole() {
        return new ResponseEntity(200, "你有超级管理员角色", null);
    }
    /**
     * Description : 是否有某个权限, 等同于subject.isPermitted  <br/>
     * ChangeLog : 1. 创建 (2021/5/3 22:53 [mayuhang]);
     *
     * @return com.woniuxy.entity.ResponseEntity
     **/
    @GetMapping("/require_permission")
    @RequiresPermissions(logical = Logical.OR, value = {"role:view", "perm_manage"})
    public ResponseEntity requirePermission() {
        return new ResponseEntity(200, "您正在访问权限，需要编辑，perm_manage权限的接口", null);
    }
    /**
     * Description : 所有401等权限认证, 错误的, 都到这里来~  <br/>
     * ChangeLog : 1. 创建 (2021/5/3 22:54 [mayuhang]);
     *
     * @return com.woniuxy.entity.ResponseEntity
     **/
    @RequestMapping(path = "/401")
    @ResponseStatus(HttpStatus.UNAUTHORIZED)
    public ResponseEntity unauthorized() {
        return new ResponseEntity(401, "未经授权", null);
    }
}
```

## 补充

![Snipaste_2021-10-19_14-12-09](../images/Snipaste_2021-10-19_14-12-09.png)

![shiro流程图](../images/shiro%E6%B5%81%E7%A8%8B%E5%9B%BE.png)

前后端不分离，第一种验证方式，使用usernamepasswordToken

前后端分离，使用第四种验证方式，使用JWTtoken

# Redis

## 一、redis概念

**优缺点**

关系型数据库(RDBMS-SQL)的缺陷：

1. 关系型数据库关注的是表与表之间，数据与数据之间的关系
2. 关系型数据库查询数据效率低(是去磁盘中查询)
3. 关系型数据库表设计的时候，结构化固定，过于单一(数据类型要固定)
4. 关系型数据库动态修改表结构、数据结构非常麻烦(代价非常高昂)

非关系型数据库(NOSQL=Not only Sql)的优点：

1. 性能较高，因为直接从内存中读取
2. key-value结构，这个结构简单，变化容易，只需要修改value的数据类型就ok。key通常都是字符串。
3. value的数据类型丰富，既可以支持字符串，也支持对象，还支持集合，甚至，视频，图片，音频等内容。

常见的非关系型数据库：memCache、redis、hbase、bigtable、MongoDB……

**Redis介绍**

1. 官网:[https://redis.io](https://redis.io/)
2. 中文网:http://www.redis.net.cn/
3. 下载windows版本连接:https://github.com/tporadowski/redis/releases

**Redis特点**

1. Redis支持持久化，把内存中的数据存到磁盘中，重启的时候，可以从磁盘中加载回来。
2. value可以支持的数据类型：`String，list，set，zset，hash`等数据结构。
3. Redis支持数据备份，支持对数据的多机热备份，即`master-slave模式的数据备份`。支持`哨兵`模式，自动主从切换。

**Redis的优点：**

1. 性能极强，Redis读数据的速度是110000次/s，写的速度81000次/s；
2. 丰富的数据类型 ，Redis支持二进制格式的 String, List, Hash, Set 及 Ordered Set 数据类型操作。
3. 原子性 ，Redis的所有操作都是原子性的，意思就是要么成功执行要么失败完全不执行。单个操作是原子性的。多个操作也支持事务，即原子性，通过MULTI和EXEC指令包起来。
4. 丰富的特性 – Redis还支持 publish/subscribe, 通知, key 过期等等特性。

## 二、Redis的基础命令

redis存储KEY-value：不同的value它的命令是完全不同的。我们只学5中类型的value的命令。

1. 字符串：String
2. hash类型：Hash
3. 列表类型：list
4. 集合类型：set
5. 有序集合类型：sortedset 通常称之为zset

Redis速度快原因：

c语言实现，距离系统更近；数据都在内存中；单线程，避免线程切换开销以及多线程的竞争问题；采用epoll，非阻塞I/O，不在网络上浪费时间

### 第一步：

**启动Redis服务（server）**

第一种方式：双击这个`redis-server.exe`。以默认配置进行启动默认端口6379

第二种方式：通过cmd启动，cmd首先必须进入redis项目的根目录，输入命令：`redis-server.exe redis.windows.conf`，按照指定的配置文件启动服务。

redis.windows.conf ： redis启动需要的配置文件，可以改端口。

**启动Redis客户端(client)**

第一种方式：双击`redis-cli.exe`。默认以127.0.0.1和端口6379，连接redis数据库。

第二种方式：通过cmd启动，cmd首先必须进入redis项目的根目录，输入命令：

`redis-cli.exe -h 127.0.0.1 -p 6379  --raw`，可以进行指定ip和端口进行连接。默认是使用的6379端口进行连接。 --raw 可以存储中文，然后取出来也是中文。

### 第二步：

在客户端的窗口上进行命令测试：

#### 1.针对key的命令

| 序号 | 命令                                 | 描述                                                         |
| :--- | :----------------------------------- | :----------------------------------------------------------- |
| 1    | `DEL key`                            | 该命令用于在 key 存在时删除 key                              |
| 2    | DUMP key                             | 序列化给定 key ，并返回被序列化的值                          |
| 3    | `EXISTS key`                         | 检查给定 key 是否存在                                        |
| 4    | EXPIRE key seconds                   | 为给定 key 设置过期时间，以秒为单位                          |
| 5    | EXPIREAT key timestamp               | EXPIREAT 的作用和 EXPIRE 类似，都用于为 key 设置过期时间。 不同在于 EXPIREAT 命令接受的时间参数是 UNIX 时间戳(unix timestamp)。 |
| 6    | PEXPIRE key milliseconds             | 设置 key 的过期时间以毫秒计。                                |
| 7    | PEXPIREAT key milliseconds-timestamp | 设置 key 过期时间的时间戳(unix timestamp) 以毫秒计           |
| 8    | KEYS pattern                         | 查找所有符合给定模式( pattern)的 key                         |
| 9    | MOVE key db                          | 将当前数据库的 key 移动到给定的数据库 db 当中                |
| 10   | PERSIST key                          | 移除 key 的过期时间，key 将持久保持                          |
| 11   | PTTL key                             | 以毫秒为单位返回 key 的剩余的过期时间                        |
| 12   | TTL key                              | 以秒为单位，返回给定 key 的剩余生存时间(TTL, time to live)   |
| 13   | RANDOMKEY                            | 从当前数据库中随机返回一个 key                               |
| 14   | RENAME key newkey                    | 修改 key 的名称                                              |
| 15   | RENAMENX key newkey                  | 仅当 newkey 不存在时，将 key 改名为 newkey                   |
| 16   | `TYPE key`                           | 返回 key 所储存的值的类型                                    |

> del key : 删除某个key
>
> exists key：判断有没有这个key的值
>
> type key：判断这个key的value的类型
>
> keys *：表示遍历所有的key显示出来
>
> keys a*: 表示遍历a开头的后面任意字符都能查出来
>
> keys a?：表示遍历a开头，后面只有一个任意字符的key出来

#### 2.针对String类型的命令

String 是Redis中的二进制字符串类型，它可以代表所有的数据类型

string 是 redis 最基本的类型，一个 key 对应一个 value，是二进制安全的。

注意:string 类型的值最大能存储 512MB。

> 这个类型是在企业开发中用得最多的类型。

| 序号 | 命令                           | 描述                                                         |
| :--- | :----------------------------- | :----------------------------------------------------------- |
| 1    | **set key value**              | 设置指定 key 的值                                            |
| 2    | **get key**                    | 获取指定 key 的值                                            |
| 3    | GETRANGE key start end         | 返回 key 中字符串值的子字符[start,end]                       |
| 4    | GETSET key value               | 将给定 key 的值设为 value ，并返回 key 的旧值(old value)     |
| 5    | GETBIT key offset              | 对key 所储存的字符串值，获取指定偏移量上的位(bit)            |
| 6    | MGET key1 [key2..]             | 获取所有(一个或多个)给定 key 的值                            |
| 7    | SETBIT key offset value        | 对key所储存的字符串值，设置或清除指定偏移量上的位(bit)       |
| 8    | SETEX key seconds value        | 将值 value 关联到 key ，并将 key 的过期时间设为 seconds (以秒为单位) |
| 9    | SETNX key value                | 只有在 key 不存在时设置 key 的值                             |
| 10   | SETRANGE key offset value      | 用 value 参数覆写给定 key 所储存的字符串值，从偏移量 offset 开始 |
| 11   | STRLEN key                     | 返回 key 所储存的字符串值的长度                              |
| 12   | MSET key value [key value …]   | 同时设置一个或多个 key-value 对                              |
| 13   | MSETNX key value [key value …] | 同时设置一个或多个 key-value 对，当且仅当所有给定 key 都不存在 |
| 14   | PSETEX key milliseconds value  | 这个命令和 SETEX 命令相似，但它以毫秒为单位设置 key 的生存时间，而不是像 SETEX 命令那样，以秒为单位 |
| 15   | INCR key                       | 将 key 中储存的数字值增一                                    |
| 16   | INCRBY key increment           | 将 key 所储存的值加上给定的增量值（increment）               |
| 17   | INCRBYFLOAT key increment      | 将 key 所储存的值加上给定的浮点增量值（increment）           |
| 18   | DECR key                       | 将 key 中储存的数字值减一                                    |
| 19   | DECRBY key decrement           | key 所储存的值减去给定的减量值（decrement）                  |
| 20   | append key value               | 如果 key 已经存在并且是一个字符串， APPEND 命令将指定的 value 追加到该 key 原来值（value）的末尾 |

总结一下：String 类型主要用于存储 简单值。

#### 3.针对Hash类型的命令

Redis hash类型的value是一个(key == value)格式。

> **命令的格式：**
>
>  存储使用HSET 或 HMSET命令：
>
> ​		`HSET key field value`
>
>  获取HGET命令：
>
>  ​		`HGET key field 可以获取指定的field的值`
>
>  ​		`HGETALL key 可以获取所有的hash(field和value)`
>
>  删除HDEL命令：
>
>  ​		`HDEL key field`

每个 hash 可以存储很多对键值对，最多可以存储2^32 -1 键值对（40多亿）。

| 序号 | 命令                                           | 描述                                                  |
| :--- | :--------------------------------------------- | :---------------------------------------------------- |
| 1    | **HDEL key field1 [field2]**                   | 删除一个或多个哈希表字段                              |
| 2    | HEXISTS key field                              | 查看哈希表 key 中，指定的字段是否存在                 |
| 3    | **HGET key field**                             | 获取存储在哈希表中指定字段的值                        |
| 4    | **HGETALL key**                                | 获取在哈希表中指定 key 的所有字段和值                 |
| 5    | HINCRBY key field increment                    | 为哈希表 key 中的指定字段的整数值加上增量 increment   |
| 6    | HINCRBYFLOAT key field increment               | 为哈希表 key 中的指定字段的浮点数值加上增量 increment |
| 7    | **HKEYS key**                                  | 获取所有哈希表中的字段                                |
| 8    | HLEN key                                       | 获取哈希表中字段的数量                                |
| 9    | HMGET key field1 [field2]                      | 获取所有给定字段的值                                  |
| 10   | HMSET key field1 value1 [field2 value2 ]       | 同时将多个 field-value (域-值)对设置到哈希表 key 中   |
| 11   | **HSET key field value**                       | 将哈希表 key 中的字段 field 的值设为 value            |
| 12   | HSETNX key field value                         | 只有在字段 field 不存在时，设置哈希表字段的值         |
| 13   | **HVALS key**                                  | 获取哈希表中所有值                                    |
| 14   | HSCAN key cursor [MATCH pattern] [COUNT count] | 迭代哈希表中的键值对                                  |

总结一下：Hash 类型主要用于存储对象

#### 4.针对List类型的命令

> 格式：
>
> 1. 添加
>
>    LPUSH key value 从左边放入数据
>
>    RPUSH key value 从右边放入数据
>
> 2. 查询
>
>    LRANGE key start end 从左往右 以start开始 以end结尾的范围内的所有值
>
>    end如果是-1 表示的是 右边的一位数据，依次逆推，左边第一个如下图，其实是-3 但是它也是0

![image-20211021153030369](../images/202110211530471.png)

> 1. 弹出再删除(弹栈)
>
>    LPOP key 弹出左边第一个元素
>
>    RPOP key 弹出右边第一个元素
>
> 2. 直接删除
>
>    LREM key count value 直接从左往右依次移除count个value值

| 序号  | 命令                                  | 描述                                                         |                              |
| :---- | :------------------------------------ | :----------------------------------------------------------- | ---------------------------- |
| 1     | BLPOP key1 [key2 ] timeout            | 移出并获取列表的第一个元素，如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止 |                              |
| 2     | BRPOP key1 [key2 ] timeout            | 移出并获取列表的最后一个元素， 如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止 |                              |
| 3     | BRPOPLPUSH source destination timeout | 从列表中弹出一个值，将弹出的元素插入到另外一个列表中并返回它； 如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止 |                              |
| 4     | LINDEX key index                      | 通过索引获取列表中的元素                                     |                              |
| 5     | LINSERT key BEFORE\                   | AFTER pivot value                                            | 在列表的元素前或者后插入元素 |
| 6     | LLEN key                              | 获取列表长度                                                 |                              |
| 7     | **LPOP key**                          | 移出并获取列表的第一个元素                                   |                              |
| **8** | **LPUSH key value1 [value2]**         | 将一个或多个值插入到列表头部                                 |                              |
| 9     | LPUSHX key value                      | 将一个值插入到已存在的列表头部                               |                              |
| 10    | **LRANGE key start stop**             | 获取列表指定范围内的元素                                     |                              |
| 11    | **LREM key count value**              | 移除列表元素                                                 |                              |
| 12    | LSET key index value                  | 通过索引设置列表元素的值                                     |                              |
| 13    | LTRIM key start stop                  | 对一个列表进行修剪(trim)，就是说，让列表只保留指定区间内的元素，不在指定区间之内的元素都将被删除 |                              |
| 14    | **RPOP key**                          | 移除列表的最后一个元素，返回值为移除的元素                   |                              |
| 15    | RPOPLPUSH source destination          | 移除列表的最后一个元素，并将该元素添加到另一个列表并返回     |                              |
| 16    | **RPUSH key value1 [value2]**         | 在列表中添加一个或多个值                                     |                              |
| 17    | RPUSHX key value                      | 为已存在的列表添加值                                         |                              |

总结一下：List类型，就是双向链表 主要用于存储 集合数据

#### 5.无序不重复的集合SET类型的命令

> 格式：
>
> ​		插入：sadd key value
>
> ​		获取：smembers key
>
> ​		删除：srem key value

| 序号 | 命令                                           | 描述                                                |
| :--- | :--------------------------------------------- | :-------------------------------------------------- |
| 1    | **SADD key member1 [member2]**                 | 向集合添加一个或多个成员                            |
| 2    | SCARD key                                      | 获取集合的成员数                                    |
| 3    | SDIFF key1 [key2]                              | 返回给定所有集合的差集                              |
| 4    | SDIFFSTORE destination key1 [key2]             | 返回给定所有集合的差集并存储在 destination 中       |
| 5    | SINTER key1 [key2]                             | 返回给定所有集合的交集                              |
| 6    | SINTERSTORE destination key1 [key2]            | 返回给定所有集合的交集并存储在 destination 中       |
| 7    | SISMEMBER key member                           | 判断 member 元素是否是集合 key 的成员               |
| 8    | **SMEMBERS key**                               | 返回集合中的所有成员                                |
| 9    | SMOVE source destination member                | 将 member 元素从 source 集合移动到 destination 集合 |
| 10   | SPOP key                                       | 移除并返回集合中的一个**随机元素**                  |
| 11   | SRANDMEMBER key [count]                        | 返回集合中一个或多个随机数                          |
| 12   | **SREM key member1 [member2]**                 | 移除集合中一个或多个成员                            |
| 13   | SUNION key1 [key2]                             | 返回所有给定集合的并集                              |
| 14   | SUNIONSTORE destination key1 [key2]            | 所有给定集合的并集存储在 destination 集合中         |
| 15   | SSCAN key cursor [MATCH pattern] [COUNT count] | 迭代集合中的元素                                    |

总结一下：Set 集合， 实际上就是HashSet的用法

#### 6.有序不重复集合ZSET类型的命令

ZSET是通过一个分数字段来进行排序！

> 格式：
>
> 1. 存储：ZADD key score value score表示分数
> 2. 获取：ZRANGE key start end
> 3. 删除: ZREM key value

| 序号   | 命名                                           | 描述                                                         |
| :----- | :--------------------------------------------- | :----------------------------------------------------------- |
| 1      | **`ZADD key score1 member1 [score2 member2]`** | 向有序集合添加一个或多个成员，或者更新已存在成员的分数       |
| 2      | ZCARD key                                      | 获取有序集合的成员数（length）                               |
| 3      | ZCOUNT key min max                             | 计算在有序集合中指定区间分数的成员数                         |
| 4      | ZINCRBY key score member                       | 有序集合中对指定成员的分数加上增量 increment                 |
| 5      | ZINTERSTORE destination numkeys key [key …]    | 计算给定的一个或多个有序集的交集并将结果集存储在新的有序集合 key 中ZINTERSTORE sum_point 2 mid_test fin_test |
| 6      | **`ZRANGE key start stop [WITHSCORES]`**       | 通过索引区间返回有序集合成指定区间内的成员                   |
| 7      | ZRANGEBYSCORE key min max [WITHSCORES] [LIMIT] | 通过分数返回有序集合指定区间内的成员ZRANGEBYSCORE grades (1 100 1<score<=5ZRANGEBYSCORE grades (5 (100 5<score<100 |
| 8      | ZRANK key member                               | 返回有序集合中指定成员的索引                                 |
| 9      | **`ZREM key member [member …]`**               | 移除有序集合中的一个或多个成员                               |
| 10     | ZREMRANGEBYRANK key start stop                 | 移除有序集合中给定的排名区间的所有成员ZREMRANGEBYRANK salary 0 1 # 移除下标 0 至 1 区间内的成员 |
| 1**1** | **`ZREMRANGEBYSCORE key min max`**             | 移除有序集合中给定的分数区间的所有成员ZREMRANGEBYSCORE salary 1500 3500 # 移除所有薪水在 1500 到 3500 内的员工 |
| 12     | ZREVRANGE key start stop [WITHSCORES]          | 返回有序集中指定区间内的成员，通过索引，分数从高到底ZREVRANGE salary 0 -1 WITHSCORES # 递减排列 |
| 13     | ZREVRANK key member                            | 返回有序集合中指定成员的排名，有序集成员按分数值递减(从大到小)排序ZREVRANK salary tom # 结果为0，tom 的工资最高 |
| 14     | **`ZSCORE key member`**                        | 返回有序集中，成员的分数值                                   |
| 15     | ZUNIONSTORE destination numkeys key [key …]    | 计算给定的一个或多个有序集的并集，并存储在新的 key 中ZUNIONSTORE salary 2 programmer manager WEIGHTS 1 3两个集合中scope的乘法系数 |

总结一下：Sorted Set 又叫ZSET ，它是属于有序集合，它是按照定义的分数值进行从小到大的排序规则来进行排序。

如果要修改排序，就修改分数值。

## 补充

同一个redis，以不同的配置文件启动，同时操作数据，会根据配置文件的不同建立不同的数据库，库之间的数据独立，不会相互污染。

cmd运行redis客户端：`redis-cli`

![image-20211021143217101](../images/image-20211021143217101.png)

redis存储中文，取出也是中文：以`redis-cli.exe --raw`启动

![image-20211021143501243](../images/image-20211021143501243.png)

输完命令安TAB，切换大小写

![image-20211021144636404](../images/image-20211021144636404.png)



# redis+shiro整合

## 一、复习

常用的5个数据结构：

String：SET key value ; GET key;

Hash：HSET key field value; HGET key field;

List：LPUSH key value; RPUSH key value; LRANGE key start end;

Set：SADD key value; smembers key; 无序不重复

ZSet：ZADD key score value; ZRANGE key start end [score];

Jedis:根据官网来操作即可,https://www.redis.net.cn/tutorial/3525.html

```java
>> 算数右移   除以2   负数除以二也是 负数    1000 0111 >>2   1010 0001
>>> 无符号右移(逻辑右移)   完整移动整体数值
    1100 1001 0101 0101 1010 1101 0100 >>> 2
    0011 0010 0101 0101 0110 1011 0101
```

## 二、redis和login登录整合

今天的需求，shiro整合redis。就是在springboot项目中，引入redis，使用redis来存储我们的JWT。我们首先修改原本的JWT生成逻辑，JWTUtil进行一次调整，取消掉jwt的自动过期时间，取消token判断，判断有没有过期或者被篡改。

### 第一步：

```java
package com.woniuxy.utils;
import com.auth0.jwt.JWT;
import com.auth0.jwt.JWTVerifier;
import com.auth0.jwt.algorithms.Algorithm;
import com.auth0.jwt.interfaces.Claim;
import com.auth0.jwt.interfaces.DecodedJWT;
import org.apache.catalina.core.ApplicationContext;
import org.springframework.beans.factory.BeanFactory;
import java.util.Date;
/**
 * 在项目中jwt的定义
 */
public class JWTUtil {
    /**
     * 过期时间 5min过期  我们引入redis以后，就不用这里来配置过期时间！
     */
//    private static final long EXPIRE_TIME=5*60*1000;
    /**
     * @todo  这个验证方法是再生成一次秘钥，自动比较 当前token是不是之前生成的token
     * @param token  认证的JWT   传入token，通过账号密码重新生成token和我们传入的token进行认证
     *               目前我们 有了redis以后，还需要重新生成jwt来进行认证么？
     * @param username 账号
     * @param secrit 密码 动态签名
     * @return
     */
    @Deprecated
    public static boolean verify(String token,String username,String secrit){
        //这里需要加密算法来对我们的密码进行加密
        Algorithm algorithm = Algorithm.HMAC256(secrit);
        //重新生成一个token
        JWTVerifier verifier = JWT.require(algorithm).withClaim("username", username)
                .build();
        //比较两个token的内容
        verifier.verify(token);
        return true;
    }
    /**
     * 签名生成jwt
     * @param username
     * @param secrit 当做密码
     * @return token
     */
    public static String sign(String username,String secrit){
        //过期时间 取消 将由redis进行过期判断
//        Date exprietime = new Date(System.currentTimeMillis() + EXPIRE_TIME);
        //对唯一秘钥进行加密使用
        Algorithm algorithm = Algorithm.HMAC256(secrit);
        return JWT.create()
                .withClaim("username",username)
                .sign(algorithm);
    }
    /**
     * 用来传入token返回一个该token的用户名
     * @param token
     * @return
     */
    public static String getUserName(String token){
        DecodedJWT jwt = JWT.decode(token);
        Claim username = jwt.getClaim("username");
        return username.asString();
    }
}
```

### 第二步：

引入依赖：

```xml
<!-- redis在springboot中整合的启动器starter -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

完整的依赖：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.4.11</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.woniuxy</groupId>
    <artifactId>SpringBootAll</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>SpringBootAll</name>
    <description>Demo project for Spring Boot</description>
    <properties>
        <java.version>1.8</java.version>
    </properties>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jdbc</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>2.2.0</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>com.github.pagehelper</groupId>
            <artifactId>pagehelper-spring-boot-starter</artifactId>
            <version>1.3.0</version>
        </dependency>
        <dependency>
            <groupId>io.jsonwebtoken</groupId>
            <artifactId>jjwt</artifactId>
            <version>0.9.1</version>
        </dependency>
<!--     入门程序需要引入的代码   -->
<!--        <dependency>-->
<!--            <groupId>org.apache.shiro</groupId>-->
<!--            <artifactId>shiro-core</artifactId>-->
<!--            <version>1.7.1</version>-->
<!--        </dependency>-->
<!--      基于springboot的shiro整合  -->
        <dependency>
            <groupId>org.apache.shiro</groupId>
            <artifactId>shiro-spring-boot-starter</artifactId>
            <version>1.7.1</version>
        </dependency>
<!--        引入jwt-->
        <dependency>
            <groupId>com.auth0</groupId>
            <artifactId>java-jwt</artifactId>
            <version>3.10.3</version>
        </dependency>
<!--        引入jedis依赖-->
        <dependency>
            <groupId>redis.clients</groupId>
            <artifactId>jedis</artifactId>
            <version>3.7.0</version>
        </dependency>
<!--    redis在springboot中整合的启动器starter  -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
        </dependency>
    </dependencies>
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <excludes>
                        <exclude>
                            <groupId>org.projectlombok</groupId>
                            <artifactId>lombok</artifactId>
                        </exclude>
                    </excludes>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```

### 第三步：

修改配置文件

```yaml
server:
  port: 8081
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/woniu_k15?serverTimezone=UTC
    username: root
    password: 123456
  devtools:
    remote:
      context-path: com.woniuxy
#  redis的配置文件 虽然host和port是默认的，以后万一要修改
  redis:
    password: foo
    host: localhost
    port: 6379
mybatis:
  mapper-locations: mapper/*.xml
pagehelper:
  helper-dialect: mysql
  #分页合理化参数，默认值为 false 。当该参数设置为 true 时，
  # pageNum<=0 时会查询第一页， pageNum>pages （超过总数时），
  # 会查询最后一页。默认 false 时，直接根据参数进行查询。
  reasonable: true
logging:
  level:
    com.woniuxy.dao: debug
```

### 第四步：

业务修改，首先修改我们的login方法，登录方法，把controller中的业务，抽取到service中去写。

controller登录方法代码如下：service自己去git中看

```java
package com.woniuxy.controller;
import com.woniuxy.entity.RbacManager;
import com.woniuxy.entity.RbacManagerDTO;
import com.woniuxy.entity.ResponseEntity;
import com.woniuxy.service.RbacManagerService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
@RestController
public class LoginController {
    @Autowired
    RbacManagerService rbacManagerService;
    /**
     * 登录成功后 返回token，给前端存起来
     * @param username
     * @param password
     * @return
     */
    @RequestMapping("/login")
    public ResponseEntity login(String username, String password){
        //通过账号密码进行数据匹配验证，验证密码是否正确  (我不给你们提供笔记，这里自己写)
        boolean flag = rbacManagerService.verifyMyPassword(username,password);
        //flag为true说明密码是匹配的  如果账号密码正确，生成token存入redis，返回token给前端
        if (flag) {
            //生成token
            String token = rbacManagerService.sign(username, password);
            //通过用户名或密码获取rbacManager对象
            RbacManager rbacManager = rbacManagerService.findManager(username);
            //获取到token和对象以后，我们需要去把token当做value存到redis数据库中，
            // key就是我们的token value 我的manager对象
            boolean isSaved = rbacManagerService.saveTokenToRedis(token, rbacManager);
            if (isSaved){
                //redis保存成功后 含token的对象token
                RbacManagerDTO rbacManagerDTO = new RbacManagerDTO();
                rbacManager.setPassword("");
                rbacManagerDTO.setRbacManager(rbacManager);
                rbacManagerDTO.setToken(token);
                return new ResponseEntity<>(rbacManagerDTO);
            }else {
                ResponseEntity<Object> objectResponseEntity = new ResponseEntity<>();
                objectResponseEntity.setMessage("服务器维护中……");
                return objectResponseEntity;
            }
        }else{
            ResponseEntity<Object> objectResponseEntity = new ResponseEntity<>();
            objectResponseEntity.setMessage("账号或密码错误！");
            return objectResponseEntity;
        }
    }
//    @RequestMapping("/verify")
//    public String verify(String token){
//        Subject subject = SecurityUtils.getSubject();
//        subject.login(new MyJsonWebToken(token));
//        System.out.println(subject.isAuthenticated());
//        return "success";
//    }
    @RequestMapping("/401")
    public String response401(){
        return "对不起，您没有登录。";
    }
}
```

## 三、JWT和shiro认证流程

### 第一步 filter后端获取token

前提条件，前端必须储存好token！我们使用的是第四类认证机制(TOKEN认证)。

前端代码，必须在所有的请求发起的时候，在请求头中携带Token。

并且后端必须提供一个实体类来存放我们的token。替换shiro自带的UsernamePasswordToken

```java
package com.woniuxy.shiro.utils;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
import org.apache.shiro.authc.AuthenticationToken;
/**
 * 为什么要自定义这个类？
 * 因为我们的认证机制使用的是第四种，JWT的认证，我们后端不再仅仅只接受账号密码，
 * 我们的认证仅仅只需要一个token字符串即可认证成功。
 */
@Data
@AllArgsConstructor
@NoArgsConstructor
public class MyJsonWebToken implements AuthenticationToken {
    String token;//我们的认证仅仅只需要提供token即可不需要账号密码
    @Override
    public Object getPrincipal() {
        return token;
    }
    @Override
    public Object getCredentials() {
        return token;
    }
}
```

前端请求拦截器，必须要有：main.js

在所有请求的时候，在请求头中，加入Authorization属性，value是token的值。

```java
// 添加请求拦截器，在请求头部加token
axios.interceptors.request.use(config =>{
    if(localStorage.getItem('token')){
      config.headers.Authorization = localStorage.getItem('token');
    }
    return config;
  },
  error =>{
    return Promise.reject(error);
});
```

![image-20211022141324959](../images/202110221413090.png)

第一步：

前置条件搞定以后，后端就需要`想办法获取`到请求头中的Authorization属性的数据。

就要使用一个过滤器的filter的代码，过滤器中，我们只是获取前端传来的token，并不会去判断token过没过期和有没有被篡改。最后一步：subject.login(AuthenticationToken 封装的token)

```java
package com.woniuxy.utils;
import com.woniuxy.shiro.utils.MyJsonWebToken;
import org.apache.shiro.web.filter.authc.BasicHttpAuthenticationFilter;
import org.springframework.http.HttpStatus;
import org.springframework.web.bind.annotation.RequestMethod;
import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
/**
 * 过滤器，
 * preHandle(第一关，所有的过滤器，优先进入这个方法，返回为true才允许controller)
 * -->isAccessAllowed
 * -->isLoginAttempt
 * -->executeLogin
 */
public class JWTFilter extends BasicHttpAuthenticationFilter {
    /**
     * 在这里需要解决的其实只有跨域的问题，全局跨域问题的解决方案
     * 虽然是前端解决跨域，但是我们还是要知道这里怎么配置
     * 这个过滤器，是登录成功以后，使用JWT进行请求访问的时候，执行的过滤器
     * shiro自带的过滤器BasicHttpAuthenticationFilter它只支持账号密码验证，不支持我们的JWT
     * @param request
     * @param response
     * @return
     * @throws Exception
     */
    @Override
    protected boolean preHandle(ServletRequest request, ServletResponse response) throws Exception {
        HttpServletRequest httpServletRequest = (HttpServletRequest) request;
        HttpServletResponse httpServletResponse = (HttpServletResponse) response;
        httpServletResponse.setHeader("Access-control-Allow-Origin", httpServletRequest.getHeader("Origin"));
        httpServletResponse.setHeader("Access-Control-Allow-Methods", "GET,POST,OPTIONS,PUT,DELETE");
        httpServletResponse.setHeader("Access-Control-Allow-Headers", httpServletRequest.getHeader("Access-Control-Request-Headers"));
        // 跨域时会首先发送一个option请求，这里我们给option请求直接返回正常状态
        if (httpServletRequest.getMethod().equals(RequestMethod.OPTIONS.name())) {
            httpServletResponse.setStatus(HttpStatus.OK.value());
            return false;
        }
        return super.preHandle(request, response);
    }
    /**
     * 尝试登录，看是否已经登录了
     * 没有登录跳转401页面，登录成功就进入shiro的subject.login(MyJsonWebToken)
     * @param request
     * @param response
     * @param mappedValue
     * @return
     */
    @Override
    protected boolean isAccessAllowed(ServletRequest request, ServletResponse response, Object mappedValue) {
        // 必须确保前端有token，我们才能通过token执行登录流程
        //前端token是必须登录成功以后，返回给前端这个token，然后前端把token存起来命名key=Authorization
        if(isLoginAttempt(request,response)){
            try {
                executeLogin(request,response);
            } catch (Exception e) {
                e.printStackTrace();
//                如果说出现异常，我们就需要返回浏览器一个401信息
                response401(request,response);
            }
        }
        return true;
    }
    private void response401(ServletRequest request, ServletResponse response) {
        HttpServletResponse response1 = (HttpServletResponse) response;
        try {
            //这里会跳转到controller
            response1.sendRedirect("/401");
        } catch (IOException e) {
            e.printStackTrace();
            System.out.println("JWTFilter.response401:"+e.getMessage());
        }
    }
    /**获取请求头的信息，拿到浏览器发给我们的 token 请求头中有Authorization，我们才继续没有，抛异常
     * 没有的话 就应该跳转到登录页面
    */
    @Override
    protected boolean isLoginAttempt(ServletRequest request, ServletResponse response) {
        HttpServletRequest httpServletRequest = (HttpServletRequest) request;
        String authorization = httpServletRequest.getHeader("Authorization");
        return authorization!=null;
    }
    /**
     * 最后的subject.login 执行shiro的登录认证流程
     * @param request
     * @param response
     * @return
     * @throws Exception
     */
    @Override
    protected boolean executeLogin(ServletRequest request, ServletResponse response) throws Exception {
        HttpServletRequest httpServletRequest = (HttpServletRequest) request;
//        从请求头中获取token
        String token = httpServletRequest.getHeader("Authorization");
        MyJsonWebToken myJsonWebToken = new MyJsonWebToken(token);
        //真正执行我们的登录方法，要进入到我们的自定义域的Authentication认证方法中
        getSubject(request,response).login(myJsonWebToken);
        return true;
    }
}
```

### 第二步 自定义域Realm

为什么要写自定义域？因为原本的shiro的域，不支持jwt的认证。它只能用于账号密码，UsernamePasswordToken的认证。

subject.login()方法调用后，会进入到自定义域中的认证方法。

```java
package com.woniuxy.shiro.utils;
import com.woniuxy.entity.RbacManager;
import com.woniuxy.service.RbacManagerService;
import com.woniuxy.utils.JWTUtil;
import org.apache.shiro.authc.AuthenticationException;
import org.apache.shiro.authc.AuthenticationInfo;
import org.apache.shiro.authc.AuthenticationToken;
import org.apache.shiro.authc.SimpleAuthenticationInfo;
import org.apache.shiro.authz.AuthorizationInfo;
import org.apache.shiro.realm.AuthorizingRealm;
import org.apache.shiro.subject.PrincipalCollection;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;
@Component//注册到spring容器中
public class MyRealmToken extends AuthorizingRealm {
    @Autowired
    RbacManagerService rbacManagerService;
    /**
     * shiro底层有过一个supports的方法，那个方法有问题，
     * 无法把我们自己写的MyJsonWebToken当做AuthenticationToken的实现类
     * MyJsonWebToken instenceof AuthenticationToken ===>false 所以我们要重写supports方法
     * @param token
     * @return
     */
    @Override
    public boolean supports(AuthenticationToken token) {
        return token instanceof MyJsonWebToken;
    }
    /**
     * 授权
     * @param principals
     * @return
     */
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principals) {
        return null;
    }
    /**
     * 认证：我们第一次登录不经过域
     * 第二次通过token登录认证的时候，先进入过滤器，然后我们才进入 自定义域 认证方法中
     * @param authenticationToken MyJsonWebToken  里面包含了token
     * @return
     * @throws AuthenticationException
     */
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken authenticationToken) throws AuthenticationException {
        MyJsonWebToken myJsonWebToken = (MyJsonWebToken) authenticationToken;
        //通过校长获取 token
        String token = myJsonWebToken.getPrincipal()+"";
        //jwt工具类的方法，通过token获取用户名
        String userName = JWTUtil.getUserName(token);
        //判断userName是否是空的 如果有人修改过token
        if(userName==null){
            throw new AuthenticationException("token 无效");
        }
        //换成通过redis进行token认证，传入token，查出数据库的key，然后返回完整对象
        RbacManager rbacManager = rbacManagerService.findRbacManagerByTokenFromRedis(token);
        if (rbacManager != null){
            return new SimpleAuthenticationInfo(token, token, "MyRealmToken");
        }else{
            throw new AuthenticationException("账号或密码不匹配，token被篡改");
        }
    }
}
```

### 第三步 shiro配置文件

为什么要写配置文件？

必须让shiro的SecurityManager使用我的自定义域，同时，配置过滤器加入shiro的过滤器链中。

类似 之前写过 shiro-realm.ini配置文件中做的事情！

![image-20211022151350130](../images/202110221513194.png)

我们的配置文件：

```java
package com.woniuxy.config;
import com.woniuxy.shiro.utils.MyRealmToken;
import com.woniuxy.utils.JWTFilter;
import org.apache.shiro.spring.LifecycleBeanPostProcessor;
import org.apache.shiro.spring.security.interceptor.AuthorizationAttributeSourceAdvisor;
import org.apache.shiro.spring.web.ShiroFilterFactoryBean;
import org.apache.shiro.web.mgt.DefaultWebSecurityManager;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import javax.servlet.Filter;
import java.util.HashMap;
import java.util.Map;
@Configuration//表示当前类是 配置类
public class ShiroConfig {
    /**
     * 配置安全管理器 SecurityManager 注入我们自带的域 realm 可以参考之前的shiro-realm.ini配置文件
     * 参数，会自动通过spring容器注入DI，在SecurityManager中注入我们自己的realm
     */
    @Bean
    public DefaultWebSecurityManager securityManager(MyRealmToken myRealmToken){
        DefaultWebSecurityManager defaultWebSecurityManager = new DefaultWebSecurityManager();
        defaultWebSecurityManager.setRealm(myRealmToken);
        return defaultWebSecurityManager;
    }
    /**
     * Filter工厂，我们写了JWTFilter，但是对于shiro来说，shiro有自己的一套过滤器
     * 我们需要把我们的自定义过滤器，添加到shiro的过滤器链中 ==>Springsecurity类似
     */
    @Bean
    public ShiroFilterFactoryBean shiroFilterFactoryBean(DefaultWebSecurityManager securityManager){
        ShiroFilterFactoryBean shiroFilterFactoryBean = new ShiroFilterFactoryBean();
        //之前已经把自定义的域 放入了SecurityManager中，现在又把SecurityManager放入过滤器工厂中
        shiroFilterFactoryBean.setSecurityManager(securityManager);
        Map<String, Filter> filterMap = new HashMap<>();
        //放入我们的自定义过滤器
        filterMap.put("JWT",new JWTFilter());
        //放入我们自定义的过滤器 命名为JWT
        shiroFilterFactoryBean.setFilters(filterMap);
        //url访问过滤配置
        Map<String, String> urlMap = new HashMap<>();
        urlMap.put("/**","JWT");//所有请求都需要经过JWT过滤器
        urlMap.put("/login","anon");//允许匿名访问
        urlMap.put("/401","anon");
        //使用我们自定义过滤器的情况，是在/**表示所有页面都必须先经过过滤器的方法进行token认证
        shiroFilterFactoryBean.setFilterChainDefinitionMap(urlMap);
        return shiroFilterFactoryBean;
    }
    //开启shiro注解支持
    @Bean
    public AuthorizationAttributeSourceAdvisor
    authorizationAttributeSourceAdvisor(DefaultWebSecurityManager securityManager) {
        AuthorizationAttributeSourceAdvisor advisor = new
                AuthorizationAttributeSourceAdvisor();
        advisor.setSecurityManager(securityManager);
        return advisor;
    }
    /**
     * Description : 管理Subject主体对象,生命周期的组件,用户只是打印下生产销毁日志什么的,
     * 请参考spring中bean的生命周期   <br/>
     * ChangeLog : 1. 创建 (2021/4/27 0:25 [mayuhang]);
     *
     * @return org.apache.shiro.spring.LifecycleBeanPostProcessor
     **/
    @Bean
    public LifecycleBeanPostProcessor lifecycleBeanPostProcessor() {
        return new LifecycleBeanPostProcessor();
    }
}
```

## 四、授权

通过方法上添加注解，来触发进入授权方法中。

```java
@RequestMapping("/showall")
@RequiresRoles({"超级管理员"})
@RequiresPermissions({"menu::find","menu::delete"})
public List<Menu> findAll(){
    return leftMenuTreeService.findAll();
}
```

RequiresRoles 参数需要的是数组类型，表示需要某些角色才能进入方法。(此处的角色必须和授权自定义域中的方法，放入的名称相同，如果你放入的是id，则这里也用id，如果放入的是中文名称，则这里也用中文名称)

RequiresPermissions 同上，判断是否拥有这个权限。

自定义域中的授权方法：

```java
package com.woniuxy.shiro.utils;
import com.woniuxy.entity.RbacManager;
import com.woniuxy.service.RbacManagerService;
import com.woniuxy.utils.JWTUtil;
import org.apache.shiro.authc.AuthenticationException;
import org.apache.shiro.authc.AuthenticationInfo;
import org.apache.shiro.authc.AuthenticationToken;
import org.apache.shiro.authc.SimpleAuthenticationInfo;
import org.apache.shiro.authz.AuthorizationInfo;
import org.apache.shiro.authz.SimpleAuthorizationInfo;
import org.apache.shiro.realm.AuthorizingRealm;
import org.apache.shiro.subject.PrincipalCollection;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;
import java.util.Set;
@Component//注册到spring容器中
public class MyRealmToken extends AuthorizingRealm {
    @Autowired
    RbacManagerService rbacManagerService;
    /**
     * shiro底层有过一个supports的方法，那个方法有问题，
     * 无法把我们自己写的MyJsonWebToken当做AuthenticationToken的实现类
     * MyJsonWebToken instenceof AuthenticationToken ===>false 所以我们要重写supports方法
     * @param token
     * @return
     */
    @Override
    public boolean supports(AuthenticationToken token) {
        return token instanceof MyJsonWebToken;
    }
    /**
     * 授权
     * @param principals
     * @return
     */
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principals) {
//        System.out.println("您已进入权限判断，但是我啥都没写！");
        //当前有用户的权限进行判断
        String token = (String) principals.getPrimaryPrincipal();
        RbacManager rbacManager = rbacManagerService.findRbacManagerByTokenFromRedis(token);
        //可以通过rbacmanager::admin 当做key  角色权限数据都存redis中，如果redis中没有，就去数据库查询
        代码略……自己写
        //通过这个对象，去数据库查出它所拥有的权限和角色 全部放入SET集合中
        int id = rbacManager.getId();
        Set<String> roles = rbacManagerService.findAllRolesById(id);
        SimpleAuthorizationInfo simpleAuthorizationInfo = new SimpleAuthorizationInfo();
        simpleAuthorizationInfo.setRoles(roles);
        //权限
        return simpleAuthorizationInfo;
    }
    /**
     * 认证：我们第一次登录不经过域
     * 第二次通过token登录认证的时候，先进入过滤器，然后我们才进入 自定义域 认证方法中
     * @param authenticationToken MyJsonWebToken  里面包含了token
     * @return
     * @throws AuthenticationException
     */
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken authenticationToken) throws AuthenticationException {
        MyJsonWebToken myJsonWebToken = (MyJsonWebToken) authenticationToken;
        //通过校长获取 token
        String token = myJsonWebToken.getPrincipal()+"";
        //jwt工具类的方法，通过token获取用户名
        String userName = JWTUtil.getUserName(token);
        //判断userName是否是空的 如果有人修改过token
        if(userName==null){
            throw new AuthenticationException("token 无效");
        }
        //换成通过redis进行token认证，传入token，查出数据库的key，然后返回完整对象
        RbacManager rbacManager = rbacManagerService.findRbacManagerByTokenFromRedis(token);
        if (rbacManager != null){
            return new SimpleAuthenticationInfo(token, token, "MyRealmToken");
        }else{
            throw new AuthenticationException("账号或密码不匹配，token被篡改");
        }
    }
}
```

# redis持久化机制

> 面试最容易问的点

持久化？保存在磁盘，硬盘上的数据，就是持久化的数据。

在内存中的数据，是临时数据，软件关闭，关机，都会丢失。

Redis的所有数据，在运行期间都是保存在内存中，然后通过不定期的`异步方案(再开一个线程来执行持久化方案)`保存到磁盘上，这种操作方案称之为`半持久化模式`；另外一种方案把 每次数据变化都写入到AOF(append only file)的文件中，而这种操作，我们都称之为`全持久化模式`

RDB：在指定的**时间间隔**中，检测key的变化情况，然后对你的数据进行快照存储。（默认开启）

AOF：日志记录的方式，可以记录每次对服务器写的操作,当服务器重启的时候会重新执行这些命令来恢复原始的数据。（默认关闭）

## 5.1 二者的区别（面试）

RDB持久化是指在指定的`时间间隔`内将内存中的数据集快照写入磁盘，实际操作过程是fork一个子进程，先将数据集写入临时文件，写入成功后，再**替换之前的文件**，用二进制压缩存储。

![img](../images/wps17.jpg)

AOF持久化以日志的形式记录服务器所处理的每一个写、删除操作，查询操作不会记录，以文本的方式记录(人类可读)，可以打开文件看到详细的操作记录。

![img](../images/wps18.jpg)

## 5.2 RDB快照模式（默认）

![img](../images/wps19.jpg)

默认情况下，是`快照RDB`的持久化方式，将内存中的数据以快照的方式写入二进制文件中，默认的文件名是dump.rdb

```java
In the example below the behaviour will be to save:  after 900 sec (15 min) if at least 1 key changed  after 300 sec (5 min) if at least 10 keys changed  after 60 sec if at least 10000 keys changed# 每900s检查一次数据，如果至少有1条发生改变的时候，进行持久化save 900 1 # 每300s检查一次数据，如果至少有10条发生改变的时候，进行持久化save 300 10# 每60s检查一次数据，如果至少有10000条发生改变的时候，进行持久化save 60 10000
```

注：需要修改的地方

![img](../images/wps21.jpg)

![img](../images/wps22.jpg)

linux设置protected-mode no 外部网络可以直接访问

![img](../images/wps23.jpg)

修改 登录用的auth 密码

![img](../images/wps24.jpg)

linux中的玩法：redis-server /usr/local/etc/redis/redis.conf 使用上传的配置文件进行开启redis 服务

连接时，需要输入auth 密码进行身份确认

![img](../images/wps25.jpg)

查看文件的变化：

![img](../images/wps26.jpg)

## 5.3 AOF 文件追加模式（手动）

![img](../images/wps27.jpg)

![img](../images/wps28.jpg)

启动服务（指定配置文件）：

![image-20210131140749626](../images/image-20210131140749626.png)

多出一个aof的持久化文件 ![image-20210131140830215](../images/image-20210131140830215.png)

![image-20211022173403194](../images/202110221734307.png)

## 5.4 AOF的重写配置（了解）

AOF是redis的一种持久化方式，用来记录所有的写操作，但是随着时间增加，aof文件会越来越大，所以需要进行重写，将内存中的数据重新以命令的方式写入aof文件。

在重写的过程中，由于redis还会有新的写入，为了避免数据丢失，会开辟一块内存用于存放重写期间产生的写入操作，等到重写完毕后会将这块内存中的操作再追加到aof文件中。

![img](../images/wps32.jpg)

\# aof重写期间是否同步

no-appendfsync-on-rewrite no

\# 重写触发配置

auto-aof-rewrite-percentage 100

auto-aof-rewrite-min-size 64mb

\# 加载aof时如果有错如何处理

aof-load-truncated yes

\# 文件重写策略

aof-rewrite-incremental-fsync yes

**no-appendfsync-on-rewrite**

 是否在后台写时同步单写，默认值no(表示需要同步).这里的后台写，表示后台正在重写文件(主要是aof重写文件了).no表示新的主进程的set操作会被阻塞掉，待整个后台写完成之后再将这部分set操作同步到aof文件中，但这可能会存在数据丢失的风险(机率很小)，而yes表示新的主进程的set不会被阻塞。如果对性能有要求，可以设置为yes，仅在后台写时会异步处理命令，保证数据安全。

**auto-aof-rewrite-percentage**

 是设置aof rewrite触发时机的一个参数，当当前的aof文件大小超过上一次rewrite后aof文件的百分比后触发rewrite；例如将100改为800 ，即当前的aof文件超过上一次重写后aof文件的8倍时才会再次rewrite，这样可以保证短期内不会再次进行重写操作。

**auto-aof-rewrite-min-size**

 aof文件重写最小的文件大小，即最开始aof文件必须要达到这个文件时才触发，后面的每次重写就不会根据这个变量了(根据上一次重写完成之后的大小).此变量仅初始化启动redis有效.如果是redis恢复时，则lastSize等于初始aof文件大小.

**aof-load-truncated**

 如果该配置启用，在加载时发现aof文件不正确时，会向客户端写入一个log，但是会继续执行，如果设置为 no ，发现错误就会停止，必须修复后才能重新加载。

**aof-rewrite-incremental-fsync**

 aof rewrite过程中,是否采取增量文件同步策略,默认为“yes”。rewrite过程中,每32M数据进行一次文件同步,这样可以减少aof大文件写入对磁盘的操作次数

## 5.5 二者的优缺点

**RDB存在哪些优势**

一旦采用该方式，那么你的整个Redis数据库将只包含一个文件，这对于文件备份而言是非常完美的。比如，你可能打算每个小时归档一次最近24小时的数据，同时还要每天归档一次最近30天的数据。通过这样的备份策略，一旦系统出现灾难性故障，我们可以非常容易的进行恢复。

对于灾难恢复而言，RDB是非常不错的选择。因为我们可以非常轻松的将一个单独的文件压缩后再转移到其它存储介质上。

性能最大化。对于Redis的服务进程而言，在开始持久化时，它唯一需要做的只是fork出子进程，之后再由子进程完成这些持久化的工作，这样就可以极大的避免服务进程执行IO操作了。

 相比于AOF机制，如果数据集很大，RDB的启动效率会更高。

**RDB又存在哪些劣势**

如果你想保证数据的高可用性，即最大限度的避免数据丢失，那么RDB将不是一个很好的选择。因为系统一旦在定时持久化之前出现宕机现象，此前没有来得及写入磁盘的数据都将丢失。

由于RDB是通过fork子进程来协助完成数据持久化工作的，因此，如果当数据集较大时，可能会导致整个服务器停止服务几百毫秒，甚至是1秒钟。

**AOF的优势**

该机制可以带来更高的数据安全性，即数据持久性。Redis中提供了3种同步策略，即每秒同步、每修改同步和不同步。事实上，每秒同步也是异步完成的，其效率也是非常高的，所差的是一旦系统出现宕机现象，那么这一秒钟之内修改的数据将会丢失。而每修改同步，我们可以将其视为同步持久化，即每次发生的数据变 化都会被立即记录到磁盘中。可以预见，这种方式在效率上是最低的。至于无同步，无需多言，我想大家都能正确的理解它。

由于该机制对日志文件的写入操作采用的是append模式，因此在写入过程中即使出现宕机现象，也不会破坏日志文件中已经存在的内容。然而如果我们本次操作只是写入了一半数据就出现了系统崩溃问题，不用担心，在Redis下一次启动之前，我们可以通过redis-check-aof（./redis-check-aof —fix appendonly.aof）工具来帮助我们解决数据一致性的问题。

如果日志过大，Redis可以自动启用rewrite机制。即Redis以append模式不断的将修改数据写入到老的磁盘文件中，同时Redis还会创建一个新的文件用于记录此期间有哪些修改命令被执行。因此在进行rewrite切换时可以更好的保证数据安全性。

AOF包含一个格式清晰、易于理解的日志文件用于记录所有的修改操作。事实上，我们也可以通过该文件完成数据的重建。

**AOF的劣势**

对于相同数量的数据集而言，AOF文件通常要大于RDB文件。RDB 在恢复大数据集时的速度比 AOF 的恢复速度要快。

根据同步策略的不同，AOF在运行效率上往往会慢于RDB。总之，每秒同步策略的效率是比较高的，同步禁用策略的效率和RDB一样高效。

二者选择的标准，就是看系统是愿意牺牲一些性能，换取更高的缓存一致性（aof），还是愿意写操作频繁的时候，不启用备份来换取更高的性能，待手动运行save的时候，再做备份（rdb）。rdb这个就更有些 最终一致 的意思。

# 手写jedis

为了干嘛呢?? 肯定是为了能看懂某个东西, 也就是我们持久化的内容!

## 1 服务端

```java
package com.woniuxy.jedis;
import java.io.IOException;
import java.io.InputStream;
import java.net.ServerSocket;
import java.net.Socket;
/** 
 * @author: mayuhang  <br/>
 * Date: 2021/5/7:23:37  <br/>
 * Description:手写jedis
 */
public class MyJedisServer {
    public static void main(String[] args) throws IOException {
        ServerSocket serverSocket = new ServerSocket(6378);
        Socket accept = serverSocket.accept();
        byte[] bytes = new byte[1024];
        InputStream inputStream = accept.getInputStream();
        inputStream.read(bytes);
        System.out.println(new String(bytes));
        accept.close();
        serverSocket.close();
    }
}
```

## 2 客户端

```java
package com.woniuxy.jedis;
import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStream;
import java.io.UnsupportedEncodingException;
import java.net.Socket;
public class MyJedisClint {
    Socket socket;
    InputStream reader;
    OutputStream writer;
    public MyJedisClint() {
        try {
            this.socket =new Socket("127.0.0.1",6379);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    public MyJedisClint(String host, int port) {
        try {
            this.socket =new Socket(host,port);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    public void set(String key, String value) throws IOException {
        StringBuffer stringBuffer = new StringBuffer();
        stringBuffer.append("*3").append("\r\n");
        stringBuffer.append("$3").append("\r\n");
        stringBuffer.append("SET").append("\r\n");
        stringBuffer.append("$").append(key.length()).append("\r\n");
        stringBuffer.append(key).append("\r\n");
        stringBuffer.append("$").append(value.length()).append("\r\n");
        stringBuffer.append(value).append("\r\n");
        byte[] bytes1 = stringBuffer.toString().getBytes();
        new String(bytes1,"UTF-8");
        writer = socket.getOutputStream();
        writer.write(bytes1);
        socket.close();
    }
    public String get(String key) throws IOException {
        StringBuffer stringBuffer = new StringBuffer();
        stringBuffer.append("*2").append("\r\n");
        stringBuffer.append("$3").append("\r\n");
        stringBuffer.append("GET").append("\r\n");
        stringBuffer.append("$").append(key.length()).append("\r\n");
        stringBuffer.append(key).append("\r\n");
        byte[] bytes1 = stringBuffer.toString().getBytes();
//        new String(bytes1,"UTF-8");
        //通过输出流  告诉redis 我的口令
        writer = socket.getOutputStream();
        //输出到redis的口令内容
        writer.write(bytes1);
        //停掉输出流
        socket.shutdownOutput();
        //获取输入流  接收 redis返回的数据
        reader = socket.getInputStream();
        //放入字符数组中
        byte[] bytes = new byte[2048];
        reader.read(bytes);
        socket.close();
        //返回给我myjedis调用方法中
        return new String(bytes);
    }
    public void sadd(String key,String ... members) throws IOException {
        int length = members.length;
        int lastLength=2+length;
        StringBuffer stringBuffer = new StringBuffer();
        stringBuffer.append("*").append(lastLength).append("\r\n");
        stringBuffer.append("$4").append("\r\n");
        stringBuffer.append("SADD").append("\r\n");
        stringBuffer.append("$").append(key.length()).append("\r\n");
        stringBuffer.append(key).append("\r\n");
        for (String x: members
             ) {
            stringBuffer.append("$").append(x.getBytes().length).append("\r\n");
            stringBuffer.append(x).append("\r\n");
        }
        System.out.println(stringBuffer);
        byte[] bytes1 = stringBuffer.toString().getBytes("ISO-8859-1");
        writer = socket.getOutputStream();
        writer.write(bytes1);
        socket.close();
    }
}
```

## 3测试类

```java
package com.woniuxy.jedis;
import org.junit.Test;
import redis.clients.jedis.Jedis;
import java.io.IOException;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
public class JedisText {
    public static void main(String[] args) throws IOException {
//        Jedis jedis = new Jedis("localhost", 6378);
//        //字符串
//        jedis.set("myjedis","sysou");
//        System.out.println(jedis.get("myjedis"));
        //list
//        jedis.lpush("mylist1","mystring1","mystring2");
//        List<String> mylist1 = jedis.lrange("mylist1", 0, -1);
//        String mylist11 = jedis.rpop("mylist1");
//        System.out.println(mylist11);
//        mylist1.forEach(System.out::println);
        //hash
//        Map<String,String> objectObjectHashMap = new HashMap<String,String>();
//        objectObjectHashMap.put("mykey1","myvalue1");
//        objectObjectHashMap.put("mykey2","myvalue2");
//        objectObjectHashMap.put("mykey3","myvalue3");
//        jedis.hset("myhash1",objectObjectHashMap);
//        Map<String, String> stringStringMap = jedis.hgetAll("myhash1");
//        System.out.println(stringStringMap);
//        com.woniuxy.jedis.MyJedisClint jedis = new com.woniuxy.jedis.MyJedisClint("192.168.40.178",6379);
//        jedis.set("mystring","mys");
    }
    @Test
    public void getTest() throws IOException {
        com.woniuxy.jedis.MyJedisClint jedis = new com.woniuxy.jedis.MyJedisClint("localhost",6379);
        jedis.set("mystring","mys");
//        String mystring = jedis.get("mystring");
//        System.out.println(mystring);
    }
    @Test
    public void getJedisTest() throws IOException {
        com.woniuxy.jedis.MyJedisClint jedis = new com.woniuxy.jedis.MyJedisClint("localhost",6379);
//        Jedis jedis = new Jedis("localhost", 6378);
        jedis.sadd("mysadd","中国","我是","你不是");
    }
}
```





![image-20211022171258882](../images/image-20211022171258882.png)

![image-20211022173222184](../images/image-20211022173222184.png)



> **持久层包命名：**
>
> DAO：表示持久层可以是原生JDBC，MyBatis，Hibrate等，也可以是这些混合使用，没有指明持久层使用框架的倾向，是一种通用的命名。
>
> Mapper：一般表示持久层就是使用了MyBatis框架。
>

**sping boot @PathVariable注解**

> @Autowired(required=true)：当使用@Autowired注解的时候，其实默认就是@Autowired(required=true)，表示注入的时候，该bean必须存在，否则就会注入失败。
>
> @Autowired(required=false)：表示忽略当前要注入的bean，如果有直接注入，没有跳过，不会报错。

![image-20211023172535847](../images/image-20211023172535847.png)

![image-20211023173002155](../images/image-20211023173002155.png)

![image-20211023173114246](../images/image-20211023173114246.png)

![image-20211023173258631](../images/image-20211023173258631.png)
