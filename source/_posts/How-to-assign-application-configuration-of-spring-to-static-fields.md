---
title: How to Assign Application Configuration of Spring to Static Fields
date: 2021-05-20 15:05:20
tags: Spring
categories:
- Java
- Java Web
---

The Solution

1. Using `@Component` inject the bean to the Spring IoC container.
2. Using `@Value` read application configurations of Spring into non-static fields.
3. Implements `InitializingBean` override `afterPropertiesSet()` method. Assigning non-static field values to static field values in `afterPropertiesSet()` method.

For example:

```java
@Component
public class FileUploadUtils implements InitializingBean
{
    private static String endpoint;
    private static String accessKeyId;
    private static String accessKeySecret;
    private static String bucketName;
    private static OSSClient ossClient;

    @Value("${app.fileUpload.oss.endpoint}")
    private String getEndpoint;

    @Value("${app.fileUpload.oss.accessKeyId}")
    private String getAccessKeyId;

    @Value("${app.fileUpload.oss.accessKeySecret}")
    private String getAccessKeySecret;

    @Value("${app.fileUpload.oss.bucketName}")
    private String getBucketName;

    @Override
    public void afterPropertiesSet() throws Exception {
        endpoint = getEndpoint;
        accessKeyId = getAccessKeyId;
        accessKeySecret = getAccessKeySecret;
        bucketName = getBucketName;
        ossClient = new OSSClient(endpoint, accessKeyId, accessKeySecret);
    }
}
