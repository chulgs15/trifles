---
published: true
layout: single
title: "[Java] JDK/OJDBC/Oracle Database"
comments: true
categories:
  - java
tags:
  - [serious, database, oracle, java]
date: 2021-05-15
last_modified_at: 2021-05-15
toc: true
toc_sticky: true
---

 접속 테스트를 위해 Oracle 19c를 Docker로 실행.

# Oracle 19c와 JDBC 접속 버젼확인

기존 Oracle 11g를 19c Upgrade 프로젝트 중 JDBC 지원에 대한 이슈가 발생했다. jdk 1.8 이하로 만들어진 Java Web 프로그램들이 19c를 지원 여부를 확인해야 했다. Oracle의 공식 Homepage에서 확인하면 아래와 같이 나온다.

![image](https://user-images.githubusercontent.com/22446581/118341483-4be64b80-b55a-11eb-98b9-d01fc30ac3ad.png)

홈페이지 정보대로라면 대대적인 JDK 1.8 Upgrade를 해야할 상황이었다. 테스트를 위해서 Docker로 접속과 Select를 빠르게 테스트 해보았다.

# 결론

 결론부터 이야기하면 JDK 1.5와 ojdbc5.jar로 Oracle 19c접속 및 Select는 성공했다.  그러면 Connection에 있어서 JDK, JDBC, DB Version의 어떤 숫자가 영향을 미치는가? 바로 아래 jar파일의 버젼이다. 

```xml
<dependency>
    <groupId>com.oracle.database.jdbc</groupId>
    <artifactId>ojdbc5</artifactId>
    <version>11.2.0.4</version>
</dependency>
```

11.2.0.4  Version 이상은 ojdbc5.jar도 Oracle 19c에 접속할 수 있었다. 자세한 Support 내용은 [Oracle Support](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/ntcli/oracle-database-client-and-oracle-database-interoperability.html#GUID-0C6321CC-3FB9-4F3E-99B0-DFDD73034C14)에서 검색하면 확인이 가능하다. 

![image](https://user-images.githubusercontent.com/22446581/118907863-483c3580-b95b-11eb-8fce-e2772b5b681d.png)

하지만 이 JDBC만으로 접속은 불가능하다. 접속하면 아래 에러 메시지를 볼 수 있다.

```
ORA-28040: No matching authentication protocol
```

# 접속 & Select 테스트

## ALLOWED_LOGON_VERSION_SERVER

 Oracle Database는 Version을 올릴 때마다, 접속하려는 Client의 version을 높이기 위해 최소 접속 version을 정의한다. 이 정의한 version보다 낮으면 `ORA-28040` 오류가 발생한다. 그래서 Database Server의 설정을 변경해야 한다.

아래 경로의 파일에 수정이 필요하다

```bash
$ORACLE_HOME/network/admin/sqlnet.ora
```

아래 내용을 추가하자. 숫자 8은 Id/Password 접속으로도 접속이 가능하도록 설정하는 방법이다.

```
NAME.DIRECTORY_PATH= (TNSNAMES, EZCONNECT, HOSTNAME)
SQLNET.ALLOWED_LOGON_VERSION_SERVER=8
SQLNET.ALLOWED_LOGON_VERSION_CLIENT=8
```

추가가 끝나면 Oracle Listener를 다시 시작해야 한다(`lsnrctl reload`).

```
sh-4.2$ lsnrctl reload

LSNRCTL for Linux: Version 19.0.0.0.0 - Production on 20-MAY-2021 01:54:19

Copyright (c) 1991, 2019, Oracle.  All rights reserved.

Connecting to (DESCRIPTION=(ADDRESS=(PROTOCOL=IPC)(KEY=EXTPROC1)))
The command completed successfully
```

## JAVA

Simple하게 Database 버젼을 확인하는 Select를 만들었다.

```java
public class App {
  public static void main(String[] args) throws Exception {
    Connection conn = null;

    String url = "jdbc:oracle:thin:@localhost:1521:ORCLCDB"; // localhost대신 ip주소가 들어갈수도
    String id = "system";
    String pw = "123456";

    String version = System.getProperty("java.version");
    System.out.println("JAVA Version : " + version);

    Class.forName("oracle.jdbc.driver.OracleDriver");

    conn = DriverManager.getConnection(url, id, pw);

    PreparedStatement preparedStatement = conn.prepareCall("SELECT * FROM v$version");

    ResultSet resultSet = preparedStatement.executeQuery();

    while (resultSet.next()) {
      System.out.println(resultSet.getString(1));
    }

    conn.close();
  }
}
```

### 결과

```
JAVA Version : 1.5.0_22
Oracle Database 19c Standard Edition 2 Release 19.0.0.0.0 - Production
```

# 주의

 이 테스트는 단순히 접속 & Select를 실행한 것 뿐이다. 어플리케이션의 모든 비즈니스 동작을 반드시 확인해야 한다.

# 참고


  * https://www.oracle.com/database/technologies/faq-jdbc.html
  * https://nenadnoveljic.com/blog/oracle-jdbc-security/
  * https://docs.oracle.com/database/121/NETRF/sqlnet.htm#NETRF2010
  * https://cofs.tistory.com/352

