'오브젝트: 코드로 이해하는 객체지향 설계' 1주차
분량 : 1장 ~ 5장
기간 : 22.5.7 ~ 22.5.14
[2장 전체 예제 코드](https://github.com/eternity-oop/object/tree/master/chapter02/src/main/java/org/eternity)

## Ch.2 객체지향 프로그래밍

요약

- 의존성의 양면성에 대하여
- 상속과 합성
- 추상화

### 객체지향은 객체를 지향하는 것이다.

객체지향 프로그래밍은 클래스를 결정한 후 어떤 속성과 메서드가 필요한지를 고민하는 것이 아니다.
진정한 객체지향 패러다임으로의 전환은 클래스가 아닌 **객체에 초점**을 맞출 때에만 얻을 수 있다.

- 어떤 클래스가 필요한지를 고민하기 전에 어떤 객체들이 필요한지 고민하라.
  - _클래스는 공통적인 상태와 행동을 공유하는 객체들을 추상화한 것이다._
- 객체를 독립적인 존재가 아니라 기능을 구현하기 위해 협력하는 공동체의 일원으로 보아라.
  - _객체는 다른 객체에게 도움을 주거나 의존하면서 살아가는 협력적인 존재이다._

> 훌륭한 협력이 훌륭한 객체를 낳고 훌륭한 객체가 훌륭한 클래스를 낳는다.

<br/>

### 클래스 구현 시 중요성

클래스를 구현할 때에 가장 중요한 것은 클래스의 **경계를 구분 짓는 것**이다.
'경계'란 클래스에서 공개하는 내용과 감추는 내용의 경계를 말한다.
ex) private, public

> 경계의 명확성이 객체의 자율성을 보장한다.
> 프로그래머에게 구현의 자유를 제공한다.

<br/>

### 메시지와 메서드의 차이

- 메시지 : 객체가 다른 객체에게 행동을 요청(request)하거나 응답(response)하는 경우
- 메서드 : 수신된 메시지를 처리하기 위한 자신만의 방법

A클래스에서 B클래스를 의존할 때 A클래스는 B클래스가 특정 메시지에 대한 응답이 올 것이라는 것만 예측할 수 있어야 한다. B클래스의 메서드 내용까지 알 필요가 없음.

> 메시지와 메서드의 구분에서부터 다형성(polymorphism)의 개념이 출발한다.

<br/>

### TEMPLATE METHOD 패턴

```java
public abstract class DiscountPolicy {
    private List<DiscountCondition> conditions = new ArrayList<>();

    public DiscountPolicy(DiscountCondition ... conditions) {
        this.conditions = Arrays.asList(conditions);
    }

    public Money calculateDiscountAmount(Screening screening) {
        for(DiscountCondition each : conditions) {
            if (each.isSatisfiedBy(screening)) {
                return getDiscountAmount(screening);
            }
        }

        return Money.ZERO;
    }

    abstract protected Money getDiscountAmount(Screening Screening);
}
```

할인이 적용될 수 있는가에 대한 여부는 추상 메서드인 calculateDiscountAmount를 통해 확인한 후, 할인 금액에 대한 책임은 자식 클래스에게 넘긴다.

> 이처럼 부모 클래스에 기본적인 알고리즘의 흐름(calculateDiscountAmount)을 구현하고 중간에 필요한 처리(getDiscountAmount)를 자식 클래스에게 위임하는 디자인 패턴을 'TEMPLATE METHOD 패턴'이라고 한다.

#### 궁금했던 점

왜 Interface가 아닌 abstract로 구현했을까 ?

Interface와 abstract의 차이점

- abstract의 목적 : 추상 클래스를 상속받아서 기능을 이용하고, 확장
- Interface의 목적 : 함수의 구현을 강제화함, 객체의 같은 동작을 보장할 수 있음

> 추상(abstract) 클래스로 정의한 이유
> AmountDiscountPolicy와 PercentDiscountPolicy의 cacluateDiscountAmount는 완전히 동일하고, getDiscountAount만 다르기 때문

<br/>

### 의존성의 양면성

코드의 의존성과 실행 시점의 의존성이 서로 다를 수 있다.

```java
public class Movie {
    private String title;
    private Duration runningTime;
    private Money fee;
    private DiscountPolicy discountPolicy;

    public Movie(String title, Duration runningTime, Money fee, DiscountPolicy discountPolicy) {
        this.title = title;
        this.runningTime = runningTime;
        this.fee = fee;
        this.discountPolicy = discountPolicy;
    }

    public Money getFee() {
        return fee;
    }

    public Money calculateMovieFee(Screening screening) {
        return fee.minus(discountPolicy.calculateDiscountAmount(screening));
    }
}
```

