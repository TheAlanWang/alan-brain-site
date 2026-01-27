Link: [Spring Initializr](https://start.spring.io/)

1. Navigate to [https://start.spring.io](https://start.spring.io)
2. Configure your project with the same settings as above
3. Click "Generate" to download the project ZIP file
4. Extract and open in your IDE

```bash
mvn spring-boot:run
```

## Picture
![[Spring_boot_initializr.png]]

Artifact: don't name with `_`
### Dependencies
#### Spring Web 
Spring Web is what enables you to build a REST API. It provides:
- an embedded web server (Tomcat by default)
- controller annotations like `@RestController`, `@PostMapping`, `@RequestBody`
- request/response handling and JSON serialization
**中文：**  
Spring Web 让你的项目变成一个可以对外提供 HTTP 服务的 Web 应用（REST API）。它包含了：
- 内置 Web 服务器（默认 Tomcat），你 `mvn spring-boot:run` 后就能在 `localhost:8080` 访问
- 写接口的注解：`@RestController`, `@GetMapping`, `@PostMapping`, `@RequestBody` 等
- 处理请求/响应、JSON 序列化（常用的是 Jackson）
#### Lombok
Lombok reduces boilerplate Java code by generating things like getters/setters, constructors, `toString()`, etc., via annotations.

**中文：**  
Lombok 是一个“减少样板代码”的工具库。Java 很多类会有一堆重复代码，比如：
- `getter / setter`
- `toString()`
- `equals() / hashCode()`
- 构造函数

Lombok 用注解自动生成这些，比如：
- `@Getter @Setter`
- `@Data`（一键生成 getter/setter/toString/equals/hashCode）
- `@AllArgsConstructor` / `@NoArgsConstructor`
- `@Builder`

