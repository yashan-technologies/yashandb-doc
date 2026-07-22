This document describes how to integrate YashanDB JDBC driver in Spring Boot + MyBatis projects and explains the configuration parameters of Hikari datasource in detail.

## Prerequisites

1. Create a Spring Boot project.

Use Spring Initializr or IDE to create a Spring Boot project with Web and MyBatis dependencies.

2. Add YashanDB JDBC driver dependency.

Add YashanDB JDBC driver dependency in pom.xml:

```xml
<dependency>
    <groupId>com.yashandb</groupId>
    <artifactId>yasdbjdbc</artifactId>
    <version>1.9.24</version>
</dependency>
```

## Project Configuration

### Project Structure

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

### Configuration File

Configure datasource and MyBatis in application.yml:

```yaml
server:
  port: 8080

spring:
  datasource:
    # YashanDB Connection Configuration
    url: jdbc:yasdb://192.168.1.2:1688/yasdb
    username: your_username
    password: your_password
    driver-class-name: com.yashandb.jdbc.Driver

    # Hikari Datasource Configuration
    hikari:
      # Basic Configuration
      pool-name: YashanDB-HikariCP
      minimum-idle: 5
      maximum-pool-size: 20
      auto-commit: true
      idle-timeout: 600000
      max-lifetime: 1800000
      connection-timeout: 30000
      connection-test-query: SELECT 1 FROM DUAL

      # Advanced Configuration
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
  # MyBatis Configuration
  mapper-locations: classpath:mapper/*.xml
  type-aliases-package: com.example.yasdbdemo.entity
  configuration:
    map-underscore-to-camel-case: true
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
```

## Code Implementation

### Entity Class

```java
package com.example.yasdbdemo.entity;

/**
 * User Entity Class
 */
public class User {
    private Long id;
    private String username;
    private String email;
    private String phone;
    private String createTime;

    // Omit getter and setter

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

### Mapper Interface

```java
package com.example.yasdbdemo.mapper;

import com.example.yasdbdemo.entity.User;
import org.apache.ibatis.annotations.Mapper;
import org.apache.ibatis.annotations.Param;

import java.util.List;

/**
 * User Mapper Interface
 */
@Mapper
public interface UserMapper {

    /**
     * Query user by ID
     */
    User selectById(@Param("id") Long id);

    /**
     * Query all users
     */
    List<User> selectAll();

    /**
     * Insert user
     */
    int insert(User user);

    /**
     * Update user
     */
    int update(User user);

    /**
     * Delete user
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

    <!-- Result Mapping -->
    <resultMap id="UserResultMap" type="com.example.yasdbdemo.entity.User">
        <id property="id" column="id"/>
        <result property="username" column="username"/>
        <result property="email" column="email"/>
        <result property="phone" column="phone"/>
        <result property="createTime" column="create_time"/>
    </resultMap>

    <!-- Query user by ID -->
    <select id="selectById" resultMap="UserResultMap">
        SELECT id, username, email, phone, create_time
        FROM users
        WHERE id = #{id}
    </select>

    <!-- Query all users -->
    <select id="selectAll" resultMap="UserResultMap">
        SELECT id, username, email, phone, create_time
        FROM users
        ORDER BY id
    </select>

    <!-- Insert user -->
    <insert id="insert" parameterType="com.example.yasdbdemo.entity.User">
        INSERT INTO users (username, email, phone, create_time)
        VALUES (#{username}, #{email}, #{phone}, SYSDATE)
    </insert>

    <!-- Update user -->
    <update id="update" parameterType="com.example.yasdbdemo.entity.User">
        UPDATE users
        SET username = #{username},
            email = #{email},
            phone = #{phone}
        WHERE id = #{id}
    </update>

    <!-- Delete user -->
    <delete id="deleteById">
        DELETE FROM users WHERE id = #{id}
    </delete>

</mapper>
```

### Service Layer

```java
package com.example.yasdbdemo.service;

import com.example.yasdbdemo.entity.User;
import com.example.yasdbdemo.mapper.UserMapper;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;

/**
 * User Service
 */
@Service
public class UserService {

    @Autowired
    private UserMapper userMapper;

    /**
     * Query user by ID
     */
    public User getUserById(Long id) {
        return userMapper.selectById(id);
    }

    /**
     * Query all users
     */
    public List<User> getAllUsers() {
        return userMapper.selectAll();
    }

    /**
     * Create user
     */
    public int createUser(User user) {
        return userMapper.insert(user);
    }

    /**
     * Update user
     */
    public int updateUser(User user) {
        return userMapper.update(user);
    }

    /**
     * Delete user
     */
    public int deleteUser(Long id) {
        return userMapper.deleteById(id);
    }
}
```

### Controller Layer

```java
package com.example.yasdbdemo.controller;

import com.example.yasdbdemo.entity.User;
import com.example.yasdbdemo.service.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.util.List;

/**
 * User Controller
 */
@RestController
@RequestMapping("/api/users")
public class UserController {

