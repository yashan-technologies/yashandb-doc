本文档介绍如何在Spring Boot + MyBatis项目中集成YashanDB JDBC驱动，并详细说明Hikari数据源的配置参数。

## 准备工作

1. 创建Spring Boot项目。

使用Spring Initializr或IDE创建一个Spring Boot项目，添加Web和MyBatis依赖。

2. 添加YashanDB JDBC驱动依赖。

在pom.xml中添加YashanDB JDBC驱动依赖：

```xml
<dependency>
    <groupId>com.yashandb</groupId>
    <artifactId>yasdbjdbc</artifactId>
    <version>1.9.24</version>
</dependency>
```

## 项目配置

### 项目结构

```java
src/
├── main/
│   ├── java/
│   │   └── com/
│   │       └── example/
│   │           └── yasdbdemo/
│   │               ├── YasdbDemoApplication.java
│   │               ├── controller/
│   │               │   └── UserController.java
│   │               ├── mapper/
│   │               │   └── UserMapper.java
│   │               ├── entity/
│   │               │   └── User.java
│   │               └── service/
│   │                   └── UserService.java
│   └── resources/
│       ├── mapper/
│       │   └── UserMapper.xml
│       └── application.yml
```

### 配置文件

在application.yml中配置数据源和MyBatis：

```yaml
server:
  port: 8080

spring:
  datasource:
    # YashanDB连接配置
    url: jdbc:yasdb://192.168.1.2:1688/yasdb
    username: your_username
    password: your_password
    driver-class-name: com.yashandb.jdbc.Driver

    # Hikari数据源配置
    hikari:
      # 基础配置
      pool-name: YashanDB-HikariCP
      minimum-idle: 5
      maximum-pool-size: 20
      auto-commit: true
      idle-timeout: 600000
      max-lifetime: 1800000
      connection-timeout: 30000
      connection-test-query: SELECT 1 FROM DUAL

      # 高级配置
      cachePrepStmts: true
      prepStmtCacheSize: 250
      prepStmtCacheSqlLimit: 2048
      useServerPrepStmts: true
      useLocalSessionState: true
      rewriteBatchedStatements: true
      cacheResultSetMetadata: true
      cacheServerConfiguration: true
      elideSetAutoCommits: true
      maintainTimeStats: false

mybatis:
  # MyBatis配置
  mapper-locations: classpath:mapper/*.xml
  type-aliases-package: com.example.yasdbdemo.entity
  configuration:
    map-underscore-to-camel-case: true
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
```

## 代码实现

### 实体类

```java
package com.example.yasdbdemo.entity;

/**
 * 用户实体类
 */
public class User {
    private Long id;
    private String username;
    private String email;
    private String phone;
    private String createTime;

    // 省略getter和setter

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    public String getPhone() {
        return phone;
    }

    public void setPhone(String phone) {
        this.phone = phone;
    }

    public String getCreateTime() {
        return createTime;
    }

    public void setCreateTime(String createTime) {
        this.createTime = createTime;
    }
}
```

### Mapper接口

```java
package com.example.yasdbdemo.mapper;

import com.example.yasdbdemo.entity.User;
import org.apache.ibatis.annotations.Mapper;
import org.apache.ibatis.annotations.Param;

import java.util.List;

/**
 * 用户Mapper接口
 */
@Mapper
public interface UserMapper {

    /**
     * 根据ID查询用户
     */
    User selectById(@Param("id") Long id);

    /**
     * 查询所有用户
     */
    List<User> selectAll();

    /**
     * 插入用户
     */
    int insert(User user);

    /**
     * 更新用户
     */
    int update(User user);

    /**
     * 删除用户
     */
    int deleteById(@Param("id") Long id);
}
```

