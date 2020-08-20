---
title: 微信公众号开发教程
date: 2020-07-27 19:02:20
categories: 
- Java
- Java Web
tags: wechat
---



## 介绍微信公众平台

**微信公众平台**是微信团队为**微信公众号**开发者提供的一个基础平台，平台通过为公众号开发者提供一系列的接口，让公众号能够更好的为微信用户提供服务。

微信公众号是微信 APP 中的一个功能，它是公众号运营者为微信用户提供资讯和服务的接入点，微信用户通过订阅公众号和进入公众号，可以看到公众号提供的服务菜单和推送消息。

## 介绍微信公众号开发

**如何开发一个公众号**

- 开发者需要在微信公众平台注册一个公众号开发者账号。公众号分为服务号和订阅号，不同类型的公众号具备不同的公众平台的接口权限。
- 在公众平台配置自己的域名和服务器，开发者的服务器与微信公众平台进行对接。
- 开发者在微信公众平台提供的接口的基础上开发自己的服务，服务分为两种：公众号消息会话和公众号内网页。

**公众号用户的识别**

微信用户在公众号内的请求会携带一个 OpenID 的参数，来标识一个用户。每个用户对每个公众号都会产生一个唯一的 OpenID。如果需要在多个公众号之间做用户共通，需要将这些公众号和应用绑定到一个开发平台账号下，绑定后，一个用户对多个公众号依然有多个不同的 OpenID，但一个用户只有一个唯一的 UnionID。

**公众号开发注意事项**

- 微信公众平台开发是指微信公众号进行业务开发，为移动应用、PC端网站、公众号第三方平台的开发，请前往微信开发平台接入。
- 在申请到认证公众号之前，可以先通过测试号申请系统，快速申请一个接口测试号，立即开始接口测试开发。
- 在开发过程中，可以使用接口调试工具来在线调试某些接口。
- 每个接口都有每日接口调用频次限制，可以在公众平台官网-开发者中心处查看具体频次。
- 在开发出现问题时，可以通过接口调用的返回码，以及报警排查指引（在微信公众平台官网-开发者中心处可以设置接口报警），开发发现和解决问题。
- 公众平台以 access_token 为接口调用凭据，来调用接口，所有接口的调用需要先获取 access_token，access_token 在2小时内有效，过期需要重新获取，但1天内获取次数有限，开发者需自行存储。
- 公众平台接口调用仅支持80端口。

**公众号服务介绍**

公众号消息会话

1. 群发消息：公众号可以以一定频次（订阅号为每天1次，服务号为每月4次），向用户群发消息，包括文字消息、图文消息、图片、视频和语音等。
2. 被动回复消息：在用户给公众号发消息后，公众号可以自动回复一个消息。
3. 客服消息：在用户给公众号发消息后的48小时内，公众号可以给用户发送不限数量的消息，主要用于客服场景。
4. 模板消息：用于给用户发送服务通知，如刷卡提醒，服务预约成功通知等。公众号可以用特定内容模板主动向用户发送。

公众号内网页

许多复杂的业务场景，需要通过网页形式来提供服务，如商城，水电缴费等。公众号网页可以通过点击公众号发送的消息，或者点击公众号菜单进入网页。网页需要用到的公众平台的功能：网页授权获取用户基本信息，微信JS-SDK。



## 微信公众号开发接入

微信公众号接入是指：部署自己的公众号应用程序到自己的服务器上，然后在微信公众平台配置自己的服务器信息，进行 token 验证，token 验证成功，说明微信公众平台能够正常地与你的服务器进行通信。

微信公众号开发接入过程如下：

1. 编写公众号应用程序（含验证 token 的接口），服务器启动程序。
2. 在微信公众平台网站填写服务器配置。在微信公众平台的“开发-基本配置-服务器配置-修改配置”页面，填写服务器的信息。
3. 验证 token。点击“基本配置页面”下方的提交按钮，进行 token 验证。若当前页面出现“token验证失败”的提示，则说明配置的信息有误，或者你服务器的代码有误；若token验证成功，则会自动返回基本配置的主页面。
4. 启用配置，准备开发。token 验证成功后，点击“启用”按钮，启用配置的服务器。然后使用微信公众平台提供的接口实现自己业务逻辑。

公众号接入成功后，可以进行微信公众平台 API 的调用测试。主要过程为：编写程序，调用程序，查看程序返回结果。

