---
title:  "[Effective Java] Item1 - 생성자 대신 정적 팩토리 메서드를 고려하라"
excerpt: "Effective Java Item1 생성자 대신 정적 팩토리 메서드를 고려하라를 읽고 정리한 포스팅입니다"

layout: single
author_profile: true
read_time: true
comments: true
share: true
related: true

categories:
  - Java
tags:
  - [Java, Effective Java, 생성자, 정적 팩토리 메서드]

toc: true
toc_sticky: true
toc_label: Contents
last_modified_at: 2021-10-17
---
## 생성자 vs 정적 팩토리 메서드
---
인스턴스를 생성하는 방법에는 **public 생성자**를 사용하는 방법과 **정적 팩터리 메서드**를 사용하는 방법이 있습니다.

<br>

**정적 팩터리 메서드**란 클래스의 인스턴스를 반환하는 단순한 정적 메서드를 의미합니다.

<br>

✔ public 생성자 사용
```java
public class Boolean{
    
    private final boolean value;

    public Boolean(boolean value) {
        this.value = value;
    }
}
```

<br>

✔ 정적 팩터리 메서드 사용
```java
public class Boolean{
    
    public static final Boolean TRUE = new Boolean(true);

    public static final Boolean FALSE = new Boolean(false);

    public static Boolean valueOf(boolean b) {
        return (b ? TRUE : FALSE);
    }
}
```

<br>


## 정적 팩토리 메서드 사용 시 장점
---

### 👍 장점1. 이름을 가질 수 있습니다 ###

<br>

생성자에 넘기는 매개변수와 생성자 자체만으로는 반환될 객체의 특성을 제대로 설명하지 못합니다.

이와 달리 정적 팩터리 메서드는 **이름을 가질 수 있기 때문에, 이름만 잘 지으면 반환될 객체의 특성을 쉽게 표현할 수 있습니다.**

<br>

### 👍 장점2. 호출될 때마다 인스턴스를 새로 생성하지 않아도 됩니다 ###

<br>

이러한 특성 덕분에, 불변 클래스는 미리 만들어둔 인스턴스를 재활용하는 식으로 객체 생성을 피할 수 있습니다.

언제, 어느 인스턴스를 살아 있게 할지 통제함으로써,
1) 싱글턴 : 객체를 오직 1개로만 제한
2) 인스턴스화 불가 : 객체를 생성하지 못하고 정적으로만 사용 

를 보장할 수 있습니다.

*인스턴스 통제는 객체가 없다면 만들고, 있다는 그 객체를 공유하는 **플라이웨이트 패턴(Flyweight Pattern)** 의 근간이 됩니다.*


<br>


### 👍 장점3. 반환 타입의 하위 타입 객체를 반환할 수 있습니다 ###

<br>

반환할 객체의 클래스는 리턴 타입의 상속을 받은 모든 클래스가 가능합니다.

인터페이스로 정적 팩터리 메소드를 구현한다면, 다형성을 가진 객체를 반환할 수 있습니다.

<br>


### 👍 장점4. 입력 매개변수에 따라 매번 다른 클래스의 객체를 반환할 수 있습니다 ###

<br>

반환할 객체의 클래스가 리턴 타입의 하위 타입이기만 하면 됩니다. 

클라이언트는 클래스들의 존재를 알지 못합니다.

만약 기존 버전에서 반환하는 타입을 변경하고자 한다면, 다음 버전에서 다른 타입을 반환해 줄 수 있습니다.


<br>


### 👍 장점5. 정적 팩터리 메서드를 작성하는 시점에는 반환할 객체의 클래스가 존재하지 않아도 됩니다 ###

<br>

하위 타입의 클래스가 존재하지 않아도, 나중에 생성할 클래스가 기존 인터페이스/클래스의 하위 타입이라면 언제든지 사용이 가능합니다.

정적 팩터리 메서드의 변경 없이 구현체를 바꿔 끼울 수 있습니다.

<br>

✔ Student는 인터페이스이고, 구현체가 없어도 아래와 같이 메서드 작성이 가능합니다.
```java
public class Classroom{
    public static List<Student> getStudetList(){
        return new ArrayList<>();
    }
}

```

<br>

### 📢 참고 ###
이러한 유연함은 **서비스 제공자 프레임워크(Service Provider Framework)** 를 만드는 근간이 됩니다.

