---
layout: post
title:  "챕터1 - DAO 개선하며 객체지향적으로 설계해보기"
excerpt: ""

categories:
  - Toby's Spring
tags:
  - [spring, java]

toc: true
toc_sticky: true
comments: true
 
date: 2021-07-31
last_modified_at: 2021-07-31
---

``토비의 스프링 3.1 의 챕터1의 일부분을 요약한 내용입니다``

> DAO(Data Access Object)
>
> DB를 사용해 데이터를 조회하거나 조작하는 기능을 전담하는 오브젝트



아래와 같이 User 객체가 있고, DB에 이와 동일한 user 테이블이 있다고 가정하자

```java
package springbook.user.domain;

public class User {
	String id;
	String name;
	String password;
	
	public String getId() {
		return id;
	}
	public void setId(String id) {
		this.id = id;
	}
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	public String getPassword() {
		return password;
	}
	public void setPassword(String password) {
		this.password = password;
	
```



## 최초의 DAO 만들기 (겁나 대충 만들어보기)

DB에 접근하여 사용자 정보를 관리할 수 있는 DAO를 만들어보겠다.

클래스의 확장성, 응집도 등등은 전혀 생각하지 말고 기본 기능만 하도록 짜본다.

UserDao가 user 테이블의 데이터를 조회, 조작하기 위해 가져야할 관심사는 아래와 같다

- DB 연결을 위한 Connection 가져오기
- SQL을 담은 PreparedStatement 만들기
- 조회의 경우 쿼리 결과를 ResultSet으로 받아서 저장할 오브젝트로 담아주기
- 작업중에 생성된 리소스 닫아주기(Connection, Statement, ResultSet)
- JDBC API가 만들어내는 예외를 잡아서 처리하거나 외부로 던져(throws)주기



위와 같은 관심사를 토대로 기본적인 DAO를 만들면 아래와 같다.

```java
package springbook.user.dao;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

import springbook.user.domain.User;

public class UserDao {
	public void add(User user) throws ClassNotFoundException, SQLException {
		Class.forName("com.mysql.jdbc.Driver");
		Connection c = DriverManager.getConnection("jdbc:mysql://localhost/springbook?characterEncoding=UTF-8", "spring",
				"book");

		PreparedStatement ps = c.prepareStatement(
			"insert into users(id, name, password) values(?,?,?)");
		ps.setString(1, user.getId());
		ps.setString(2, user.getName());
		ps.setString(3, user.getPassword());

		ps.executeUpdate();

		ps.close();
		c.close();
	}


	public User get(String id) throws ClassNotFoundException, SQLException {
		Class.forName("com.mysql.jdbc.Driver");
		Connection c = DriverManager.getConnection("jdbc:mysql://localhost/springbook?characterEncoding=UTF-8", "spring",
				"book");
		PreparedStatement ps = c
				.prepareStatement("select * from users where id = ?");
		ps.setString(1, id);

		ResultSet rs = ps.executeQuery();
		rs.next();
		User user = new User();
		user.setId(rs.getString("id"));
		user.setName(rs.getString("name"));
		user.setPassword(rs.getString("password"));

		rs.close();
		ps.close();
		c.close();

		return user;
	}
}
```

딱봐도 안좋아보이는 코드가 완성되었다.

리팩터링이 필요한 코드의 가장 흔한 특징이 중복된 코드가 발견되는 것이다.

위의 add, get 메소드에서 ``DB와 커넥션을 맺는 부분``이 중복되어있는 것이 잘 보인다.



이제부터 ``DB 커넥션을 만드는 관심사``를 여러 방법으로 분리해보면서 간단한 디자인패턴, 객체지향적 설계방법 등을 알아보겠다.



## 메소드 추출을 통한 분리

가장 기본적인 방법이다. 프로그래밍을 한번이라도 해봤다면 생각할 수 있는 방법으로, 중복되는 코드를 하나의 메소드로 만들어 중복 코드 대신 그 메소드를 사용하는 것이다.

