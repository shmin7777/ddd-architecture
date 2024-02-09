# DIP
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
<img width="404" alt="스크린샷 2024-02-09 오후 3 37 23" src="https://github.com/shmin7777/ddd-architecture/assets/67637716/b28e564f-69ba-400d-ba39-d9b59a920163">  

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


