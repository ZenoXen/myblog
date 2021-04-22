---
title: Spring实现手机号脱敏处理
date: 2021-04-20 17:55:43
tags: [Java, Spring, 信息脱敏, 正则表达式]
categories: [Java]
description: 目前大部分互联网信息产品都会对一些用户隐私信息进行脱敏处理，就比如本文所涉及的手机号，看完本文，你可以自己用Java写一个简单的手机号脱敏处理的功能
---

# 前言

先介绍一下我这边的开发场景，数据库中存的是完整的手机号，即没有加密过的字符串，但需求希望将数据返回前端时进行脱敏处理，将手机号中间4位替换为*号。

这里还有一点值得一提，返回的ResponseBody中，可能存在一些手机号是夹杂在一个字符串中间的，需要在保留字符串原来内容的前提下，对这些手机号进行脱敏处理。

# 正则表达式实现数据脱敏

实现这个功能的思路是，使用正则表达式替换一个字符串中的所有符合手机号码特征的子串，Java的String类本身就提供了replaceAll方法，我们可以用该方法实现这个功能。

首先来看看匹配手机号码的正则和手机模糊化的正则。

## 中国手机号码的结构特征

一般中国的手机号码是11位，前三位是根据运营商而定的，4到7位为地区编码，8到11位是随机分配给用户的号码，一般加密是给中间四位地区码加密。

同时前三位手机号，第一位就是1，第二位和第三位各个运营商各有不同，基本规律我写在下面的代码中，即3可以组合0-9，4组合5或7，5组合0-3或5-9......

```java
private static final String PHONE_REGEX = "1(3[0-9]|4[57]|5[0-35-9]|7[0135678]|8[0-9])\\d{4}(\\d{4})";

private static final String PHONE_BLUR_REGEX = "1$1****$2";
```

而将中间四位替换为星号的话，需要在正则表达式中使用圆括号划分出两个区域，即第二和第三位与最后四位之间的位置。

# 自定义ResponseBody数据转换

一般来说，在我们的系统中并不是所有地方都必须得将手机号进行脱敏，有些地方可能还是需要将手机号暴露给用户的，对于不同的接口或者说对于不同的ResponseBody，可以使用Jackson的序列化器来指定如何将JSON序列化给前端。

## 自定义JsonSerializer

自定义一个DesensitizeSerializer，继承JsonSerializer\<String\>，即它是用于字符串的序列化，在方法中直接调用String的replaceAll方法，将所有符合PHONE_REGEX的字串使用PHONE_BLUR_REGEX替换为加密过的字符串。

```java
public class DesensitizeSerializer extends JsonSerializer<String> {

    private static final String PHONE_REGEX = "1(3[0-9]|4[57]|5[0-35-9]|7[0135678]|8[0-9])\\d{4}(\\d{4})";

    private static final String PHONE_BLUR_REGEX = "1$1****$2";


    @Override
    public void serialize(String s, JsonGenerator jsonGenerator, SerializerProvider serializerProvider) throws IOException {
        jsonGenerator.writeString(this.encryptPhoneNumber(s));
    }

    private String encryptPhoneNumber(String phoneNumber) {
        if(Objects.nonNull(phoneNumber)) {
            return phoneNumber.replaceAll(PHONE_REGEX, PHONE_BLUR_REGEX);
        }
        return null;
    }
}
```

## 应用JsonSerializer

一般我们使用ResponseBody注解将一个Pojo类或者JPA的interface-based projection返回给前端，虽然我们的确可以在SQL层面做预处理，但那样就容易受需求变更的影响了，而如果是在返回给前端的最后一步之前，也就是将ResponseBody序列化为Json的这一步做处理，那么就很容易面临需求的变更了。

假设现在有个UserAccount类作为ResponseBody返回给前端，其中的mobile字段需要加密，我们可以像下面这样应用之前写好的JsonSerializer。

```java
@Data
public class UserAccount {
    
    @JsonSerialize(using = DesensitizeSerializer.class)
    private String mobile;
}
```

@JsonSerialize注解可以用在getter方法上或者是类字段上，使用using属性可以为这个字段指定一个JsonSerializer。

写一个测试接口，来看看结果。

```json
{"mobile":"135****3843"}
```

如果手机号是混杂在一个很长的字符串里的，也不必担心，String的replaceAll方法可以将整个字符串中所有的手机号加密。

# 参考文章

[手机号校验与脱敏处理](https://www.pianshen.com/article/7263153515/)