getConnection() 메소드를 만들어서 get, add 메소드 내부의 Connection을 만들도록 해주면 된다.

```java
	private Connection getConnection() throws ClassNotFoundException,
			SQLException {
		Class.forName("com.mysql.jdbc.Driver");
		Connection c = DriverManager.getConnection("jdbc:mysql://localhost/springbook?characterEncoding=UTF-8", "spring",
				"book");
		return c;
	}
```

이전 코드였다면, Connection을 Oracle DB에 대한 것으로 바꿔야할 때, Connection을 만드는 모든 코드를 찾아 변경해주어야 한다.

이렇게 메소드 추출을 하게 되면, 추출한 메소드만 변경함으로써 코드가 변경에 용이해진다.



이제 다음 상황을 가정해보자.

이 UserDao가 너무 좋은 코드라고 생각하여, Maven에 Library로 배포하고자 한다.

근데 이 UserDao를 사용하고자 하는 개발자들이 다른 DB에 연결하고자 하거나 다른 종류의 DB로 바꿀 때마다,

배포한 Library를 까서 소스코드를 직접 수정해야하는 불상사가 생긴다. (getConnection 메소드)

라이브러리로 배포하기 너무 부끄럽지 않을까 싶다 ㅋㅋ



## 상속을 통한 분리

상속을 사용하여 UserDao 의  ``DB 커넥션을 만드는 관심사`` 를 클래스 외부로 분리할 수 있다.

1. UserDao 를 abstract class 로 변경
2. getConnection 을 abstract method로 변경
3. UserDao 를 상속받는 구현 클래스에서 getConnection 을 override 하여 자신이 필요한 connection 생성

```java
package springbook.user.dao;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

import springbook.user.domain.User;

public abstract class UserDao {
	public void add(User user) throws ClassNotFoundException, SQLException {
		Connection c = getConnection();

		PreparedStatement ps = c.prepareStatement(
			"insert into users(id, name, password) values(?,?,?)");
		ps.setString(1, user.getId());
		ps.setString(2, user.getName());
		ps.setString(3, user.getPassword());

		ps.executeUpdate();

		ps.close();
		c.close();
	}


	public User get(String id) throws ClassNotFoundException, SQLException {
		Connection c = getConnection();
		PreparedStatement ps = c
				.prepareStatement("select * from users where id = ?");
		ps.setString(1, id);

		ResultSet rs = ps.executeQuery();
		rs.next();
		User user = new User();
		user.setId(rs.getString("id"));
		user.setName(rs.getString("name"));
		user.setPassword(rs.getString("password"));

		rs.close();
		ps.close();
		c.close();

		return user;
	}

	abstract protected Connection getConnection() throws ClassNotFoundException, SQLException ;
}

```

```java
package springbook.user.dao;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;

public class NUserDao extends UserDao {
	protected Connection getConnection() throws ClassNotFoundException,
			SQLException {
		Class.forName("com.mysql.jdbc.Driver");
		Connection c = DriverManager.getConnection(
				"jdbc:mysql://localhost/springbook?characterEncoding=UTF-8",
				"spring", "book");
		return c;
	}
}
```



깔끔해진 것 같다.

이제 UserDao에 대한 직접적인 수정 없이, 상속받아 필요한 부분만을 Override 함으로써 원하는 방법으로 Connection 을 생성할 수 있게 되었다.



그리고 이렇게 슈퍼클래스의 기본적인 로직의 흐름을 만들고, 일부를 추상 메소드나 오버라이딩이 가능한 protected 메소드로 만든 뒤 자식클래스에서 필요에 맞게 구현해서 사용하도록 하는 방법을 디자인 패턴에서는 ``템플릿 메소드 패턴`` 이라고 한다.

또한, 서브클래스에서 구체적인 오브젝트 생성 방법을 결정하게 하는 것을 ``팩토리 메소드 패턴`` 이라고 부르기도 한다.



