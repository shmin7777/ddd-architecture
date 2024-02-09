# DIP(Dependency Inversion Principle)
<img width="455" alt="스크린샷 2024-02-09 오후 3 34 17" src="https://github.com/shmin7777/ddd-architecture/assets/67637716/11ea9b08-b262-41de-9e83-56a1a494a663">  

가격 할인 계산을 하려면 왼쪽과 같이 고객 정보를 구해야 하고, 구한 정보를 이용해서 룰을 실행 해야한다.  
고수준 모듈은 의미 있는 단일 기능을 제공하는 모듈로 CalculateDiscountService는 가격 할인 계산이라는 기능을 구현한다.  
고수준 모듈의 기능을 구현하려면 여러 하위 기능이 필요하다.  
저수준 모듈은 하위 기능을 실제로 구현한 것이다.  
<br>  
고수준 모듈이 제대로 동작하려면 저수준 모듈을 사용해야한다.  
그러나 구현 변경과 테스트가 어렵다는 문제가 발생한다.  

DIP는 이 문제를 해결하기 위해 <b>저수준 모듈이 고수준 모듈에 의존</b>하도록 바꾼다.  
방법은 추상화한 인터페이스에있다.  
<img width="369" alt="스크린샷 2024-02-09 오후 3 42 26" src="https://github.com/shmin7777/ddd-architecture/assets/67637716/ee434da4-a289-4818-ae56-47016de51249">  

```
interface RuleDiscounter {
  fun applyRules(): Money
}

class CalculateDiscountService(
  private val ruleDiscounter: RuleDiscounter
){
  fun calculate(){
    // 전처리
    ruleDiscounter.applyRules()
    // 후처리
  }
}

class DroolsRuleDiscounter : RuleDiscounter{
 // 구현
}
```  

위 구조를 보면 CalculateDiscountService는 더 이상 구현 기술인 Drools에 의존하지 않는다.  
=> 스프링에선 DI, IOC로 지원.  

테스트를 할 경우에도 mock 객체를 사용해서 테스트를 진행할 수 있다.  

##  DIP 주의사항
DIP를 잘못 적용한 예  
<img width="435" alt="스크린샷 2024-02-09 오후 3 45 19" src="https://github.com/shmin7777/ddd-architecture/assets/67637716/b3834453-3056-425f-84b9-7a2c2f615619">  
이는 잘못된 구조이다.  
이 구조에서 도메인 영역은 구현 기술을 다루는 인프라스트럭처 영역에 의존하고 있다.  
이는 여전히 고수준 모듈이 저수준 모듈에 의존하고 있는 것.  
DIP를 적용할 때 하위 기능을 추상화한 인터페이스는 고수준 모듈 관점에서 도출한다.  
즉 추상화 인터페이스는 저수준 모듈이 아닌 고수준 모듈에 위치 해야한다.  
<img width="466" alt="스크린샷 2024-02-09 오후 3 47 18" src="https://github.com/shmin7777/ddd-architecture/assets/67637716/9a52c43e-d738-42aa-a562-7d1e8b9b1969">  

## DIP와 아키텍처
<img width="382" alt="스크린샷 2024-02-09 오후 3 48 31" src="https://github.com/shmin7777/ddd-architecture/assets/67637716/34936c62-8e48-4fd0-82a2-3fcf7fc14c8f">  
인프라스트럭처 영역은 구현 기술을 다루는 저수준 모듈이고 응용 영역과 도메인 영역은 고수준 모듈이다.  
인프라스트럭처 계층이 가장 하단에 위치하는 계층형 구조와 달리 아키텍처에 DIP를 적용하면 인프라스트럭처 영역이 응용 영역와 도메인 영역에 의존(상속)하는 구조가 된다.  

인프라스트럭처에 위치한 클래스가 도메인이나 응용 영역에 정의한 인터페이스를 상속받아 구현하는 구조가 되므로 도메인과 응용 영역에 대한 영향을 주지 않거나 최소화하면서 구현 기술을 변경하는 것이 가능하다.  

<img width="478" alt="스크린샷 2024-02-09 오후 3 49 43" src="https://github.com/shmin7777/ddd-architecture/assets/67637716/7bf52141-210d-41c9-be49-aa0c92453ea9">  

인프라스트럭처 영역의 EmailNotifier 클래스는 응용 영역의 Notifier 인터페이스를 상속받고 있다.  
주문 시 통지 방식에 SMS를 추가해야 한다는 요구사항이 들어왔을 때 응용 영역의 OrderService는 변경할 필요가 없다.  

<img width="503" alt="스크린샷 2024-02-09 오후 3 53 04" src="https://github.com/shmin7777/ddd-architecture/assets/67637716/0aea9fd0-f03d-4031-8d70-89188a248796">  
위와 같이 두 통지 방식을 함께 제공하는 Notifier 구현 클래스를 인프라스트럭처 영역에 추가하면 된다.  
비슷하게 마이바티스 대신 JPA를 구현 기술로 사용하고 싶다면 JPA를 이용한 OrderRepository구현 클래스를 인프라스트럭처 영역에 추가하면 된다.  

```
note

DIP를 항상 적용할 필요는 없다.
사용하는 구현 기술에 따라 완벽한 DIP를 적용하기보다는 구현 기술에 의존적인 코드를 도메인에 일부 포함하는 게 효과적일 때도 있다.
또한 추상화 대상이 잘 떠오르지 않을 때도 있다.
이럴 때는 무조건 DIP를 적용하려고 시도하지 말고 DIP의 이점을 얻는 수준에서 적용 범위를 검토하자.  
``` 








