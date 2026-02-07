GET/PUT/POST/DELETE


我现在创建了一个 REST API。  
当我用 curl 发送 **POST** 请求到 `/product` 这个 endpoint，并带上 JSON request body，Spring 会调用我 controller 里的 `createProduct(...)` 方法。  
这个方法返回一个 `ResponseEntity`，里面包含 **HTTP 状态码**（201 Created）和 **响应 body**（例如 `{"product_id": 123}`）。

```java
package com.cs6650_assignment.controller;

import java.util.Map;
import java.util.concurrent.ThreadLocalRandom;
import lombok.extern.slf4j.Slf4j;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@Slf4j
@RestController
@RequestMapping("/product")

public class ProductController {
  @PostMapping
  public ResponseEntity<Map<String, Integer>> createProduct(@RequestBody String input) {
    log.debug("POST /product received");
    int productId = ThreadLocalRandom.current().nextInt(1, Integer.MAX_VALUE);
    return ResponseEntity
        .status(HttpStatus.CREATED)
        .body(Map.of("product_id", productId));
  }

}
```

```java
@Slf4j // 启用 logger：你可以直接用 `log.debug/info/error(...)`。
@RestController // 让 Spring 扫描并注册这个类为 Controller，并自动把返回值写入响应 body（不走 view）。
@RequestMapping("/product") // 这个 Controller 的 base path：所以方法上不写路径时就是 `/product`。

@PostMapping // - 这个方法处理 `POST /product`。建议加 `consumes/produces` 更明确。  
```



### Response:

```java
return ResponseEntity.ok(data); // 200
return ResponseEntity.status(HttpStatus.OK).body(data);

return ResponseEntity.status(HttpStatus.NO_CONTENT).build(); // 400 Bad request
```

# Health check

```java
package com.cs6650_assignment.controller;

import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HealthController {

  @GetMapping("/health")
  public ResponseEntity<String> health() {
    return ResponseEntity.ok("ok");
  }
}

```