하지만, 이것도 단점이 존재하는데 그것은 자바에서 상속의 특징 때문이다.

1. 자바는 클래스의 다중상속을 허용하지 않기 때문에, 커넥션을 만드는 방법을 분리하기 위한 목적만으로 상속 구조를 사용한다면, 후에 다른 목적으로 UserDao 에 상속을 적용하기 힘들 것이다.
2. 상속관계로 맺어진 두 클래스는 긴밀한 결합을 허용한다. 슈퍼클래스의 내부의 변경이 생기면 모든 서브클래스를 수정해야 할 수도 있고, 그런 상황을 막기 위해 슈퍼클래스의 수정에 제약을 가할수도 있다.



추가적으로 생각해볼 것이,

수많은 Dao 가 생긴다고 했을 때, 각 Dao 마다 모두 getConnection 을 Override 해야하기 때문에, getConnection 코드가 중복돼서 나타나는 문제가 생길 것이다.



## 클래스의 분리

DB 커넥션을 별도의 클래스로 분리해보면 어떨까?

아래와 같이 SimpleConnectionMaker 라는 클래스를 만들어서 Connection 을 만들게 하고, UserDao 는 그걸 사용하는 것이다.

```java
package springbook.user.dao;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;

public class SimpleConnectionMaker {
	public Connection getConnection() throws ClassNotFoundException,
			SQLException {
		Class.forName("com.mysql.jdbc.Driver");
		Connection c = DriverManager.getConnection(
				"jdbc:mysql://localhost/springbook?characterEncoding=UTF-8", "spring", "book");
		return c;
	}
}
```

```java
package springbook.user.dao;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

import springbook.user.domain.User;

public class UserDao {
	private SimpleConnectionMaker simpleConnectionMaker;
	
	public UserDao() {
		this.simpleConnectionMaker = new SimpleConnectionMaker();
	}

	public void add(User user) throws ClassNotFoundException, SQLException {
		Connection c = this.simpleConnectionMaker.getConnection();

		PreparedStatement ps = c.prepareStatement(
			"insert into users(id, name, password) values(?,?,?)");
		ps.setString(1, user.getId());
		ps.setString(2, user.getName());
		ps.setString(3, user.getPassword());

		ps.executeUpdate();

		ps.close();
		c.close();
	}

	public User get(String id) throws ClassNotFoundException, SQLException {
		Connection c = this.simpleConnectionMaker.getConnection();
		PreparedStatement ps = c
				.prepareStatement("select * from users where id = ?");
		ps.setString(1, id);

		ResultSet rs = ps.executeQuery();
		rs.next();
		User user = new User();
		user.setId(rs.getString("id"));
		user.setName(rs.getString("name"));
		user.setPassword(rs.getString("password"));

		rs.close();
		ps.close();
		c.close();

		return user;
	}
}
```

이제 Connection 을 만드는 코드는 SimpleConnectionMaker 가 담당하기 때문에, 여러 Dao 를 만들어도 코드의 중복문제는 생기지 않을 것이다.

하지만 Connection 방법을 바꾸기 위해서는 아래와 같이 기존의 코드를 바꿔야하는 문제가 다시 발생한다.

1. SimpleConnectionMaker 의 getConnection 메소드를 수정
2. SimpleConnectionMaker 대신 다른 ConnectionMaker 를 만든 후 UserDao 코드를 수정해서 새로 만든 ConnectionMaker 를 사용하도록 변경



## 인터페이스의 도입

그렇다면 클래스를 분리하면서도, 기존 코드 변경 없이 Connection 을 만드는 방법이 없을까?

해결책은 서로 긴밀하게 연결되어 있지 않도록 중간에 추상적인 느슨한 연결고리를 만들어 주는 것이다.

그리고 자바에서 추상화를 위해 제공하는 가장 유용한 도구는 바로 인터페이스이다.



아래와 같이 인터페이스를 도입하여 ConnectionMaker  를 느슨하게 연결해주면 UserDao 에서는 구현체는 신경쓰지 않고

