# 1장/2장 

JPA (Java Persistence API)

- Java ORM 기술에 대한 API 표준 명세


- JPA 객체 그래프 탐색 : 지연로딩 이용
  - SQL 기반으로 하려면 미리 모두 쿼리를 만들어두어야 함.
- 동등성 비교
  - == : 인스턴스가 같은가
  - equals : 값이 같은가
  - JPA는 같은 트랜잭션일 때 같은 객체가 조회되는 것을 보장한다. (==)
- 패러다임의 불일치를 해결하기 위한 노력
- Why JPA
  - 생산성 : CRUD
  - 유지보수 : 엔티티 수정이 SQLs의 수정으로 이어지지 않음
  - 패러다임 불일치 해결
  - 성능 : ex> 캐시
  - 데이터 접근 추상화와 벤더 독립성
  - 표준





# 3장

- 영속성 컨텍스트(persistence context)
  - 비영속 : 영속성 컨텍스트와 전혀 관계가 없는 상태
  - 영속 : 영속성 컨텍스트에 저장된 상태
  - 준영속 : 영속성 컨텍스트에 저장되었따가 분리된 상태
  - 삭제 : 삭제된 상태



**엔티티의 생명주기**

![엔티티의 생명주기](img/pic3-2.png)



* 영속성 컨텍스트의 특징
  * 영속성 컨텍스트와 식별자 값 : @Id로 테이블의 기본 키와 매핑한 값으로 구분
  * 영속성 컨텍스트와 데이터베이스 저장 : 트랜잭션 커밋 순간 데이터베이스에 반영. 이것을 flush
  * 영속성 컨텍스트가 엔티티를 관리할 경우의 장점들
    * 1차 캐시
    * 동일성 보장
    * 트랜잭션을 지원하는 쓰기 지연
    * 변경 감지
    * 지연 로딩



* 엔티티 수정
  * 변경 감지 : JPA는 엔티티를 영속성 컨텍스트에 보관할 때, 최초 상태를 복사해서 저장해두는데 이것을 스냅샷이라고 한다. 그리고 플러시 시점에 스냅샷과 엔티티를 비교해서 변경된 엔티티를 찾는다.
    * *변경 감지는 영속성 컨텍스트가 관리하는 영속 상태의 엔티티에만 적용된다.* (당연한 얘기지만…)
    * JPA의 기본 업데이트 전략은 기본적으로 모든 필드 업데이트이다.
    * 필드가 많거나 저장되는 내용이 너무 크면 수정된 데이터만 사용해서 동적으로 UPDATE SQL을 생성하는 전략을 선택하면 된다. 단 이 때는 하이버네이트 확장 기능을 사용해야 한다.
      * '@org.hibernate.annotations.DynamicUpdate'



* 플러시
  * 플러시는 영속성 컨텍스트의 변경 내용을 데이터베이스에 반영한다.
  * 영속성 컨텍스트를 플러시하는 방법
    1. em.flush()를 직접 호출 : 거의 사용하지 않음
    2. 트랜잭션 커밋 시 플러시가 자동 호출
    3. JPQL 쿼리 실행 시 플러시가 자동 호출 : ex> DB에는 없고 영속성 컨텍스트에만 있는 엔티티 조회 쿼리를 JPQL로 조회할 경우의 대응을 위해
  * 플러시 모드
    1. FlushMode.AUTO : 커밋이나 쿼리를 실행할 때 플러시 (기본값)
    2. FlushMode.COMMIT : 커밋할 때만 플러시



* 준영속(detached)
  * 영속 상태의 엔티티를 준영속 상태로 만드는 방법
    1. em.detach(entity) : 특정 엔티티만 준영속 상태로 전환
    2. em.clear() : 영속성 컨텍스트를 완전히 초기화한다.
    3. em.close() : 영속성 컨텍스트를 종료
  * 준영속 상태의 특징
    * 거의 비영속 상태에 가깝다
    * 식별자 값을 가지고 있다. : 비영속 상태는 식별자 값이 없을 수도 있지만, 준영속 상태는 반드시 식별자 값을 가지고 있다.
    * 지연 로딩을 할 수 없다.



