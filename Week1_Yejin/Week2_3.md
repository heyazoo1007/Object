## 8장, 의존성

다른 객체와 협력하기위해 알아야하는 것

1. 해당 객체 존재 여부
2. 객체가 수신할 수 있는 메시지 정보

### 의존성

- 변경에 의한 영향의 전파 가능성을 암시
- 의존하는 대상이 변경될 때 함께 변경될 가능성
- 단방향으로 작용

의존성이 실행시점과 구현 시점에서 가지는 의미

- 실행시점: 의존하는 객체가 정상적으로 동작하기 위해서는 실행 시에 의존 대상 객체가 반드시 존재해야 한다
- 구현 시점: 의존 대상 객체가 변경될 경우 의존하는 객체도 함께 변경된다(의존되는 것이 변경 → 의존하는 것도 변경)

Q: 의존하는 관계가 협력인데, 의존성과 변경?

협력이라는 문맥에서 적절한 책임을 가지고 있는 객체가 있다면 다른 객체와 협력하기 위해 적절한

의존성을 가지고 있어야 한다. 그런데 여기서 의존성이 너무 커버리면 (A→B 상황에서) B의 변경에 A가

변경될 가능성이 높다. 그래서 객체가 다른 객체에 대해서 알아야하지만, 이는 내부 구현을 아는 것이 아니라 퍼블릭 인터페이스를 앎으로써 적절한 의존성을 가지는 것이 중요하다.

→ 협력을 위해 의존성은 필요하지만, 변경에 최대한 적은 영향을 받기 위해 적절한 의존성(다른 객체에 대해서 최소한으로 알아야함)이 필요하다.

### 의존성 전이(C→ A →B)

- 객체 A가 의존하는 객체 B가 변경될 경우, A에 의존하는 C도 변경될 가능성이 있다
- 위 상황에서 A의 캡슐화가 잘 되어 있다면 C까지 변경 안해도 됨
- 반대로 캡슐화가 잘되어 있지 않다면 C까지 변경될 가능성 있음

직접 의존성

B가 변경돼서 A가 변경된 경우

간접 의존성

B가 변경됐는데 C까지 변경된 경우

---

### 런타임 의존성, 컴파일타임 의존성

컴파일 타임

- 작성된 코드를 컴파일하는 시점
- 코드 그자체 (동적 타입 언어는 컴파일 시점이 존재하지 않음)

런타임 의존성의 주제: 객체 사이의 의존성

컴파일타임 의존성의 주제: 클래스 사이의 의존성

→ 런타임 의존성과 컴파일타임 의존성의 차이가 클 수록 변경에 유용한 설계가 된다.

컴파일 시점에서는 어떤 객체는 의존하는 객체에 대해서 구체적인 정보를 모르지만,

실행 시점에는 협력하는 상황을 생각하면 된다.

→ 클래스는 자신과 협력할 객체의 구체적인 클래스에 대해 알아서는 안된다

### 컨텍스트(문맥) 독립성

클래스가 사용된 특정 문맥에 대해 최소한의 가정만으로 이루어진 것.

실행 컨텍스트에 대해서 아는 것이 적으면 적을수록 코드의 재사용 방면에서 좋다.

(예제에서는 Movie가 DiscountPolicy만 알고, Percent,Amount는 모르지만

실행시점에는 Percent 또는 Amount와 협력하는 것처럼)

`정리`: 클래스가 협력하는 객체에 대해 최소한으로 알아야 실행시점 의존성 ≠ 컴파일시점 의존성을 만들어서 의존성을 최소화할 수 있다.

Q: 실행 컨텍스트에 대해 아는게 적은데 어떻게 런타임에 실행 컨텍스트에 적절한 객체들과 협력할 수 있나?

→ ***의존성 해결방법***을 사용하면 된다

### 의존성 해결

- 객체 생성*시점에* ***생성자***를 통해
- 객체 생성 *후  **setter 메서드***(수정자)
- 메서드 실행 시 ***인자***를 이용
- 이후에 의존성 주입과 관련됨

setter 메서드를 이용해 의존성 해결

장점: 실행 시점에 의존 대상을 변경할 수 있기 때문에 설계를 좀 더 유연하게 만들 수 있다

단점: 진행시점이 객체 생성 이후기 때문에 객체 생성, 의존성 결정까지는 객체가 불안정하다

메서드 실행 시 인자 이용

협력이 항상 필요하지 않고, 특정 상황에서만 필요할 때 이용한다

메서드가 실행될 때마다 의존성 변경: 메서드 인자 방법

메서드가 실행될 때마다 의존성 변경 x: 생성자, 수정자 방법

### 의존성 자체가 나쁜 것이 아니라, 의존성의 정도가 높으면 문제가 된다

바람직한 의존성을 가지게 하는 것이 목표

바람직한 의존성은 해당 의존성이 다양한 환경에서 재사용할 수 있는 경우를 말한다

컨텍스트에 독립적인 의존성은 다양한 환경에서 재사용이 가능 → 바람직한 의존성→ 느슨한/약한 결합도

특정 컨텍스트에 강하게 결합된 의존성 → 바람직하지 않은 의존성 → 단단한/강한 결합도

---
### 결합도

결합도의 정도: 한요소가 자신이 의존하고 있는 다른 요소에 대해 *알고있는 정보의 양*으로 결정된다

의존하는 대상을 추상화할수록(대상에 대해 아는 것이 적을수록) 결합도는 낮아진다

결합도가 낮아지는 순서

가장 높음: **구체** 클래스 의존성

중간: **추상** 클래스 의존성(구체 클래스 - (내부 구현, 자식 클래스 정보))

낮음: **인터페이스** 의존성( 추상 클래스 - (상속 계층 정보))

### 명시적 의존성, 숨겨진 의존성

명시적 의존성: 의존성이 퍼블릭 인터페이스에 노출되는 것(생성자 파라미터로 전달받는 방법)

숨겨진 의존성: 의존성이 퍼블릭 인터페이스에 노출되는 것 (생성자 내부에서 인스턴스 직접 생성하는 방법)

→ 의존성은 `명시적`으로 표현되어야 한다

### 정리

- 사용(서버) 생성(클라이언트)의 책임을 분리하고,
- 의존성을 생성자에 명시적으로 드러내고,
- 추상 클래스에 의존하게 함으로써 유연한 설계를 만들 수 있다.



되도록이면 new로 인스턴스 생성은 지양하기

- new 연산자를 사용하기 위해 구체 클래스의 이름을 직접 기술해야함 → 구체 클래스 관련 의존성이 증가     → 결합도가 낮아진다
- 클래스 이름 + 어떤 파라미터를 이용해야하는지도 알아야한다 → 알아야할 정보 증가 → 결합도 감소