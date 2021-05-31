---
title: Custom JSON Serialization and Deserialization
date: 2021-05-31 16:37:46
tags: JSON
categories:
- Java
- Java Web
toc: false
---

## JSON Serialization

### Custom Serializing Enumeration

```java
import com.fasterxml.jackson.core.JsonGenerator;
import com.fasterxml.jackson.databind.SerializerProvider;
import com.fasterxml.jackson.databind.ser.std.StdSerializer;
import com.yuwoyg.crm.modules.task.enumeration.NameValueEnum;

import java.io.IOException;

/**
 * Serializing Enums to JSON
 */
public class NameValueEnumSerializer extends StdSerializer<NameValueEnum> {

    protected NameValueEnumSerializer() {
        super(NameValueEnum.class);
    }

    protected NameValueEnumSerializer(Class t) {
        super(t);
    }

    @Override
    public void serialize(NameValueEnum nameValueEnum, JsonGenerator jsonGenerator, SerializerProvider serializerProvider) throws IOException {
        jsonGenerator.writeStartObject();
        jsonGenerator.writeFieldName("name");
        jsonGenerator.writeString(nameValueEnum.getName());
        jsonGenerator.writeFieldName("value");
        jsonGenerator.writeString(nameValueEnum.getValue());
        jsonGenerator.writeEndObject();
    }
}
```

```java
public interface NameValueEnum {

    public String getName();

    public void setName(String name);

    public String getValue();

    public void setValue(String value);
}
```

```java
@JsonSerialize(using = NameValueEnumSerializer.class)
public enum TaskPriority implements NameValueEnum {
    EMERGENCY("emergency", "紧急"),
    HIGH("high", "高"),
    GENERAL("general", "普通"),
    LOW("low", "低")
    ;

    private String name;

    private String value;
    
	@Override
    @JsonValue
    public String getName() {
        return name;
    }

    @Override
    public void setName(String name) {
        this.name = name;
    }

    @Override
    public String getValue() {
        return value;
    }

    @Override
    public void setValue(String value) {
        this.value = value;
    }
}
```

```java
public class Task extends BaseEntity {
    private TaskPriority priority;
}
```



## JSON Deserialization

### Deserializing Comma Separated String to List

```java
import com.fasterxml.jackson.core.JsonParser;
import com.fasterxml.jackson.core.JsonProcessingException;
import com.fasterxml.jackson.databind.DeserializationContext;
import com.fasterxml.jackson.databind.JsonDeserializer;

import java.io.IOException;
import java.util.Arrays;
import java.util.List;
import java.util.stream.Collectors;

public class CommaSeparatedStrToListJsonDeserializer
        extends JsonDeserializer<List<String>> {

    @Override
    public List<String> deserialize(JsonParser jsonParser, DeserializationContext deserializationContext) throws IOException, JsonProcessingException {
        if ("{specifiedField}".equals(jsonParser.currentName())) {
            String fieldStr = jsonParser.getValueAsString();
            if (fieldStr != null && !fieldStr.trim().isEmpty()) {
                return Arrays.stream(fieldStr.split(",")).collect(Collectors.toList());
            }
        }
        return null;
    }
}
```

```java
@JsonDeserialize(using = CommaSeparatedStrToListJsonDeserializer.class)
private List<String> {specifiedField};
```

### Deserializing JSON Array to String

```java
public class JsonArrayToStringDeserializer extends JsonDeserializer<String> {
    @Override
    public String deserialize(JsonParser jsonParser, DeserializationContext deserializationContext) throws IOException {
       if (jsonParser.currentToken() == JsonToken.START_ARRAY) {
           List<String> list = new ArrayList<>();
           jsonParser.nextToken();

           while (jsonParser.hasCurrentToken() && jsonParser.currentToken() != JsonToken.END_ARRAY) {
              list.add(jsonParser.getValueAsString());
              jsonParser.nextToken();
           }
          return String.join(",", list);
       }
       return null;
    }
}
```

```java
@JsonDeserialize(using = JsonArrayToStringDeserializer.class)
private String {specifiedField};
```