* 병합(merge)
  * 준영속/비영속 상태의 엔티티를 받아서 그 정보로 **새로운 영속 상태의 엔티티를 반환**한다.





# 4장

JPA를 사용하는 데 가장 중요한 일은 엔티티와 테이블을 정확히 매핑하는 것이다. 따라서 매핑 어노테이션을 숙지하고 사용해야 한다.

대표 어노테이션

* 객체와 테이블 매핑 : @Entity, @Table
* 기본 키 매핑 : @Id
* 필드와 컬럼 매핑 : @Column
* 연관관계 매핑 : @ManyToOne, @JoinColumn




### @Entity

- 테이블과 매핑할 클래스의 어노테이션으로 사용
- @Entity 적용 시 주의사항
  - 기본 생성자는 필수다.(파라미터 없는 public or protected 생성자)
  - final 클래스, enum, interface, inner 클래스에는 사용할 수 없다.
  - 저장할 필드에 final을 사용하면 안 된다.



### @Table

- 엔티티와 매핑할 테이블을 지정. 생략하면 매핑한 엔티티 이름을 테이블 이름으로 사용



### 데이터베이스 스키마 자동 생성

- persistence.xml에

  - hibernate.hbm2ddl.auto 속성 지정으로 자동 생성 가능

    - | 옵션          | 설명                                       |
      | ----------- | ---------------------------------------- |
      | create      | 기존 테이블 삭제하고 새로 생성. DROP + CREATE         |
      | create-drop | create 속성에 추가로 애플리케이션 종료할 때 생성한 DDL을 제거. DROP + CREATE + DROP |
      | update      | 데이터베이스 테이블과 엔티티 매핑정보를 비교해서 변경 사항만 수정     |
      | validate    | 데이터베이스 테이블과 엔티티 매핑정보를 비교해서 차이가 있으면 경고를 남기고 애플리케이션을 실행하지 않음 |
      | none        | 자동 생성 기능을 사용하지 않으려면 유효하지 않은 옵션 값을 주면 됨. none은 유효하지 않은 옵션 값 |

  - hibernate.show_sql 속성으로 DDL 출력 가능

  - hibernate.ejb.naming_strategy : 이름 매핑 전략으로 java의 카멜case를 언더스코어 표기법으로 변환해 DDL을 수행



### 기본 키 매핑

- 오라클의 시퀀스 오브젝트나 MySQL의 AUTO_INCREMENT 같은 기능을 사용해서 생성된 값을 기본 키로 사용하려면?

JPA가 제공하는 데이터베이스 기본 키 생성 전략

- 직접 할당 : 기본 키를 애플리케이션에서 직접 할당한다.
- 자동생성 : 대리 키 사용 방식
  - IDENTITY : 기본 키 생성을 데이터베이스에 위임한다. (MySQL, PostgreSQL, MS SQL Server, DB2)
  - SEQUENCE : 데이터베이스 시퀀스를 사용해서 기본 키를 할당한다. (오라클, PostgreSQL, DB2, H2)
  - TABLE : 키 생성 테이블을 사용한다.
    - TABLE 전략은 시퀀스 대신에 테이블을 사용한다는 것만 제외하면 SEQUENCE 전략과 내부 동작방식이 같다.
  - AUTO : 데이터베이스에 따라 IDENTITY나 SEQUENCE를 자동 선택






# 5장 연관관계 매핑 기초

ORM에서 가장 어려운 부분이 바로 객체 연관관계와 테이블 연관관계를 매핑하는 일이다.



## 단방향 연관관계

ex> Member class에 Team을 속성으로 가지고 있고, Team class에는 Member의 속성을 가지고 있지 않는 경우



- 참조를 사용하는 객체의 연관관계는 단방향이다.
  - A -> B
  - 객체를 양방향으로 참조하려면 단방향 연관관계를 2개 만들어야 한다.
