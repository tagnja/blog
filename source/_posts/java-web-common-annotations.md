---
title: Java Web Common Annotations
date: 2021-03-26 16:03:28
tags: javaweb
categories:
- Java
- Java Web
---

## Java SE

```java
@Deprecated // programmers are discouraged from using, typically because it is dangerous, or because a better alternative exists.
@FunctionalInterface
@Override // Indicates that a method declaration is intended to override a method declaration in a supertype.
@SuppressWarnings // Indicates that the named compiler warnings should be suppressed in the annotated element (and in all program elements contained in the annotated element).
@SafeVarargs // A programmer assertion that the body of the annotated method or constructor does not perform potentially unsafe operations on its varargs parameter.
```



## Spring Projects

### Spring

Bean declaration

```java
@Component
@Controller
@Service
@Repository
```

Bean injection

```java
@Autowired
@Resource
```

Specify inject bean name

```java
@Qualifier("specificBeanName")
```

Bean initMethod and destroyMethod

```java
@PostConstruct
public void init() {}
```

```java
@PreDestroy
public void destroy() {}
```

Configurations

```java
@Configuration
@Bean
@ConditionalOnBean
@ConditionalOnMissingBean
@ConditionalOnProperty
@ConditionalOnResource
@ConditionalOnWebApplication
@ConditionalExpression
@Conditional
```

Configuration file properties

```java
@PropertySource
public class MySqlDriver {}
```

```java
@Value("${databaseName}")
private String databaseName;
```



### Spring MVC

Request

```java
@Controller
@RestController
@RequestMapping
@GetMapping
@PostMapping
@PutMapping
@DeleteMapping
@PatchMapping
```

Request Information

```java
@PathVariable
@RequestParam
@ModelAttribute
@RequestBody
@RequestHeader
```

Response

```java
@ResponseBody
@ResponseHeader
@ResponseStatus
```

Data Validation

```
@Valid
@Validated(groupClass)
```

Data Validation Constraints Annotations 

