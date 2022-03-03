---
title: "Jpa Mappedsuperclass"
date: 2022-03-03T20:14:01+09:00
categories:
    - JPA
tags:
    - JPA
    - Spring
url: /2022/03/03/jpa-mappedsuperclass
featuredImage: /images/jpa-logo.png
images: 
    - /images/jpa-logo.png
published: true
---
# @MappedSuperclass
엔티티를 구현하다 보면 보통 공통적으로 사용하는 속성들이 존재한다.  
가장 많이 나오는 게 등록일, 수정일, 등록자, 수정자 등이 이에 해당한다.  
이렇듯 공통 매핑 정보를 부모 클래스에서 상속받아 사용할 때 쓰인다.  
부모 클래스는 테이블과 매핑하지 않고 자식 클래스에게 매핑 정보만 제공하는 것이다.(자식 클래스에서는 상속한 필드들도 칼럼으로 인식한다)

```java
@MappedSuperclass
@Getter
public abstract class BaseEntity {
    private LocalDateTime createdAt;
    private LocalDateTime updatedAt;
}
```

```java
@Entity
@Builder
@Getter
public class Member extends BaseEntity {
    ...
}
```

@MappedSuperclass를 선언한 클래스는 엔티티가 아니다.  
그리고 직접 생성해서 사용할 일이 없으므로 추상 클래스로 만드는 것을 권장한다.