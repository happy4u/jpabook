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



