---
title: Java Web Common Annotations
date: 2021-03-26 16:03:28
tags: javaweb
categories:
- Java
- Java Web
---



## Spring

Bean declaration

```
@Configuration
@Bean
```

```
@Component
@Controller
@Service
@Repository
```

Bean Injection

```
@Autowired
@Resource
```

Specify inject bean name

```
@Qualifier("specificBeanName")
```

Bean initMethod and destroyMethod.

```
@PostConstruct
public void init() {}
```

```
@PreDestroy
public void destroy() {}
```

Configuration file properties

```
@PropertySource
public class MySqlDriver {}
```

```
@Value("${databaseName}")
private String databaseName;
```



### Spring MVC

Request

```
@Controller
@RequestMapping
@PathVariable
@RequestParam
@ModelAttribute
@RequestBody
@RequestHeader
```

Response

```
@ResponseBody
@ResponseHeader
```

Data Validation

```
@Valid
@Validated(groupClass)
```



### Spring Data Access

```
@Transactional
```



### Spring Security

```
@EnableWebSecurity
```

### Spring Boot

```
@SpringBootApplication
@EnableAutoConfiguration
```



### JSON Serialize

Update format of serialized JSON value from Date type 

```
@JsonFormat(pattern = "yyyy-MM-dd HH:mm:ss", timezone = "GMT+8")
private Date createTime;
```

Update field name of serialized JSON

```
@JsonProperty(value = "user_password")
private String userPassword;
```

Ignore properties for JSON serialization

```
@JsonIgnore // for field
@JsonIgnoreProperties("fieldname") // for POJO class
```

Serialize Enum to JSON String

```
@JsonSerialize(using = NameValueEnumSerializer.class)
public enum Type {
}
```



### JSON Deserialize

Convert JSON String to Date

```
@DateTimeFormat(pattern = "yyyy-MM-dd HH:mm:ss")
private Date createTime;
```

Convert JSON String to Enum field

```
@JsonValue
public String getName() {
	return name;
}
```



## MyBatis-Plus

Specify Table name

```
@TableName
```

Specify primary key

```
@TableId(value = "id", type = IdType.AUTO)
```

Specify field is not as a column of table of database

```
@TableField(exist = false)
```

When field name same with keywords of database need use backquote to avoid SQL execution error

```
@TableField("`column`")
private String column;
```

Logic delete

```
@TableLogic
```