ConnectionMaker 에서 정의한 기능만을 사용하면 된다.

```java
package springbook.user.dao;

import java.sql.Connection;
import java.sql.SQLException;

public interface ConnectionMaker {

	public abstract Connection makeConnection() throws ClassNotFoundException,
			SQLException;

}
```

```java
package springbook.user.dao;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;

public class DConnectionMaker implements ConnectionMaker {
	public Connection makeConnection() throws ClassNotFoundException,
			SQLException {
		Class.forName("com.mysql.jdbc.Driver");
		Connection c = DriverManager.getConnection(
				"jdbc:mysql://localhost/springbook?characterEncoding=UTF-8", "spring", "book");
		return c;
	}
}

```

```java
package springbook.user.dao;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

import springbook.user.domain.User;

public class UserDao {
	private ConnectionMaker connectionMaker;
	
	public UserDao(ConnectionMaker simpleConnectionMaker) {
		this.connectionMaker = simpleConnectionMaker;
	}

	public void add(User user) throws ClassNotFoundException, SQLException {
		Connection c = this.connectionMaker.makeConnection();

		PreparedStatement ps = c.prepareStatement(
			"insert into users(id, name, password) values(?,?,?)");
		ps.setString(1, user.getId());
		ps.setString(2, user.getName());
		ps.setString(3, user.getPassword());

		ps.executeUpdate();

		ps.close();
		c.close();
	}

	public User get(String id) throws ClassNotFoundException, SQLException {
		Connection c = this.connectionMaker.makeConnection();
		PreparedStatement ps = c
				.prepareStatement("select * from users where id = ?");
		ps.setString(1, id);

		ResultSet rs = ps.executeQuery();
		rs.next();
		User user = new User();
		user.setId(rs.getString("id"));
		user.setName(rs.getString("name"));
		user.setPassword(rs.getString("password"));

		rs.close();
		ps.close();
		c.close();

		return user;
	}
}
```

이제 UserDao 는 SimpleConnectionMaker 클래스에 의존하는게 아니라, ConnectionMaker 에 의존하게 되었다.

그리고 무슨 ConnectionMaker 구현체를 사용할 지는 UserDao 를 사용하는 클라이언트 객체에서 UserDao 를 생성할 때 결정해주면 된다.



이제 ConnectionMaker 를 구현하는 Class 를 만들어 UserDao 가 사용하기만 하면,

UserDao 의 코드변경없이 Connection 방법을 바꿀 수 있고, 여러 Dao 를 만들어도 코드의 중복을 막을 수 있게 되었다.



## 원칙과 패턴

지금까지 DAO  코드를 개선해온 결과를 객체지향 기술의 여러 가지 이론을 통해서 설명할 수 있다.

1. 개방 폐쇄 원칙

   5가지 객체지향 설계의 원칙 SOLID 중 하나인 개방 폐쇄 원칙을 지키게 되었다.

   개방 폐쇄 원칙은 '클래스나 모듈은 확장에는 열려 있어야 하고 변경에는 닫혀 있어야 한다' 라는 원칙이다.

   DAO의 최종 결과를 보면, UserDao는 DB연결 방법이라는 기능을 확장하는데는 열려있다.

   그리고, 이러한 기능 확장을 위해서 UserDao 코드는 전혀 고치지 않아도 되므로 변경에는 닫혀있다.

2. 높은 응집도와 낮은 결합도

3. 전략 패턴

   최종 결과물을 디자인 패턴의 시각으로 보면 전략 패턴에 해당한다고 볼 수 있다.

   전략 패턴은 자신의 기능 맥락에서, 필요에 따라 변경이 필요한 알고리즘을 인터페이스를 통해 통째로 외부로 분리시킨다.

   그리고 이 인터페이스의 구현체를 바꿔 사용함으로 써 필요한 알고리즘(전략)을 바꿔 사용할 수 있게 되는 것이다.

   따라서 이러한 디자인 패턴을 전략 패턴이라고 부른다. 