Movie 클래스는 코드 상에선 DiscountPolicy를 의존하고 있지만, 실행 시점에서는 할인된 금액을 계산해야 하므로 DiscountPolicy의 자식 클래스들에 의존해야 한다.
실행 시점에 의존하고 있는 객체의 정확한 타입을 알기 위해서는 의존성을 연결하는 부분을 찾아봐야 한다.
이 부분에서는 Movie 인스턴스를 생성하는 부분이다.

> 코드의 의존성과 실행 시점의 의존성이 다르면 다를수록 코드는 더 유연해지고 확장 가능하다. 그러나, 그럴수록 코드는 더 이해하기 어려워진다. <br/>
> **무조건 유연한 설계도, 무조건 읽기 쉬운 코드도 정답이 아니다.**

<br/>

### 다형성

다형성을 구현하는 방법에는 메서드를 컴파일 시점이 아닌 **실행 시점**에 결정한다는 공통점이 있다.

- 지연 바인딩(lazy binding) or 동적 바인딩(dynamic binding) : 메시지와 메서드를 실행 시점에 바인딩하는 것
- 초기 바인딩(early binding) or 정적 바인딩(static binding) : 컴파일 시점에 실행될 함수나 프로시저를 결정하는 것

<br/>

### 상속

상속의 가장 큰 목적은 ***인터페이스의 재사용***이다.
상속을 사용하면 동일한 인터페이스를 공유하는 클래스들을 하나의 타입 계층으로 묶을 수 있다.

상속의 단점

1. 캡슐화를 위반한다.
   자식 클래스가 부모 클래스의 내부 구조를 잘 알고 있어야 한다.
   -> 부모 클래스의 구현이 자식 클래스에게 노출되기 때문에 캡슐화가 약화된다.
   -> 캡슐화의 약화는 자식 클래스가 부모 클래스에 강하게 결합되도록 만들어 부모 클래스를 변경할 때 자식 클래스도 함께 변경될 확률을 높인다.

2. 설계를 유연하지 못하게 만든다.
   실행 시점에 객체의 종류를 변경하는 것이 불가능하다.

<br/>

### 코드 재사용

코드 재사용을 위해서는 상속보다는 합성이 더 좋은 방법이다.
다른 객체의 인스턴스를 자신의 인스턴스 변수로 포함해서 재사용하는 방법을 말한다.
상속은 클래스를 통해 강하게 결합되는 데 비해 합성은 메시지를 통해 느슨하게 결합된다.

합성은 상속의 문제점을 해결한다.

- 인터페이스에 정의된 메시지를 통해서만 재사용이 가능하기 때문에 구현을 효과적으로 캡슐화할 수 있다.
- 실행 시점에 객체의 종류를 변경할 수 있다.

대부분의 설계에서는 상속과 합성을 조합해서 사용해야 한다.

> 코드의 재사용을 위해선 합성, 인터페이스를 재사용하는 경우에는 상속을 이용해야 한다.

<br/>

### 추상화

"영화 예매 요금은 최대 하나의 '할인 정책'과 다수의 '할인 조건'을 이용해 계산할 수 있다"의 문장이 "영화의 예매 요금은 '금액 할인 정책'과 '두 개의 순서 조건, 한 개의 기간 조건'을 이용해서 계산할 수 있다"라는 문장을 포괄할 수 있다.
세부사항에 억눌리지 않고 상위 개념만으로도 도메인의 중요한 개념을 설명할 수 있게 한다.

> 추상화의 장점 : 세부적인 내용을 무시한 채 상위 정책을 쉽고 간단하게 표현할 수 있다.

### 1장에서 설계한 코드 수정

```java
// Office abstract class 선언
public abstract class Office {
    Ticket getTicket();

	public void minusAmount(Long amount) {
        this.amount -= amount;
    }

    public void plusAmount(Long amount) {
        this.amount += amount;
    }

    // abstract protected 추가 필요 메서드
}

// TicketOffice 구현
public class TicketOffice implements Office{
    private Long amount;
    private List<Ticket> tickets = new ArrayList<>();

    //추가 필요 메서드 오버라이딩
}

// TicketSeller
public class TicketSeller {
    private Office office;

    public TicketSeller(Office office) {
        this.office = office;
    }

    public void sellTo(Audience audience) {
        office.sellTicketTo(audience);
    }
}
```

단지 Interface만으로 구현해놨었지만, minusAmount, plusAmount는 동일한 역할을 할 것이므로 추상 메서드로 구현했다. 더 추가될 기능을 생각하지 못했지만, 더 필요한 기능이 있다면 위에 적어논 곳에 추가하면 될 것이다.

---

> #### 모든 코드에는 합당한 이유가 있어야 한다. 비록 아주 사소한 결정이더라도 트레이드오프를 통해 얻어진 결론과 그렇지 않은 결론 사이의 차이는 크다. 고민하고 트레이드오프하라.