    @Autowired
    private UserService userService;

    /**
     * Get user details
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
     * Get all users
     */
    @GetMapping
    public ResponseEntity<List<User>> getAllUsers() {
        List<User> users = userService.getAllUsers();
        return ResponseEntity.ok(users);
    }

    /**
     * Create user
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
     * Update user
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
     * Delete user
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

### Application Class

```java
package com.example.yasdbdemo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

/**
 * Spring Boot Application Class
 */
@SpringBootApplication
public class YasdbDemoApplication {

    public static void main(String[] args) {
        SpringApplication.run(YasdbDemoApplication.class, args);
    }
}
```

## Hikari Datasource Configuration Parameters

### Basic Configuration Parameters

|Parameter Name |Parameter Description |Example Value |
| ------------------------- | ------------------------------------------------------------ | ---------------------- |
| pool-name | Connection pool name, used for logging and JMX identification | YashanDB-HikariCP |
| minimum-idle | Minimum number of idle connections that HikariCP will maintain | 5 |
| maximum-pool-size | Maximum number of connections in the pool | 20 |
| auto-commit | Auto-commit transactions, true means enabled, false means disabled | true |
| idle-timeout | Idle connection timeout in milliseconds. 0 means unlimited | 600000 |
| max-lifetime | Maximum connection lifetime in milliseconds. 0 means unlimited lifetime | 1800000 |
| connection-timeout | Connection acquisition timeout in milliseconds | 30000 |
| connection-test-query | Connection test query | SELECT 1 FROM DUAL |

### Advanced Configuration Parameters

|Parameter Name |Parameter Description |Example Value |
| ------------------------- | ------------------------------------------------------------ | ---------------------- |
| cachePrepStmts | Whether to cache PreparedStatement | true |
| prepStmtCacheSize | PreparedStatement cache size | 250 |
| prepStmtCacheSqlLimit | Maximum SQL length cached by PreparedStatement | 2048 |
| useServerPrepStmts | Whether to use server-side prepared statements | true |
| useLocalSessionState | Whether to use local session state | true |
| rewriteBatchedStatements | Whether to rewrite batched statements | true |
| cacheResultSetMetadata | Whether to cache ResultSet metadata | true |
| cacheServerConfiguration | Whether to cache server configuration | true |
| elideSetAutoCommits | Whether to omit setAutoCommit calls | true |
| maintainTimeStats | Whether to maintain time statistics | false |

### High Availability Configuration

Configure high availability parameters in URL to achieve failover:

```yaml
spring:
  datasource:
    url: jdbc:yasdb:primary://192.168.1.2:1688,192.168.1.3:1688;192.168.1.4:1688,192.168.1.5:1688/yasdb?poolTimeout=180&failover=on&failoverType=session&failoverMethod=basic&failoverRetries=5&failoverDelay=1
    hikari:
      maximum-pool-size: 30
      minimum-idle: 10
      connection-timeout: 60000
```

## Testing and Verification

### Start Application

```bash
mvn spring-boot:run
```

### API Testing

Use curl or Postman to test API:

```bash
# Query all users
curl http://localhost:8080/api/users

# Query user by ID
curl http://localhost:8080/api/users/1

# Create user
curl -X POST http://localhost:8080/api/users \
  -H "Content-Type: application/json" \
  -d '{"username":"testuser","email":"test@example.com","phone":"13800138000"}'

# Update user
curl -X PUT http://localhost:8080/api/users/1 \
  -H "Content-Type: application/json" \
  -d '{"username":"updateduser","email":"updated@example.com","phone":"13900139000"}'

# Delete user
curl -X DELETE http://localhost:8080/api/users/1
```

## Common Issues

1. Connection Timeout.

**Problem**: Connection acquisition timeout.

**Solution**: Increase connection-timeout value or check if the database service is running properly.

2. Connection Pool Exhausted.

**Problem**: Failed to acquire connection, prompt "Connection is not available".

**Solution**: Increase maximum-pool-size value or check for connection leaks.

3. SQL Syntax Error.

**Problem**: Prompt SQL syntax error.

**Solution**: Check if SQL statements are correct, pay attention to YashanDB syntax features.

## Performance Optimization Recommendations

1. Connection Pool Tuning.

- Adjust maximum-pool-size and minimum-idle parameters based on application concurrency.
- Set reasonable connection-timeout value.
- Configure appropriate idle-timeout and max-lifetime values.

2. SQL Optimization.

- Use indexes to optimize query performance.
- Avoid using select *, only query required fields.
- Use batch operations to improve insert/update efficiency.

3. Monitoring and Logging.

- Enable HikariCP logs to monitor connection pool status.
- Regularly check slow query logs.
- Monitor application performance metrics.