## 公众号开发基础

**公众平台接口域名**

微信公众平台有多个域名，开发者可以根据自己的服务器部署情况，选择最佳的接入点（延时更低，稳定性更高）。除此之外，开发者可以将其他接入点用作容灾用途，当网络链路发生故障时，可以考虑选择备用接入点来接入。微信公众平台的域名如下：

1. 通用域名(api.weixin.qq.com)，使用该域名将访问官方指定就近的接入点；
2. 通用异地容灾域名(api2.weixin.qq.com)，当上述域名不可访问时可改访问此域名；
3. 上海域名(sh.api.weixin.qq.com)，使用该域名将访问上海的接入点；
4. 深圳域名(sz.api.weixin.qq.com)，使用该域名将访问深圳的接入点；
5. 香港域名(hk.api.weixin.qq.com)，使用该域名将访问香港的接入点。

**获取 Access token**

调用微信公众平台接口之前，必须获取 access_token，access_token 时公众号的全局唯一调用凭据，公众号调用公众平台的接口时都需要使用 access_token 作为参数。

access_token 的存储至少需要保留 512 个字符空间，它的有效期为 2 个小时，需定时刷新，重复获取将导致上次获取的 access_token 失效。

access_token 使用说明：

- 建议公众号开发者使用中控服务器统一获取和刷新access_token，其他业务逻辑服务器所使用的access_token均来自于该中控服务器，不应该各自去刷新，否则容易造成冲突，导致access_token覆盖而影响业务；
- 目前access_token的有效期通过返回的expire_in来传达，目前是7200秒之内的值。中控服务器需要根据这个有效时间提前去刷新新access_token。在刷新过程中，中控服务器可对外继续输出的老access_token，此时公众平台后台会保证在5分钟内，新老access_token都可用，这保证了第三方业务的平滑过渡；

接口调用请求：

```
https请求方式: GET 
URL: https://api.weixin.qq.com/cgi-bin/token?grant_type=client_credential&appid=APPID&secret=APPSECRET
```

返回说明：

成功返回信息如下

```
{"access_token":"ACCESS_TOKEN","expires_in":7200}
```

**IP 白名单**

在 IP 白名单内的IP来源，获取 access_token 接口才可调用成功。

**接口测试号申请**

微信公众平台的某些高级接口的调用权限需要微信认证后才可以获取。为了帮助开发者快速了解和上手微信公众号开发，熟悉各个接口的调用，可以申请微信公众帐号测试号。

微信公众号测试号和你的正式的公众号不是同一个公众号，它们有不同的配置信息，如 appID, appsecret。微信公众号测试号相当于一个虚拟的公众号，可以让你调用很多接口，当你通过测试号实现了你的业务，最后，可以将你的配置参数改为正式的公众号。

测试号不需要设置 IP 白名单，使用测试号的配置信息去调用微信公众平台的接口是没有 IP 限制的。

