### '오브젝트: 코드로 이해하는 객체지향 설계' 1주차

분량 : 1장 ~ 5장
기간 : 22.5.7 ~ 22.5.14
![](https://velog.velcdn.com/images/manx/post/c79fa30a-c70d-47ba-8342-e0550fa082ab/image.png)

## Ch.1 객체, 설계

Ch.1의 핵심 내용을 요약하면 다음 두 가지 인 듯 하다.

- 객체지향이란 무엇인가
- 캡슐화의 중요성

### 프로그래밍 패러다임

프로그래밍 패러다임은 개발자 공동체가 동일한 프로그래밍 스타일과 모델을 공유할 수 있게 함으로써 **불필요한 부분에 대한 의견 충돌을 방지**한다.
Ex) MVC 패턴 이론이 적립되어 있어, 내가 왜 이런 스타일로 코딩했는지에 대한 의견 충돌이 방지된다.

### 모듈이 가져야 하는 세 가지 기능

- 제대로 실행돼야 한다.
- 변경이 용이해야 한다.
- 이해하기 쉬워야 한다.

### 변경에 취약한 설계

- 하나의 클래스나 메서드에서 너무 많은 세부사항을 다루면 안된다. (의존 클래스들에 대한 모든 정보)
- 지나치게 세부적인 사실에 의존하면 안된다. (관람객은 가방을 들고 있다, 판매원이 매표소에서만 티켓을 판매한다.)
- 객체 사이의 의존성은 최소한으로 유지해야한다.
- 결합도가 높다 : 객체 사이의 의존성이 과하다.

=> _**변경하기 힘들다.**_

### 인터페이스와 구현

객체를 인터페이스와 구현으로 나누고 인터페이스만을 공개하는 것은 객체 사이의 결합도를 낮추고 변경하기 쉬운 코드를 작성하기 위해 따라야 하는 가장 기본적인 설계 원칙.

### 캡슐화

객체 내부의 상태를 **캡슐화**하고 객체 간에 오직 **메시지를 통해서만 상호작용**하도록 만드는 것이 핵심!
즉, 하나의 클래스에서 다른 클래스의 동작까지 몰라도 된다. (단지 메시지에 응답할 수 있는 것만 알면 됨)
밀접하게 연관된 작업만을 수행하고 연관성 없는 작업은 다른 객체에게 위임하는 객체를 가리켜 응집도가 높다고 말한다.
자율적인 객체 : 결합도 ↓, 응집도 ↑

### 절차지향과 객체지향

- 절차지향 : 모든 처리가 하나의 클래스 안에 위치하고 나머지 클래스는 단지 데이터의 역할만 수행
- 객체지향 : 자신의 데이터를 스스로 처리하도록 (데이터와 프로세스가 동일한 모듈 내부에 위치하도록)

객체에 '어떤 데이터를 넣을지' 보다 '어떤 책임을 할당할 것인가'가 중요!

#### 설계를 어렵게 만드는 것 : 의존성

해결방법 : 객체 사이의 결합도를 낮추는 것
=> 캡슐화
=> 자율성을 높이고 응집도 높은 객체들의 공동체를 창조할 수 있게
=> 최소한의 의존성만 남기는 것이 핵심

때로 한 클래스의 결합도를 낮췄지만, 다른 클래스의 결합도가 높아지는 경우가 발생한다.
이 때 ***어떤 것을 더 우선해야하는가를 결정***하는 것이 중요하다.

훌륭한 객체지향설계란 **모든 객체들이 자율적으로 행동**하는 설계이다.

---

### TicketOffice를 다르게 하고 싶은 경우

책에서 'TicketSeller가 매표소가 아니라 은행에 돈을 보관하도록 만들고 싶을 때 TicketSeller 내부만 변경하면 된다.'라고 해 스스로 단계 별로 개선해보도록 하겠다.

_**원래의 코드**_

```java
public class TicketOffice {
    private Long amount;
    private List<Ticket> tickets = new ArrayList<>();

    public TicketOffice(Long amount, Ticket... tickets) {
        this.amount = amount;
        this.tickets.addAll(Arrays.asList(tickets));
    }

    public Ticket getTicket() {
        return tickets.remove(0);
    }

    public void minusAmount(Long amount) {
        this.amount -= amount;
    }

    public void plusAmount(Long amount) {
        this.amount += amount;
    }
}


public class TicketSeller {
    private TicketOffice ticketOffice;

    public TicketSeller(TicketOffice ticketOffice) {
        this.ticketOffice = ticketOffice;
    }

    public void sellTo(Audience audience) {
        ticketOffice.plusAmount(audience.buy(ticketOffice.getTicket()));
    }
}
```

위 설계에서 TicketSeller가 은행에 돈을 보관하고 싶다면

```java
private TicketOffice ticketOffice;
```

부분 및 ticketOffice가 사용된 모든 부분을 변경해야한다.
이는 SOLID 원칙 중 DIP, OCP (개방 페쇄의 원칙)을 위반했다고 할 수 있다.

단계별로 해결해 보자. (생성자 부분도 따로 구현해야 하지만 구현하지 않았다.)

### 1. 인터페이스와 구현 분리

```java
// Office Interface 선언
public interface Office {
    Ticket getTicket();

    void minusAmount(Long amount);

    void plusAmount(Long amount);
}

// TicketOffice 구현
public class TicketOffice implements Office{
    private Long amount;
    private List<Ticket> tickets = new ArrayList<>();

    public Ticket getTicket() {
        return tickets.remove(0);
    }

    public void minusAmount(Long amount) {
        this.amount -= amount;
    }

    public void plusAmount(Long amount) {
        this.amount += amount;
    }
}

// TicketSeller
public class TicketSeller {
    private Office office = new TicketOffice();

    public void sellTo(Audience audience) {
        Office.plusAmount(audience.buy(Office.getTicket()));
    }
}
```

다음과 같이 인터페이스와 구현을 분리하면, 수정 시 new TicketOffice 부분만 변경할 수 있다.
그러나 변경될 시 클라이언트의 코드를 수정하는 것은 OCP의 위배되므로 다른 무언가가 필요하다.

### DI를 이용한 설계

```java
// Office Interface 선언
public interface Office {
    Ticket getTicket();

    void minusAmount(Long amount);

    void plusAmount(Long amount);
}

// Ticket Office 구현
public class TicketOffice implements Office{
    private Long amount;
    private List<Ticket> tickets = new ArrayList<>();


    public Ticket getTicket() {
        return tickets.remove(0);
    }

    public void minusAmount(Long amount) {
        this.amount -= amount;
    }

    public void plusAmount(Long amount) {
        this.amount += amount;
    }
}

// 돈을 어디에 저장할지 극장 대표가 결정
public class TheaterCeo {
	public Office office() {
    	return new TicketOffice();
    }
}

// TheaterCeo로 부터 생성된 Office 주입
public class TicketSeller {
    TheaterCeo theaterCeo = new TheaterCeo();
    Office office = theaterCeo.office();

    public void sellTo(Audience audience) {
        office.plusAmount(audience.buy(office.getTicket()));
    }
}
```

다음과 같이 설계해서 만약 BankOffice로 바꾸고 싶다면, Office를 구현하는 BankOffice 클래스를 만든 뒤 TicketSeller 클래스가 자체가 아닌 TheaterCeo 코드만 수정하면 된다.
간략하게 알고 있는 의존성 주입(Dependency Injection)을 적용해 보았다.

---

### 결론 : 객체지향적 설계란 ?

> 오늘 요구하는 기능을 온전히 수행하면서 내일의 변경을 매끄럽게 수용할 수 있는 설계