See [JavaEE Validation Constraints Annotations](#Validation-Constraints)



Exception Handling

```java
@ExceptionHandler
```



### Spring Data Access

```java
@Transactional(rollbackFor=Exception.class)
```

By default all `RuntimeException`s rollback transaction whereas checked exceptions don't. This is an EJB legacy. You can configure this by using `rollbackFor()` and `noRollbackFor()` annotation parameters: `@Transactional(rollbackFor=Exception.class)`. This will rollback transaction after throwing **any** exception.

Programatically and manually roll back 

```
TransactionAspectSupport.currentTransactionStatus().setRollbackOnly();
```



### Spring Security

```java
@EnableWebSecurity
```



### Spring Boot

```java
@SpringBootApplication
```

- @SpringBootApplication = @Configuration + @ComponentScan + @EnableAutoConfiguration
- @Configuration: This annotation marks a class as a Configuration class for Java-based configuration. This is particularly important if you favor Java-based configuration over XML configuration. 
- @ComponentScan: This annotation enables component-scanning so that the web controller classes and other components you create will be automatically discovered and registered as beans in Spring's Application Context.
- @EnableAutoConfiguration: This annotation enables the magical auto-configuration feature of Spring Boot, which can automatically configure a lot of stuff for you.

## Java EE

###  Validation Constraints

```java
@NotNull
@Null
@NotBlank // The annotated element must not be null and must contain at least one non-whitespace character.
@NotEmpty // The annotated element must not be null nor empty.
@Size // The annotated element size must be between the specified boundaries (included).
@Pattern // The annotated CharSequence must match the specified regular expression.
@Email
```

```java
@Digits
@Min
@Max
@DecimalMax
@DecimalMin
@Positive
@PositiveOrZero
@Negative
@NegativeOrZero
```

```java
@Future // The annotated element must be an instant, date or time in the future.
@FutureOrPresent
@Past // The annotated element must be an instant, date or time in the past.
@PastOrPresent
```

[Java EE 8 javax.validation.constraints annotations](https://javaee.github.io/javaee-spec/javadocs/javax/validation/package-summary.html)



## Jackson

### JSON Serialization

Update format of serialized JSON value from Date type 

```java
@JsonFormat(pattern = "yyyy-MM-dd HH:mm:ss", timezone = "GMT+8")
private Date createTime;
```

Update field name of serialized JSON

```java
@JsonProperty(value = "user_password")
private String userPassword;
```

Ignore properties for JSON serialization

```java
@JsonIgnore // for field
@JsonIgnoreProperties({"fieldname1", "fieldname2"}) // for POJO class
@JsonInclude // annotation used to define if certain "non-values" (nulls or empty values) should not be included when serializing
```

Serialize Enum to JSON String

```java
@JsonSerialize(using = NameValueEnumSerializer.class)
public enum Type {
}
```

Property documentation, metadata

```
@JsonPropertyDescription
```



### JSON Deserialization

Convert JSON String to Date

```java
@DateTimeFormat(pattern = "yyyy-MM-dd HH:mm:ss")
private Date createTime;
```

Convert JSON String to Enum field

```java
@JsonValue
public String getName() {
	return name;
}
```

[Jackson Annotations](https://github.com/FasterXML/jackson-annotations/wiki/Jackson-Annotations)



## Hibernate

```java
@Entity
@Table
@Id
@GeneratedValue
```



## MyBatis-Plus

Specify Table name

```java
@TableName
```

Specify primary key

```java
@TableId(value = "id", type = IdType.AUTO)
```

Specify field is not as a column of table of database

```java
@TableField(exist = false)
```

When field name same with keywords of database need use backquote to avoid SQL execution error

```java
@TableField("`column`")
private String column;
```

Logic delete

```java
@TableLogic
```

[MyBatis-Plus Annotations](https://baomidou.com/guide/annotation.html)

## Lombok

POJO

```java
@Data
@Getter/@Setter
@ToString
@NoArgsConstructor, @RequiredArgsConstructor and @AllArgsConstructor
@EqualsAndHashCode
@Builder // User.builder().name("Jack").build()
```

- `@Data`: A shortcut for `@ToString`, `@EqualsAndHashCode`, `@Getter` on all fields, and `@Setter` on all non-final fields, and `@RequiredArgsConstructor`.

Generate a null-check statement

```java
public NonNullExample(@NonNull Person person) {
	super("Hello");
	this.name = person.getName();
}
```

Result code

```java
public NonNullExample(@NonNull Person person) {
	super("Hello");
	if (person == null) {
	  throw new NullPointerException("person is marked @NonNull but is null");
	}
	this.name = person.getName();
}
```

Automatic resource management: Call your close() methods safely with no hassle.

```java
public static void main(String[] args) throws IOException {
    @Cleanup InputStream in = new FileInputStream(args[0]);
    @Cleanup OutputStream out = new FileOutputStream(args[1]);
    byte[] b = new byte[10000];
    while (true) {
      int r = in.read(b);
      if (r == -1) break;
      out.write(b, 0, r);
    }
}
```

To sneakily throw checked exceptions without actually declaring this in your method's throws clause

```java
@SneakyThrows(UnsupportedEncodingException.class)
public String utf8ToString(byte[] bytes) {
    return new String(bytes, "UTF-8");
}
```

Result code

```java
public String utf8ToString(byte[] bytes) {
    try {
      return new String(bytes, "UTF-8");
    } catch (UnsupportedEncodingException e) {
      throw Lombok.sneakyThrow(e);
    }
}
```

Annotate any class with a log annotation to let lombok generate a logger field.

```java
@Log
@Log4j
@Log4j2
@Slf4j
```

```java
private static final java.util.logging.Logger log = java.util.logging.Logger.getLogger(LogExample.class.getName());

private static final org.apache.log4j.Logger log = org.apache.log4j.Logger.getLogger(LogExample.class);

private static final org.apache.logging.log4j.Logger log = org.apache.logging.log4j.LogManager.getLogger(LogExample.class);

private static final org.slf4j.Logger log = org.slf4j.LoggerFactory.getLogger(LogExample.class);
```

Others

```java
@Value
@Synchronized
@With
@Getter(lazy=true)
```

[Lombok features](https://projectlombok.org/features/all)