- 외래 키를 사용하는 테이블의 연관관계는 양방향이다.



```java
@ManyToOne
@JoinColumn(name='TEAM_ID')
private Team team;
```



### @JoinColumn

- 외래키를 매핑할 때 사용



### @ManyToOne

- 다대일 관계에서 사용



## 연관관계 사용



### 저장

- JPA에서 엔티티를 저장할 때 연관된 모든 엔티티는 영속 상태여야 한다.



### 조회

연관관계가 있는 엔티티를 조회하는 방법은 크게 두 가지

1. 객체 그래프 탐색 (객체 연관관계를 사용한 조회)
2. 객체지향 쿼리 사용 (JQL)



### 수정

- 별도의 update 메소드가 없으므로, 영속 상태의 엔티티 값만 변경해두면 트랜잭션을 커밋할 때 플러시가 일어나면서 변경 감지 기능이 작동



### 연관된 엔티티 삭제

- 연관된 엔티티를 삭제하려면 기존에 있던 연관관계를 먼저 제거하고 삭제를 해야 한다. 그렇지 않으면 키 제약조건으로 인해, 데이터베이스 오류가 발생. (ex> 팀을 삭제하려면, 팀에 속해 있는 멤버의 관계 삭제가 우선되어야 함.)



## 양방향 연관관계

ex> Member class에 Team을 속성으로 가지고 있고, Team class에는 `List<Member>`의 속성을 가지고 있는 경우



### 양방향 연관관계 매핑

```java
@OneToMany(mappedBy = "team")
private List<Member> members = new ArrayList<Member>();
```



## 연관관계의 주인

@OneToMany만 있으면 되지 mappedBy는 왜 필요할까?

엄밀히 이야기하면 객체에는 양방향 연관관계라는 것이 없다. 서로 다른 단방향 연관관계 2개를 애플리케이션 로직으로 잘 묶어서 양방향인 것처럼 보이게 할 뿐이다. 반면 데이터베이스 테이블은 외래 키 하나로 양쪽이 서로 조인할 수 있다.



엔티티를 양방향으로 매핑하면 **회원 -> 팀, 팀 -> 회원** 두 곳에서 서로를 참조한다.

**엔티티를 양방향 연관관계로 설정하면 객체의 참조는 둘인데, 외래 키는 하나다 따라서 두 사이에 차이가 발생한다.** 그렇다면 둘 중 어떤 관계를 사용해서 외래키를 관리해야 할까?

이런 차이로 인해 JPA에서는 **두 객체 연관관계 중 하나를 정해서 테이블의 외래키를 관리해야 하는데 이것을 연관관계의 주인(Owner)**이라 한다.



### 양방향 매핑의 규칙 : 연관관계의 주인

연관관계의 주인만이 데이터베이스 연관관계와 매핑되고 외래 키를 관리(등록, 수정, 삭제)할 수 있다. 반면에 주인이 아닌 쪽은 읽기만 할 수 있다.

어떤 연관관계를 주인으로 정할지는 mappedBy 속성을 사용하면 된다.



### 연관관계의 주인은 외래 키가 있는 곳

ex> Member 테이블에 TEAM_ID가 외래 키로 있다면, 연관관계의 주인은 Member class가 되어야 함.

그리고 주인이 아닌 Team class에서는 mappedBy 속성을 이용해 연관관계의 주인인 team을 주면 된다.

- 연관관계의 주인만 데이터베이스 연관관계와 매핑되고 외래 키를 관리할 수 있다.
- 주인이 아닌 반대편은 읽기만 가능하고 외래 키를 변경하지는 못한다.



## 양방향 연관관계 저장

양방향 연관관계의 저장도 단방향과 동일하다.



```java
team1.getMembers().add(member1) // List에 add
team1.getMembers().add(member2) // List에 add
```

이런 코드가 있어야 할 것 같지만 Team.members는 연관관계의 주인이 아니다. 주인이 아닌 곳에서 입력된 값은 외래 키에 영향을 주지 않는다.



## 양방향 연관관계의 주의점

