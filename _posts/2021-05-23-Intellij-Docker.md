---
published: true
layout: single
title: "[Java] Intellij로 Angular + Spring Boot + Docker 구성하기(2)"
comments: true
categories:
  - java
tags:
  - [serious, intellij, docker, angular, spring]
date: 2021-05-26
last_modified_at: 2021-05-26
toc: true
toc_sticky: true
---

 Intellij Enterprise 기능으로 Frontend/Backend/Docker를 구성해보자.

<img src="https://user-images.githubusercontent.com/22446581/119630731-2e628d00-be4a-11eb-8602-6613b7b876c2.jpg" alt="Angular + Spring Boot + Mysql(Docker)" style="zoom: 33%;" />

# 해보고 싶은 것.

 DB를 도커로 실행하고, Backend로 연결하면 Frontend-Backend-DB Imange 연결을 완성한다.

## Mysql

### Folder/File 구성

 아래와 같이 File과 Folder를 구성한다.

![image](https://user-images.githubusercontent.com/22446581/119624777-72529380-be44-11eb-8bb1-230e0ca6d883.png)



### mysql_data

Docker는 기본적으로 데이터를 지우기 때문에, Host에 저장할 역활로 만든 Folder다. 해당 폴더는 Git과 연동되면 안되기 때문에 `.gitignore`에 아래와 같이 설정한다.

```
mysql_data/*
```

### Dockerfile

DB Docker 이미지를 실행하기 위한 데이터다. 사용자는 `scott`, 사용할 DB는 `myapp`을 사용할 예정이다.

```dockerfile
FROM mysql:5.7

ENV MYSQL_USER=scott
ENV MYSQL_PASSWORD=123456
ENV MYSQL_ROOT_PASSWORD=123456
ENV MYSQL_DATABASE=myapp
ENV MYSQL_PORT=3306

VOLUME ./mysql_data:/var/lib/mysql

EXPOSE 3306

ADD ./my.cnf /etc/mysql/conf.d/my.cnf
```

### my.cnf

Mysql의 서버 설정을 위한 파일이다. 

```
[mysqld]
character-set-server=utf8

[mysql]
default-character-set=utf8

[client]
default-character-set=utf8
```

## Intellij 에서 Docker실행.

사실 명령어나 Docker Window 프로그램으로 실행해도 상관없지만, 이번에는 Intellij 에서 실행해보자.

### Docker 실행 설정

`Run Configuration`을 실행하자

![image](https://user-images.githubusercontent.com/22446581/119625843-9662a480-be45-11eb-8710-b972eb74ab1f.png)

### 데이터 실행

여기서 중요한 점은 `port`를 꼭 Mapping해야 한다.

![image](https://user-images.githubusercontent.com/22446581/119626436-2dc7f780-be46-11eb-9d04-e24e21b4963b.png)

아래와 같이 실행결과가 나타난다.

![image](https://user-images.githubusercontent.com/22446581/119629029-9a43f600-be48-11eb-8dcb-1639a98b36a5.png)

#### 재실행 시...

재 실행하려면 아래를 클릭하면 된다.

![image](https://user-images.githubusercontent.com/22446581/119628857-71bbfc00-be48-11eb-9f46-c53c1412a800.png)

## Backend-DB 연결

Spring JPA와 DB를 연결한다.

### application.properties

DB 연결 정보를 Backend로 설정한다.

```properties
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/myapp?serverTimezone=UTC&characterEncoding=UTF-8
spring.datasource.username=scott
spring.datasource.password=123456


# SQL문장을 콘솔에 출력한다.
spring.jpa.show-sql=true

# DDL 을 생성하는 설정
spring.jpa.generate-ddl=true

# MySQL
spring.jpa.database=mysql

# MySQL 방언 설정
spring.jpa.database-platform=org.hibernate.dialect.MySQL5InnoDBDialect
```

### Domain 생성

#### Board

```java
@Entity
@Getter
@Table(name = "board")
public class Board {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long boardId;

    @ManyToOne(fetch = FetchType.EAGER)
    @JoinColumn(name = "user_name")
    private AccountUser user;

    @Column(name = "message")
    private String message;

    @CreationTimestamp
    @DateTimeFormat(pattern = "yyyy-MM-dd HH:mm:ss")
    private LocalDateTime creationDate;

    @UpdateTimestamp
    @DateTimeFormat(pattern = "yyyy-MM-dd HH:mm:ss")
    private LocalDateTime lastUpdateDate;


    public Board() {
    }

    @Builder
    public Board(AccountUser user, String message) {
        this.user = user;
        this.message = message;
    }

    @Override
    public String toString() {
        return "Board{" +
                "boardId=" + boardId +
                ", user=" + user.getUserName() +
                ", message='" + message + '\'' +
                ", creationDate=" + creationDate +
                ", lastUpdateDate=" + lastUpdateDate +
                '}';
    }
}

```

#### AccountUser

```java
@Entity
@Getter
@Table(name = "fnd_user")
public class AccountUser {
    @Id
    @Column(name = "user_name")
    private String userName;

    @Column(name = "password")
    @JsonIgnore
    private String password;

    @OneToMany(mappedBy = "user")
    @JsonIgnore
    private List<Board> boardList;

    public AccountUser() {
    }

    @Builder
    public AccountUser(String userName, String password) {
        this.userName = userName;
        this.password = password;
    }

    @Override
    public String toString() {
        return "AccountUser{" +
                "userName='" + userName + '\'' +
                ", password='" + password + '\'' +
                '}';
    }
}
```

### TestController 추가.

```java
@RestController
@Slf4j
@RequiredArgsConstructor
public class TestController {

    private final AccountUserRepository userRepository;
    private final BoardRepository boardRepository;

    @GetMapping(value = "/api/hello")
    public String helloWorld() {
        log.info("hello world is coming");
        return "hello world";
    }

    @PostMapping(value = {"/api/user/loginProcess"}, produces = MediaType.APPLICATION_JSON_VALUE)
    public LoginDto loginProcess(@RequestBody LoginDto loginDto) {
       ....
    }

    @PostMapping(value = {"/api/user/register"}, produces = MediaType.APPLICATION_JSON_VALUE)
    public void registerUser(@RequestBody LoginDto loginDto) {
       ...
    }


    @PostMapping("/api/board/register")
    public void registerBoard(@RequestBody BoardDto boardDto) {
        ...
    }

    @GetMapping(value = "/api/board/user={userName}", produces = MediaType.APPLICATION_JSON_VALUE)
    public List<Board> getBoardListByUserName(@PathVariable("userName") String userName) {
        ...
    }

    @GetMapping(value = "/api/board/")
    public List<Board> getBoardList()  {
        ...
    }

    @Getter
    private static class LoginDto{
        private String userName;
        private String password;

        public LoginDto() {
        }

        public LoginDto(String userName, String password) {
            this.userName = userName;
            this.password = password;
        }
    }

    @Getter
    private static class BoardDto{
        private String userName;
        private String message;

        public BoardDto() {
        }
    }

}

```