### Mapper XML  /  Mapper XML

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.example.yasdbdemo.mapper.UserMapper">

    <!-- 结果映射 -->
    <resultMap id="UserResultMap" type="com.example.yasdbdemo.entity.User">
        <id property="id" column="id"/>
        <result property="username" column="username"/>
        <result property="email" column="email"/>
        <result property="phone" column="phone"/>
        <result property="createTime" column="create_time"/>
    </resultMap>

    <!-- 根据ID查询用户 -->
    <select id="selectById" resultMap="UserResultMap">
        SELECT id, username, email, phone, create_time
        FROM users
        WHERE id = #{id}
    </select>

    <!-- 查询所有用户 -->
    <select id="selectAll" resultMap="UserResultMap">
        SELECT id, username, email, phone, create_time
        FROM users
        ORDER BY id
    </select>

    <!-- 插入用户 -->
    <insert id="insert" parameterType="com.example.yasdbdemo.entity.User">
        INSERT INTO users (username, email, phone, create_time)
        VALUES (#{username}, #{email}, #{phone}, SYSDATE)
    </insert>

    <!-- 更新用户 -->
    <update id="update" parameterType="com.example.yasdbdemo.entity.User">
        UPDATE users
        SET username = #{username},
            email = #{email},
            phone = #{phone}
        WHERE id = #{id}
    </update>

    <!-- 删除用户 -->
    <delete id="deleteById">
        DELETE FROM users WHERE id = #{id}
    </delete>

</mapper>
```

### Service层

```java
package com.example.yasdbdemo.service;

import com.example.yasdbdemo.entity.User;
import com.example.yasdbdemo.mapper.UserMapper;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;

/**
 * 用户Service
 */
@Service
public class UserService {

    @Autowired
    private UserMapper userMapper;

    /**
     * 根据ID查询用户
     */
    public User getUserById(Long id) {
        return userMapper.selectById(id);
    }

    /**
     * 查询所有用户
     */
    public List<User> getAllUsers() {
        return userMapper.selectAll();
    }

    /**
     * 创建用户
     */
    public int createUser(User user) {
        return userMapper.insert(user);
    }

    /**
     * 更新用户
     */
    public int updateUser(User user) {
        return userMapper.update(user);
    }

    /**
     * 删除用户
     */
    public int deleteUser(Long id) {
        return userMapper.deleteById(id);
    }
}
```

### Controller层

```java
package com.example.yasdbdemo.controller;

import com.example.yasdbdemo.entity.User;
import com.example.yasdbdemo.service.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.util.List;

/**
 * 用户Controller
 */
@RestController
@RequestMapping("/api/users")
public class UserController {

    @Autowired
    private UserService userService;

    /**
     * 获取用户详情
     */
    @GetMapping("/{id}")
    public ResponseEntity<User> getUser(@PathVariable Long id) {
        User user = userService.getUserById(id);
        if (user != null) {
            return ResponseEntity.ok(user);
        }
        return ResponseEntity.notFound().build();
    }

    /**
     * 获取所有用户
     */
    @GetMapping
    public ResponseEntity<List<User>> getAllUsers() {
        List<User> users = userService.getAllUsers();
        return ResponseEntity.ok(users);
    }

    /**
     * 创建用户
     */
    @PostMapping
    public ResponseEntity<String> createUser(@RequestBody User user) {
        int result = userService.createUser(user);
        if (result > 0) {
            return ResponseEntity.ok("User created successfully");
        }
        return ResponseEntity.badRequest().body("Failed to create user");
    }

    /**
     * 更新用户
     */
    @PutMapping("/{id}")
    public ResponseEntity<String> updateUser(@PathVariable Long id, @RequestBody User user) {
        user.setId(id);
        int result = userService.updateUser(user);
        if (result > 0) {
            return ResponseEntity.ok("User updated successfully");
        }
        return ResponseEntity.notFound().build();
    }

    /**
     * 删除用户
     */
    @DeleteMapping("/{id}")
    public ResponseEntity<String> deleteUser(@PathVariable Long id) {
        int result = userService.deleteUser(id);
        if (result > 0) {
            return ResponseEntity.ok("User deleted successfully");
        }
        return ResponseEntity.notFound().build();
    }
}
```

### 启动类

```java
package com.example.yasdbdemo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

/**
 * Spring Boot启动类
 */
@SpringBootApplication
public class YasdbDemoApplication {

    public static void main(String[] args) {
        SpringApplication.run(YasdbDemoApplication.class, args);
    }
}
```

## Hikari数据源配置参数详解

### 基础配置参数

| 参数名| 参数说明| 示例值|
| ------------------------- | ------------------------------------------------------------ | ---------------------- |
| pool-name | 连接池名称，用于日志和JMX标识 | YashanDB-HikariCP |
| minimum-idle | 最小空闲连接数，HikariCP会保持的最小空闲连接数 | 5 |
| maximum-pool-size | 连接池最大连接数 | 20 |
| auto-commit | 自动提交事务，true表示开启，false表示不开启 | true |
| idle-timeout | 空闲连接超时时间，单位为毫秒。0表示无限空闲 | 600000 |
| max-lifetime | 连接最大生命周期，单位为毫秒。0表示无限生命周期 | 1800000 |
| connection-timeout | 获取连接超时时间，单位为毫秒 | 30000 |
| connection-test-query | 连接测试查询语句 | SELECT 1 FROM DUAL |

### 高级配置参数

| 参数名| 参数说明| 示例值|
| ------------------------- | ------------------------------------------------------------ | ---------------------- |
| cachePrepStmts | 是否缓存PreparedStatement | true |
| prepStmtCacheSize | PreparedStatement缓存大小 | 250 |
| prepStmtCacheSqlLimit | PreparedStatement缓存的SQL最大长度 | 2048 |
| useServerPrepStmts | 是否使用服务器端预处理语句 | true |
| useLocalSessionState | 是否使用本地会话状态 | true |
| rewriteBatchedStatements | 是否重写批量语句 | true |
| cacheResultSetMetadata | 是否缓存ResultSet元数据 | true |
| cacheServerConfiguration | 是否缓存服务器配置 | true |
| elideSetAutoCommits | 是否省略setAutoCommit调用 | true |
| maintainTimeStats | 是否维护时间统计信息 | false |

### 高可用配置

在URL中配置高可用参数，实现故障转移：

```yaml
spring:
  datasource:
    url: jdbc:yasdb:primary://192.168.1.2:1688,192.168.1.3:1688;192.168.1.4:1688,192.168.1.5:1688/yasdb?poolTimeout=180&failover=on&failoverType=session&failoverMethod=basic&failoverRetries=5&failoverDelay=1
    hikari:
      maximum-pool-size: 30
      minimum-idle: 10
      connection-timeout: 60000
```

## 测试验证

### 启动应用

```bash
mvn spring-boot:run
```

### API测试

使用curl或Postman测试API：

```bash
# 查询所有用户
curl http://localhost:8080/api/users

# 根据ID查询用户
curl http://localhost:8080/api/users/1

# 创建用户
curl -X POST http://localhost:8080/api/users \
  -H "Content-Type: application/json" \
  -d '{"username":"testuser","email":"test@example.com","phone":"13800138000"}'

# 更新用户
curl -X PUT http://localhost:8080/api/users/1 \
  -H "Content-Type: application/json" \
  -d '{"username":"updateduser","email":"updated@example.com","phone":"13900139000"}'

# 删除用户
curl -X DELETE http://localhost:8080/api/users/1
```

## 常见问题

1. 连接超时。

**问题**：连接获取超时。

**解决方案**：增加connection-timeout值或检查数据库服务是否正常运行。

2. 连接池耗尽。

**问题**：获取连接失败，提示"Connection is not available"。

**解决方案**：增加maximum-pool-size值或检查是否有连接泄漏。

3. SQL语法错误。

**问题**：提示SQL语法错误。

**解决方案**：检查SQL语句是否正确，注意YashanDB的语法特性。

## 性能优化建议

1. 连接池调优。

- 根据应用并发量调整maximum-pool-size和minimum-idle参数。
- 设置合理的connection-timeout值。
- 配置合适的idle-timeout和max-lifetime值。

2. SQL优化。

- 使用索引优化查询性能。
- 避免使用select *，只查询需要的字段。
- 使用批量操作提高插入/更新效率。

3. 监控与日志。

- 启用HikariCP日志监控连接池状态。
- 定期检查慢查询日志。
- 监控应用性能指标。