양방향 연관관계를 설정하고 가장 흔히 하는 실수는 연관관계의 주인에는 값을 입력하지 않고, 주인이 아닌 곳에만 값을 입력하는 것이다.

ex> Member에 team은 입력하지 않고, team의 list에 member만 추가하는 경우



### 순수한 객체까지 고려한 양방향 연관관계

그렇다면 정말 연관관계의 주인에만 값을 저장하고 주인이 아닌 곳에는 값을 저장하지 않아도 될까? 사실은 **객체 관점에서 양쪽 방향에 모두 값을 입력해주는 것이 가장 안전하다**. 양쪽 방향 모두 값을 입력하지 않으면 JPA를 사용하지 않는 순수한 객체 상태에서는 심각한 문제가 발생할 수 있다.



### 연관관계 편의 메소드

양방향 연관관계는 결국 양쪽 다 신경 써야 한다. 각각 호출하다 보면 실수로 둘 중 하나만 호출해서 양방향이 깨질 수 있다.



```java
public class Member{
	private Team team;

	public void setTeam(Team team){
		this.team = team;
		team.getMembers().add(this);
	}
}
```

이렇게 한 번에 양방향 관계를 설정하는 메소드를 연관관계 편의 메소드라 한다.



### 연관관계 편의 메소드 작성 시 주의사항

이전 작성 편의 메소드에는 버그가 있다.

만약 이전 team의 관계가 있었던 경우에는 이전 team의 관계를 끊어주는 작업이 추가로 필요함



```java
public class Member{
	private Team team;

	public void setTeam(Team team){
      	// 기존 팀과 관계를 제거
      	if (this.team != null){
          	this.team.getMembers().remove(this);
      	}
      
		this.team = team;
		team.getMembers().add(this);
	}
}
```

이 코드는 객체에서 서로 다른 단방향 연관관계 2개를 양방향인 것처럼 보이게하려고 얼마나 많은 고민과 수고가 필요한지 보여준다. 반면에 관계형 데이터베이스는 외래 키 하나로 문제를 단순하게 해결한다. 정리하자면 **객체에서 양방향 연관관계를 사용하려면 로직을 견고하게 작성해야 한다**.



## 정리

단방향 매핑과 비교해서 양방향 매핑은 복잡하다. 연관관계의 주인도 정해야 하고, 두 개의 단방향 연관관계를 양방향으로 만들기 위해 로직도 잘 관리해야 한다.

* 단방향 매핑만으로 테이블과 객체의 연관관계 매핑은 이미 완료되었다.
* 단방향을 양방향으로 만들면 반대방향으로 객체 그래프 탐색 기능이 추가된다.
* 양방향 연관관계를 매핑하려면 객체에서 양쪽 방향을 모두 관리해야 한다.



양방향 매핑은 복잡하다. 비지니스 로직의 필요에 따라 다르겠지만 우선 단방향 매핑을 사용하고 반대 방향으로 객체 그래프 탐색 기능(JPQL 쿼리 탐색 포함)이 필요할 때 양방향을 사용하도록 코드를 추가해도 된다.



**연관관계의 주인은 외래 키의 위치와 관련해서 정해야지 비지니스 중요도로 접근하면 안 된다.**



*<<주의>>*

*양방향 매핑 시에는 무한 루프에 빠지지 않게 조심해야 한다. 예를 들어 Member.toString()에서 getTeam()을 호출하고 Team.toString()에서 getMember()를 호출하면 무한 루프에 빠질 수 있다. 이런 문제는 엔티티를 JSON으로 변환할 때 자주 발생하는데 JSON 라이브러리들은 보통 무한루프에 빠지지 않도록 하는 어노테이션이나 기능을 제공한다. 그리고  Lombok이라는 라이브러리를 사용할 때도 자주 발생한다.*



# 6장 다양한 연관관계 매핑

다중성

- 다대일 (@ManyToOne)
- 일대다 (@OneToMany)
- 일대일 (@OneToOne)
- 다대다 (@ManyToMany)