<br>

**서비스 제공자 프레임워크 ?**

> 다양한 서비스 제공자들이 하나의 서비스를 구성하는 시스템입니다.
클라이언트는 세부적인 구현 내용을 몰라도 서비스를 이용할 수 있습니다.
대표적으로 JDBC는 mysql, oracle 등의 서비스 제공자들이 JDBC라는 하나의 서비스를 구성합니다.



<br>

**서비스 제공자 프레임워크 구성**
1) 서비스 인터페이스 - 구현체의 동작을 정의
2) 제공자 등록 API - 제공자가 구현체를 등록할 때 사용
3) 서비스 접근 API - 클라이언트가 서비스의 인스턴스를 얻을 때 사용
  - 클라이언트가 서비스 접근 API 사용 시 원하는 구현체 조건을 명시할 수 있다
  - 이 서비스 접근 API가 바로 서비스 제공자 프레임워크의 근간이라고 한 **유연한 정적 팩터리**의 실체
4) 서비스 제공자 인터페이스(Option) - 서비스 인터페이스의 인스턴스를 생성하는 팩터리 객체를 설명  


<br>

JDBC에서 서비스 제공자 프레임워크에서의 제공자는 서비스 구현체입니다.

프레임워크에서 이 서비스 구현체들을 클라이언트에 제공하는 것을 통제합니다.

이로써 클라이언트와 구현체를 분리해줍니다.

클라이언트는 Connection 인터페이스를 구현한 라이브러리 등을 통해 JDBC라는 서비스 규칙을 지킨 다양한 DB를 사용할 수 있게 됩니다 😀

<br>

~~~
서비스 인터페이스 - Connection
제공자 등록 API - DriveManager.registerDrive()
서비스 접근 API - DriverManager.getConnection()
서비스 제공자 인터페이스 - Driver
~~~


<br>


## 정적 팩토리 메서드 사용 시 단점
---

### 👎 단점1. 정적 팩터리 메서드만 제공하면 하위 클래스를 만들 수 없습니다 ###

<br>

상속을 하려면 public이나 protected 생성자가 필요하기 때문입니다.

하지만 상속보다 컴포지션을 사용하도록 유도하고, 불변 타입으로 만들려면 해당 제약을 지켜야 한다는 경우에서는 장점으로 작용할 수 있습니다.

<br>

### 👎 단점2. 정적 팩터리 메서드는 프로그래머가 찾기 어렵습니다 ###

<br>

API 설명에 명확하게 드러나지 않기 때문입니다.

그래서 널리 알려진 규약으로 이름을 짓는 식으로 문제를 완화할 수 있습니다.

<br>

|명명 방식|설명|예시|
|---|---|---|
|from|매개 변수를 받아 해당 타입의 인스턴스를 반환하는 형변환 메소드|Date d = Date.from(instant);
|of|여러 매개변수를 받아 적합한 타입의 인스턴스를 반환하는 집계 메서드|Set\<Rank> faceCards = Enumset.of(JACK, QUEEN, KING);
|valueOf|from과 of의 더 자세한 버전|BigInteger prime = BigInteger.valueOf(Integer.MAX_VALUE);
|instance / getInstance| 매개변수로 명시한 인스턴스를 반환하지만, 항상 같은 인스턴스임을 보장하진 않음| StackWalker luke = StackWalker.getInstance(options);
|create / newInstance| instance / getInstance와 동일하지만, 매번 새로운 인스턴스를 생성해 반환함을 보장|Object newArray = Array.newInstance(classObject, arrayLen);
|get*Type*|getInstance와 같으나, 생성할 클래스가 아닌 다른 클래스의 팩터리 메서드를 정의할 때 사용. *Type*은 팩터리 메소드가 반환할 객체의 타입이다.|FileStore fs = Files.getFileStore(path);
|new*Type*|newInstance와 같으나, 생성할 클래스가 아닌 다른 클래스의 팩터리 메서드를 정의할 때 사용. *Type*은 팩터리 메소드가 반환할 객체의 타입이다.|BufferedReader br = Files.getBufferedReader(path);
|*type*|get*Type*과 new*Type*의 간결한 버전|List\<Complaint> litany = Collections.list(legacyLitany);

<br>