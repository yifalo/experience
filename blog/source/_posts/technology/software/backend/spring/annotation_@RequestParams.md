---
title: '@RequestParams'
categories:
  - technology
  - software
  - backend
  - spring
---

## 1. @RequestParams注解的作用

官方原话：https://www.baeldung.com/spring-request-param

---

### Simply put, we can use @RequestParam to extract query parameters, form parameters, and even files from the request.
### 简单地说，我们可以使用@RequestParam从请求中提取Query参数，Form参数甚至文件。

---

 简单例子：

```java
@GetMapping("/api/foos")
@ResponseBody
public String getFoos(@RequestParam String id) {
    return "ID: " + id;
}
```

```
http://localhost:8080/spring-mvc-basics/api/foos?id=abc
```

输出:
```
ID: abc
```

有四个属性可以设置

* name （指定变量名的来源参数名）
* value （同name）
* **required (是否必须， 默认为true)**
* defaultValue (默认值)

刚刚的例子，当required为true时，假设请求为
```
http://localhost:8080/spring-mvc-basics/api/foos
```
则会报错400

如果required为false, 则输出
```
ID: null
```

---

可以配合Optional、defaultValue等使用

## 2. 什么时候加？什么时候不加？

#### 要求请求中必须带某个参数时，需要添加@RequestParams

如Get请求根据ID查询某个用户的信息时

#### 请求中的参数不要求必须传入时，可以不加注解，或者设置required为false。

如分页搜索。page、size等参数可以不传