链接地址：[微信公众号接口测试帐号申请](http://mp.weixin.qq.com/debug/cgi-bin/sandbox?t=sandbox/login&token=641658112&lang=zh_CN)

**微信公众平台接口在线调试工具**

微信公众平台接口在线调试工具，是为了帮助开发者检测调用公众平台 API 时发送的请求参数是否正确。在“在线调试工具”的页面提交相关信息后，提交请求，可获得公众平台服务器的返回结果。

在线调试工具的主要功能是帮你构造参数，其实其它任何地方也可以调用微信公众平台的接口。

链接地址：[微信公众平台接口调试工具](https://mp.weixin.qq.com/debug/)

**Web开发者工具**

微信公众平台为开发者提供“web开发者工具”，用于帮助开发基于微信的网页或者webapp。它是一个桌面应用，通过模拟微信客户端的表现使得开发者可以使用这个工具方便地在 PC 上进行开发和调试。

下载地址：[web开发者工具](https://developers.weixin.qq.com/doc/offiaccount/OA_Web_Apps/Web_Developer_Tools.html#5)

**微信公众平台的接口调用和微信公众号内的网页请求**

微信公众号的网页，需要在微信公众号内或者在微信web开发者工具中打开。

微信公众平台的接口调用可以在任何支持 HTTP 请求的地方请求并得到返回结果。但“获取access_token 接口”的请求的客户端 IP 需要 IP 白名单中，才能成功返回。然而，调用其它公众平台接口需要 access_token 作为参数，近似相当于没有在 IP 白名单内的客户端请求无法调用微信公众平台接口。不在 IP白名单的客户端的请求结果如下：

```
https://api.weixin.qq.com/cgi-bin/token?grant_type=client_credential&appid=xxx&secret=xxx
```

```
{"errcode":40164,"errmsg":"invalid ip 58.213.199.30 ipv6 ::ffff:58.213.199.30, not in whitelist hint: [Zhnd8cQNe-4P2DPA]"}
```

### 微信公众平台的接口和功能

微信公众平台提供的接口和功能如下：

- 自定义菜单
- 消息管理
- 微信网页开发
- 素材管理
- 图文消息留言管理
- 用户管理
- 帐号管理
- 数据统计
- 微信卡券
- 微信门店
- 智能接口
- 微信设备功能
- 新版客服功能
- 对话能力（原导购助手）
- 微信“一物一码”
- 微信发票

我们可以简单地将微信公众平台的API接口分为三类：

- 基础接口（菜单，用户，素材等），为消息服务和网页服务提供基础。
- 消息服务接口。
- 网页服务接口。

以上功能的具体的使用步骤和接口调用说明，请参考 [微信公众号开发指南](https://developers.weixin.qq.com/doc/offiaccount/Getting_Started/Overview.html)。

## 微信网页服务开发

### 网页授权

网页授权是指：在用户访问公众号网页时获取该微信用户的信息需要先跳转到用户授权页面进行授权。

微信公众号应用需要通过微信用户的 openId 来标识用户，我们需要通过网页授权来获取用户信息，根据用户的 openId 知道是哪个用户访问了公众号网页，然后进行相应的业务处理。

网页授权的过程为：公众号页面重定向跳转到微信用户授权页面，微信回调你的服务器，你获得 code 参数，利用 code 参数获取 JSAPI access_token（与基础 API 的 access_token 不同），利用 access_token 去调用微信 API 获取用户基本信息。

网页授权的实现过程：

- 在后端定义一个 Filter，拦截所有需要进行网页授权的请求（特定的 URI 前缀），检查请求的 session 中是否存在 openId。如果 openId 不存在，则需要进行网页授权，将未授权的请求重定向到微信授权页面；如果 openId 存在，则不进行任何处理。
- 微信回调后，你获取到了 code 参数，然后利用 code 参数调用微信 API 获取 access_token 参数，然后利用 access_token 调用微信 API 获取用户基本信息。需要调用两次微信 API。
- 将微信用户基本保存到 session 中。

### JS-SDK使用说明

JS-SDK 功能的页面参考样式：https://www.weixinsxy.com/jssdk/

JS-SDK 接口主要的功能：监听分享事件，暂存音频和视频，调用微信APP的功能（如微信扫一扫、内置地图），微信支付等。

访问测试公众号的网页的条件：

- 需要在微信开发者工具或者微信APP中访问公众号网页。
- 需要扫码关注了测试公众号的用户才能访问该公众号网页。
- 微信公众平台中配置"JS接口安全域名"（公众号调用 JSSDK 的域名），需要备案的域名，而不能是 IP。
- 微信公众平台中配置“接口权限列表--网页授权获取用户基本信息--授权回调页面域名”（用户授权后回调公众号的域名），需要备案的域名才行。
- “JS接口安全域名”与“授权回调页面域名”，需要保持一致。
- （Note: 微信公众平台中配置“Token验证” 的 URL是用于“token 验证”（消息通信是否正常）和“微信公众号的消息服务开发”（消息服务通信的 URL 与 token 验证 的 URL 一致，但消息服务是 HTTP POST 请求），token 验证 URL 与公众号网页服务开发没有关系。消息服务的通信 URL 可以是 IP，可以与网页服务的两个域名不一致）

使用微信 JS-SDK 接口

调用微信 JS-SDK 的 API 接口需要开发者应用提供相关配置和签名参数给 JS-SDK 的配置接口。主要是4个参数：appId，timestamp，nonceStr 和 signature。

获取签名的实现过程：通过普通的 access_token 调用微信 API 获取 jsapi_ticket 参数，生成一个随机字符串（可使用UUID），根据签名算法，将需要4个参数转换为1个签名参数。

JS-SDK 的使用如下：

1）进行权限验证

```javascript
wx.config({
	debug: true, // 开启调试模式,调用的所有api的返回值会在客户端alert出来，若要查看传入的参数，可以在pc端打开，参数信息会通过log打出，仅在pc端时才会打印。
	appId: '${wxJsapiConfig.appId}', // 必填，公众号的唯一标识
	timestamp: '${wxJsapiConfig.timestamp}', // 必填，生成签名的时间戳
	nonceStr: '${wxJsapiConfig.nonceStr}', // 必填，生成签名的随机串
	signature: '${wxJsapiConfig.signature}',// 必填，签名
	jsApiList: [
		'updateAppMessageShareData',
		'updateTimelineShareData'
	] // 必填，需要使用的JS接口列表
});

```

2）权限验证成功后，会执行JS-SDK 的 wx.ready() 方法：

```javascript
wx.ready(function(){
	//自定义“分享给朋友”及“分享到QQ”按钮的分享内容
    // Auto running
    wx.updateAppMessageShareData({
        title: 'wxPage-index', // 分享标题
        desc: 'Test share to friend.', // 分享描述
        link: '${wxJsapiConfig.url}', // 分享链接，该链接域名或路径必须与当前页面对应的公众号JS安全域名一致
        imgUrl: '', // 分享图标
        success: function () {
            // 设置成功
            console.log("Set share to friend info successfully!");
        }
    })

    // 拍照或从手机相册中选图接口
    // onclick running
    document.querySelector('#chooseImage').onclick = function () {
        wx.chooseImage({
            count: 9, // 默认9
            sizeType: ['original', 'compressed'], // 可以指定是原图还是压缩图，默认二者都有
            sourceType: ['album', 'camera'], // 可以指定来源是相册还是相机，默认二者都有
            success: function (res) {
                localIds = res.localIds; // 返回选定照片的本地ID列表，localId可以作为img标签的src属性显示图片
                console.log("chooseImage -- localIds first: " + localIds[0]);
            }
        });
    };
});
```

3）权限验证失败后，会执行 JS-SDK 的 wx.error() 方法。

```javascript
wx.error(function (res) {
    alert(res.errMsg);
});
```



更多内容请参考官方说明：

- [JS-SDK 接口使用说明](https://developers.weixin.qq.com/doc/offiaccount/OA_Web_Apps/JS-SDK.html)

- [JS-SDK 接口列表](https://developers.weixin.qq.com/doc/offiaccount/OA_Web_Apps/JS-SDK.html#63)

## 微信公众号开发接入示例

下面以 Java 为例，基于 Spring Boot 框架进行示例展示。

**1.创建和配置项目**

创建一个 Maven 项目，项目名为 `wechat-official-accounts`。

```
mvn archetype:generate -DgroupId=com.taogen.example -DartifactId=wechat-official-accounts -DarchetypeArtifactId=maven-archetype-webapp -DinteractiveMode=false
```

配置 `pom.xml` 文件

```xml
<project ...>  
    ...
    
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        <maven.compiler.target>1.8</maven.compiler.target>
        <maven.compiler.source>1.8</maven.compiler.source>
        <!-- custom properties -->
        <project.java.version>1.8</project.java.version>
        <junit.version>4.12</junit.version>
        <log4j.version>2.8.2</log4j.version>
    </properties>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.6.RELEASE</version>
    </parent>

    <dependencies>
        <!-- start -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
            <exclusions>
                <exclusion>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-starter-logging</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <!-- log4j2 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-log4j2</artifactId>
        </dependency>
        <!-- spring web -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <!-- unit test -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <sourceDirectory>src/main/java</sourceDirectory>
        <testSourceDirectory>src/test/java</testSourceDirectory>
        <plugins>
            <!-- Package as an executable jar/war. $ mvn package spring-boot:repackage -->
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <executions>
                    <execution>
                        <goals>
                            <goal>repackage</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
            <!-- maven compile -->
            <plugin>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.5.1</version>
                <configuration>
                    <source>${project.java.version}</source>
                    <target>${project.java.version}</target>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```

配置 log4j2 日志

`src/main/resources/log4j2.xml`

```xml
<Configuration status="WARN">
    <Appenders>
        <Console name="Console" target="SYSTEM_OUT">
            <PatternLayout pattern="%d [%t] %-5level %logger{36} - %msg%n"/>
        </Console>
    </Appenders>
    <Loggers>
        <Logger name="com.taogen.example" level="debug" additivity="false">
            <AppenderRef ref="Console"/>
        </Logger>
        <Root level="error">
            <AppenderRef ref="Console"/>
        </Root>
    </Loggers>
</Configuration>
```

配置 Spring Boot 参数和业务参数 

`src/main/resources/application.yml`

```yaml
wechat:
  token: "my_token"
  appId: "..."
  appSecret: "..."
server:
  port: 80
```



**2.编写验证 token 的 Java 代码**

`src/main/java/com/taogen/example/App.java`

```java
@SpringBootApplication
@RestController
public class App {

    private static final Logger logger = LogManager.getLogger();
    @Value("${wechat.token}")
    private String token;
    @Value("${wechat.appId}")
    private String appId;
    @Value("${wechat.appSecret}")
    private String appSecret;

    public static void main(String[] args) {
        SpringApplication.run(App.class, args);
    }

    @GetMapping("/hello")
    public String hello() {
        logger.debug("Access /hello");
        return "hello " + new Date();
    }

    /**
     * Validate Token
     *
     * @param signature 微信加密签名，signature结合了开发者填写的token参数和请求中的timestamp参数、nonce参数。
     * @param timestamp 时间戳
     * @param nonce     随机数
     * @param echostr   随机字符串
     * @return 若确认此次GET请求来自微信服务器，请原样返回echostr参数内容
     */
    @GetMapping("/wx")
    public String validateToken(@RequestParam("signature") String signature,
                                @RequestParam("timestamp") String timestamp,
                                @RequestParam("nonce") String nonce,
                                @RequestParam("echostr") String echostr) {
        logger.debug("signature: {}, timestamp: {}, nonce: {}, echostr: {}",
                signature, timestamp, nonce, echostr);
        // 开发者通过检验signature对请求进行校验（下面有校验方式）。
        // 1）将token、timestamp、nonce三个参数进行字典序排序
        // 2）将三个参数字符串拼接成一个字符串进行sha1加密
        // 3）开发者获得加密后的字符串可与signature对比，标识该请求来源于微信
        String sortedParams = getSortedParams(timestamp, nonce);
        String encryptedParams = getEncryptedParams(sortedParams);
        if (encryptedParams.equals(signature)) {
            return echostr;
        } else {
            return "error";
        }
    }

    private String getSortedParams(String timestamp, String nonce) {
        List<String> params = new ArrayList<>();
        params.add(this.token);
        params.add(timestamp);
        params.add(nonce);
        Collections.sort(params, Comparator.naturalOrder());
        StringBuilder validateString = new StringBuilder();
        for (String param : params) {
            validateString.append(param);
        }
        return validateString.toString();
    }

    private String getEncryptedParams(String sortedParams) {
        MessageDigest crypt = null;
        try {
            crypt = MessageDigest.getInstance("SHA-1");
            crypt.reset();
            crypt.update(sortedParams.toString().getBytes("UTF-8"));
        } catch (NoSuchAlgorithmException | UnsupportedEncodingException e) {
            e.printStackTrace();
        }

        return new BigInteger(1, crypt.digest()).toString(16);
    }
}
```



**3.打包项目，部署到服务器，运行项目**

打包项目：

```
mvn package spring-boot:repackage
```

将项目打包文件上传到服务器，运行以下命令启动项目： 

```
java -jar <project_name>.jar
```



**4.在微信公众平台网站进行 token 验证**

- 访问[微信公众平台](https://mp.weixin.qq.com/)，注册帐号或扫码登录。

- 登录后，在左侧的菜单栏选择 “开发” - “基本配置”，点击进入基本配置页面。

- 进入基本配置页面，在“服务器配置”右侧点击修改配置。填写以下配置，主要是 URL 和 你项目中自定义的 token：

  ```
  URL: http://<your_server_ip>/wx
  Token: my_token （项目中自主定义的 token）
  EncodingAESKey: （默认）
  消息加密方式：（默认）
  ```

- 点击修改配置页面的“提交”按钮，若 token 验证成功会自动跳转到基本配置页面；若验证失败会在当前页面弹出错误提示。

## Summary

**微信基础 API 接口**

前提要求

- 客户端请求的 IP 需要在公众平台的 IP 白名单中。但测试公众号不需要。

不满足前提的错误结果

- 调用微信 API 接口返回的错误提示：

  ```
  {"errcode":40164,"errmsg":"invalid ip <your_ip> ipv6 ::ffff:<your_ip>, not in whitelist hint: [jhCDbOwFE-C.kTAa] rid: 5f2baef3-06a383d9-48b6ee02"}
  ```

功能列表

- 获取 access_token
- 自定义菜单 （创建、删除、查询等）
- 素材管理（临时素材、永久素材、图文素材的创建、删除和获取等）
- 图文消息留言管理
- 用户管理（用户标签、用户备注名、用户基本信息、用户地理位置等）
- 帐号管理
- 数据统计
- 微信卡券
- ...

如何对接

- 获取 access_token，调用微信 API。

**微信消息服务**

前提要求

- token 验证通过。

不满足前提的错误结果

- 无法收到微信服务器的请求

功能列表

- 自定义菜单 -事件推送
- 消息管理 （接收普通消息、接收事件推送、回复消息）
- 消息管理（群发消息、模板消息、一次性订阅消息）

如何对接

- 接收来自微信服务器的 token 验证请求，进行 token 验证。
- 接收来自微信服务器的消息，解析 XML 获取消息内容和用户信息，回复 XML 格式的消息。
- 获取 access_token，调用微信 API，群发消息给多个用户。

**微信网页服务**

前提要求

- 公众号的网页需要在微信开发者工具或者微信APP中访问。
- 公众号网页的 URL 的域名，JS 安全域名和用户授权回调域名需要一致，且域名已经备案。

不满足前提的错误结果

- 网页出现下面的提示：

  ```
  Oops! Something went wrong:(
  ```

功能列表

- 网页授权
- 网页开发样式 WeUI
- JS-SDK （基础接口，分享接口，图像接口，音频接口，智能接口，地理位置）
- JS-SDK（微信支付）
- 微信开放标签

如何对接

- 在网页获取微信用户信息。公众号页面重定向跳转到微信用户授权页面，微信回调你的服务器，获得 code 参数，利用 code 参数获取 JSAPI access_token（与基础 API 的 access_token 不同），利用 access_token 去调用微信 API 获取用户基本信息。
- 在网页调用微信 JS-SDK 的 API。调用微信 API 获取 jsapi_ticket，根据 jsapi_ticket 构造得到签名，将签名和相关参数返回给前端，前端利用签名和相关参数调用 JS-SDK API。

### 常见问题

**Question: 公众号页面重定到微信用户授权页面（获取 code ）时出现错误**

微信授权页面错误提示为：`Oops! Something went wrong:(`

Solution:

确保在微信开发者工具或者微信APP 中打开微信公众号的页面链接，而不是在浏览器打开。

检查配置。微信公众平台的接口权限列表中的“网页授权获取用户信息”行，修改“授权回调页面域名”（不需要加 http:// or https://）

**Question: JSAPI 参数错误，无效的签名。**

错误提示为：`{errMsg: "config:fail,Error: 系统错误，错误码：63002,invalid signature [20200804 16:04:22][]"}`

Solution：

检查需要的4个参数（noncestr, jsapi_ticket, timestamp, url）是否不为空，是否正确。

检查签名算法是否正确。

检查访问的页面的 URL 要与传递的 URL 一致。注意传递的 url 是 `https://xxx`， 访问页面也要是 `https://xxx`，而不能是 `http://xxx`。

**Question: JS-SDK debug 模式没有弹出提示**

Solution:

检查 JSSDK 的 js 引用是否正确。如，`<script src="https://res.wx.qq.com/open/js/jweixin-1.6.0.js"></script>`

查看微信开发者工具的 console，JavaScript 代码是否有语法错误。

## Appendixes

[开发者规范](https://developers.weixin.qq.com/doc/offiaccount/Getting_Started/Developer_Standards.html)

[接口权限说明](https://developers.weixin.qq.com/doc/offiaccount/Getting_Started/Explanation_of_interface_privileges.html)

[全局返回码说明](https://developers.weixin.qq.com/doc/offiaccount/Getting_Started/Global_Return_Code.html)

## References

[1] [微信公众号开发指南 - DOC](https://developers.weixin.qq.com/doc/offiaccount/Getting_Started/Overview.html)

[2] [微信公众平台](https://mp.weixin.qq.com/)