보통 다대일과 일대다 관계를 가장 많이 사용하고 다대다 관계는 실무에서는 거의 사용하지 않는다.

참고로 다중성은 왼쪽을 연관관계의 주인으로 정했다. 예를 들어 다대일 양방향이라 하면 다(N)가 연관관계의 주인이다.



## 다대일

데이터베이스 테이블의 일(1), 다(N) 관계에서 외래 키는 항상 다쪽에 있다. 따라서 객체 양방향 관계에서 연관관계의 주인은 항상 쪽이다.



## 일대다

일대다 관계는 엔티티를 하나 이상 참조할 수 있으므로 자바 컬렉션인 Collection, List, Set, Map 중에 하나를 사용해야 한다.



### 일대다 단방향

하나의 팀은 여러 회원을 참조할 수 있는데 이런 관계를 일대다 관계라 한다. 그리고 팀은 회원들을 참조하지만 반대로 회원은 팀을 참조하지 않으면 둘의 관계는 단방향이다.

*특이점은 데이터베이스의 테이블 연관관계는 다대일과 동일하게 멤버 테이블이 TEAM_ID를 가지는 동일한 구조이다.*

일대다 관계에서 외래 키는 항상 다쪽 테이블에 있다. 하지만 다 쪽인 Member 엔티티에는 외래 키를 매핑할 수 있는 참조 필드가 없다. 대신 반대쪽인 Team 엔티티에만 참조 필드인 members가 있다. 따라서 반대편 테이블의 외래 키를 관리하는 특이한 모습이 나타난다.

```
@OneToMany
@JoinColumn(name = "TEAM_ID") // MEMBER 테이블의 TEAM_ID(FK)
private List<Member> members = new ArrayList<Member>();
```

일대다 단방향 관계를 매핑할 때는 @JoinColumn을 명시해야 한다. 그렇지 않으면 JPA는 연결 테이블을 중간에 두고 연관관계를  관리하는 조인 테이블 전략을 기본으로 사용해서 매핑한다.

일대다 단방향 매핑의 단점

- 매핑한 객체가 관리하는 외래 키가 다른 테이블에 있다는 점
- 다른 테이블에 외래 키가 있으면 연관관계 처리를 위한 UPDATE SQL을 추가로 실행해야 함.

일대다 단방향 매핑보다는 다대일 양방향 매핑을 사용하자

* 상황에 따라 다르겠지만 일대다 단방향 매핑보다는 다대일 양방향 매핑을 권장한다.



### 일대다 양방향

일대다 양방향 매핑을 존재하지 않는다. 대신 다대일 양방향 매핑을 사용해야 한다.

더 정확히 말하자면 양방향 매핑에서 @OneToMany는 연관관계의 주인이 될 수 없다. 왜냐하면 관계형 데이터베이스의 특성상 일대다, 다대일 관계는 항상 다 쪽에 외래 키가 있다.

Member쪽의 team 속성을 읽기 전용으로 만들면 억지로 표현은 가능하겠으나, 이렇게 사용할 이유는 없음



## 일대일

- 일대일 관계는 그 반대도 일대일 관계
- 일대일 관계는 주 테이블이나 대상 테이블 둘 중 어느 곳이나 외래 키를 가질 수 있다.



주 테이블에 외래 키

- 주 객체가 대상 객체를 참조하는 것처럼 주 테이블에 외래 키를 두고 대상 테이블을 참조. 외래 키를 객체 참조와 비슷하게 사용할 수 있어서 객체지향 개발자들이 선호. 
- 장점 : 주 테이블이 외래 키를 가지고 있으므로 주 테이블만 확인해도 대상 테이블과 연관관계가 있는지 알 수 있다.

대상 테이블에 외래 키

- 데이터베이스 개발자들은 보통 대상 테이블에 외래 키를 두는 것을 선호
- 장점 : 테이블 관계를 일대일에서 일대다로 변경할 때 테이블 구조를 그대로 유지할 수 있다.



### 주 테이블에 외래 키



#### 단방향

