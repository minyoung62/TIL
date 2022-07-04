# Ch2 아키텍처 개요 

## 2.1 네개의 영역
  - 표현, 응용, 도메인, 인프라스트럭처는 아키텍처를 설계할 때 출현하는 전형적인 네 가지 영역이다
  - 표현 영역(또는 UI영역)
    - 사용자의 요청을 받아 응용 영역에 전달하고 응용영역의 처리 결과를 다시 사용자에게 보여줌
  - 웹 애플리케이션의 표현 영역
    - HTTP 요청을 응용 용역이 필요로 하는 형식으로 변환해서 응용 영역에 전달하고 응용 영역의 응답을 HTTP 응답으로 변환하여 전송
  - 응용 영역
    - 사용자에게 제공해야 할 기능을 구현 (주문 등록, 주문 취소, 상품 상세 조회 등)
    - 응용 여역은 기능을 구현하기 위해 도메인 영역의 도메인 모델 사용 
    ``` java
    public class CancelOrderService() {
      @Transactinal
      public void cancelOrder(String orderId) {
        Order order = findOrderById(orderId);
        if (order == null) thorw new OrderNotFoundException(orderId);
        order.cancel();
      }
    }
    ```
    - 응용 서비스는 로직을 직접 수행하기보다는 도메인 모델에 로직 수행을 위임
    - 위 코드도 주문 취소 로직을 직접구현하지 않고 Order객초에 취소 처리를 위임
    - 즉, 응용 영역은 도메인 모델을 이용해서 사용자에게 제공할 기능을 구현한다. 실제 도메인 로직 구현은 도메인 모델에 위임 
  - 인프라스트럭처 영역
    - 구현 기술에 대한 것을 다룸 
    - 이 영역은 RDBMS 연동을 처리, 메시징 큐에 메시지를 전송 데이터 연동 처리를 함 
    - 이 영역은 SMTP를 이용한 메일 발송 기능을 구현하거나 HTTP 클라이언트를 이용해서 REST API를 호출하는 것도 처리한다 
## 2.2 계층 구조 아키텍처
  - 네 영역을 구성할 때 많이 사용하는 아키텍처가 *표현 -> 응용 -> 도메인 -> 인프라스트럭처*와 같은 계층 구조이다 
  - 표현 영역과 응용 용역은 도메인 영역을 사용하고, 도메인 영역은 인프ㅏㄹ스트럭처 영역을 사용하므로 계층 구조를 적용하기에 적당해 보임
  - 계층 구조는 그 특성상 상위 계츠엥서 하위 계층으로의 의존만 존재
  - 편리함을 위해 응용 계층이 인프라스트럭처 계층에 의존하기도 함
  - 표현, 응용, 도메인 계층이 상세한 구현 기술을 다루는 인프라스트럭처 계층에 종속됨
  - 도메인의 가격 계산 규칙을 예로 들어보자
  - 다음은 할인 금액을 계산하기 위해 Droools라는 룰 엔지을 사용해서 계산 로직을 수행하는 인프라스트럭처 영역의 코드를 만들어본 것
    ``` java
    public class DroolsRuleEngine {
      ...
      public void evaluete(String sessionName, List<?> facts) {...}
    }
    ```
  - 응용 영역은 가격 계산을 위해 인프라스트럭처 영역의 DroolsRuleEngine을 사용
    ``` java
    public class CaculateDiscountService {
      private DroolsRuleEngine ruleEngine;

      public CalculateDiscountService() {
        ruleEngine = new DroolsRuleEngine() ;
      }

      public Money calculateDiscount(List<OrderLine> orderLines, String customerId) {
        Customer customer = findCustomer(customerId);

        MutableMoney money = new MutableMoney(0);
        List<?> facts = Arrays.asList(customer, money);
        facts.addAll(orderLines);
        ruleEngine.evalute("discountCalculation", facts);
        return money.toImmutableMoney();
      }
      ...
    }
    ```
    - 위 코드는 두가지 문재점 존재 
    - 첫 번째 문제는 CalculateDiscountService만 테스트하기 어렵다
      - CalculateDiscountService를 테스트하라면 RuleEngine이 완벽히 동작해야함
    - 두 번째 문제는 구현 방식을 변경하기 어렵다 
      - discountCalculation 문자열은 Drools의 세선 이름을 의미  이것을 변경하려면 CalculationDiscountService의 코드도 함께 변경해야함
      - MutableMoney는 룰 적용 결괏 값을 보관하기 위해 추가한 타입인데 다른 방식을 사용했다면 필요 없는 타입 
      - 이처럼 CalculationDiscountService가 인스프라스트럭처의 기술에 직접적으로 의존 
      - 이 상황에서 Drools가 아닌 다른 구현 기술을 사용하려면 코드의 많은 부분을 고쳐야만 함
## 2.3 DIP
  - 가격 할인 계산(CalculationDiscountService)는 고객 정보를 구해야 하고, 구한 고객 정보와 주문 정보를 이용해서 룰을 실행해야 한다 
  - 여기서 CalculationDiscountService는 고수준 모듈이다 
    - 고수준 모듈은 의미 있는 단일 기능을 제공하는 모듈로 가격 할인 계산이라는 기능을 구현
    - 고수준 모듈의 기능을 구현하려면 여러 하위 기능이 필요
  - 저수준 모듈은 하위 기능을 실제로 구현한 것
    - JPA를 이용해서 고객 정보를 읽어오는 모듈과 Drools로 룰을 실행하는 모듈이 저수준 모듈이다 
  - 고수준 모듈이 제대로 동작하려면 저수준 모듈을 사용해야 한다. 그런데 고수준 모듈이 저수준 모듈을 사용하면서 앞서 계층 구조 아키텍처에서 언급했던 구다지 문제, 즉 구현 변경과 테스트가 어렵다는 문제가 발생
  - DIP는 이 문제를 해결하기 위해 저수준 모듈이 고수준 모듈에 의존하도록 바꾼다 
  - CalculateDiscountService 입장에서 룰 적용을 Drools로 구현했는지 자바로 직접 구현했는지 중요하지 않다
  - '고객 정보와 구매 정보에 룰을적용해서 할인 금액을 구한다'라는 것만 중요
  - 이를 추상화한 인터페이스는 다음과 같다
    ``` java
    public interface RuleDiscounter {
      Money applyRules(Customer customer, List<OrderList> orderLines);
    }
    ```
    ``` java
    public class CalculateDiscountService {
      private RuleDiscounter ruleDiscounter;
      
      public CalculateDiscountService(RuleDiscounter ruleDiscounter) {
        this.ruleDiscounter = ruleDiscounter;
      }
      
      public Money calculateDiscounter(List<OrderLine> orderLines, String customerId) {
        Customer customer = findCustomer(customerId);
        return ruleDiscounter.applyRules(customer, orderLines);
      }
      ...
    } 
    ```
    - CalculateDiscounteService에는 Droolse에 의존하는 코드가 없다. 단지 RuleDiscounter가 룰을 적용한다는 사실만 알뿐이다.
    - 여기서 중요한 것은 Drools가 RuleDiscounter를 상속받아 구현한다는 것이다
    ``` java
    public class RoolsRuleDiscounter implements RuleDiscounter {
      ...
      
      @Override
      public Money applyRules(Customer, List<OrderLine> orderLines) {
        ...
      }
    }
    ```
  - DIP를 적용하면 저수준 모듈이 고수준 모듈에 의존하게 된다
  - 고수준 모듈이 저수준 모듈을 사용하라면 고수준 모듈이 저수준 모듈에 의존해야하는데, 반대로 저수준 모듈이 고수준 모듈에 의존한다고 해서 이를 DIP. 즉, 의존 역전 원칙이라 부름
  - 고수준 모듈은 더 이상 저수준 모듈에 의존하지 않고 구현을 추상화한 인터페이스에 의존 
  - 실제 사용할 저수준 구현 객체는 다음 코드처럼 의존 주입을 이용해 전달 받음
    ``` java
    // 사용할 저수준 객체 생성
    RuleDiscounter ruleDiscounter = new DroolseRuleDiscounter();
    
    // 생성자 방식으로 주입
    CalculateDiscounteSerice disService = new CalculateDiscounterService(ruleDiscounter);
    ```
    - 구현 기술을 변경하더라도 CalculateDiscounterService를 수정할 필요가 없음 
    ``` java
    // 사용할 저수준 객체 생성
    RuleDiscounter ruleDiscounter = new SimpleRuleDiscounter();
    
    // 생성자 방식으로 주입
    CalculateDiscounteSerice disService = new CalculateDiscounterService(ruleDiscounter);
    ```
  - 테스트에 대해 언급하기 전에 CalculateDiscounterService가 제대로 작동하려면 Customer를 찾는 기능도 구현해야함
  - 이를 위한 고수준 인터페이스를 CustomerRepository라고 함 
    ``` java
    public class CalculateDiscountService {
      private CustomRepository cusotmerRepsoitory;
      private RuleDiscounter ruleDiscounter;
      
      public CalculateDiscountService(...) {...}
      
      public Money caculateDiscount(List<OrderLine> orderLines, String customerId) {
        Customer customer = findCustomer(customerId);
        return ruleDiscounter.applyRules(customer, orderLines);
      }
      
      private Customer findCustomer(String customerId){
        Customer customer = customerRepository.findById(customerId);
        if (customer == null) throw new NoCustomerException();
        return customer;
      }
    
    }
    ```
  - 다음은 대역 객체를 사용해서 Customer가 존재하지 않는 경우 익셉션이 발생하는지 검증하는 테스트 코드의 예이다
    ``` java
    public class CalculateDiscountServiceTest {
      @Test
      public void noCustomer_thenExceptionShouldBeThrown() {
        // 테스트 목적의 대역 객체
        CustomerRepository stubRepo = mock(CustomerRepository.class);
        when(stubRepo.findById("noCustId")).thenReturn(null);
        
        RuleDiscounter stubRule = (cust, line) -> null;
        
        // 대용 객체를 주입 받아 테스트 진행
        CalculateDiscountService calDisSvc = new CalculateDiscountService(stubRepo, stubRule);
        
        assertThrow(NoCustomerException.class, () -> calDisSvc.calculateDiscount(someLines, "noCustId"));
      }
    }
    ```
    - 이 코드에서 stubRepo와 stubRule은 각각 CustomerRepsoitory와 RuleDiscounter의 대역 객체이다
    - stubRepo는 Mockito라는 Mock프레임워크를 이용해서 대역 객체를 생성했고
    - stubRule은 메서드가 한 개여서 람다식을 이용해 객체를 생성 
    - 두 대역 객체는 테스트를 수행하는 데 필요한 기능만 수행
    - stubRepo의 경우 findById("noCustId")를 실행하면 null을 리턴하는데, calDisSvc를 생성할 때 생성자로 stubRepo를 주입
    - 따라서 calDisSvc.calculateDiscounter(someLines, "noCusId") 코드를 실행하면 customerRepository.findById(customerId) 코드는 null을 리턴하고 NoCustomerException을 발생
    - 앞서 테스트 코드는 CustomerRepository와 RuleDiscounter의 실제 구현 클래스가 없어도 CalculateDiscounterService를 테스트할 수 있음을 보여준다 
    - 이렇게 실제 구현 없이 테스트를 할 수 있는 이유는 DIP를 적용해서 고수준 모듈이 저수준 모듈에 의즈ㅗㄴ하지 않도록 했기 때문
### 2.3.1 DIP 주의사항
  - DIP의 핵심은 고수준 모듈이 저수준 모듈에 의존하지 않도록 하기 위함인데 DIP를 적용한 결과 구조만 보고 저수준 모듈에서 잉ㄴ터페이스를 추출하는 경우가 있다
  - DIP를 적용할 때 하위 기능을 추상화한 인터페이스는 고수준 모듈 관점에서 도출한다
### 2.3.2 DIP와 아키텍처
  - 인프라스트럭처 영역은 구현 기술을 다루는 저수준 모듈이고 응용 영역과 도메인 영역은 고수준 모듈이다 

## 2.4 도메인 영역의 주요 구성 요소
  - 도메인 영역은 도메인의 핵심 모델을 구현한다
  - 도메인 영역의 모델은 도메인 주요 개념을 표현하며 핵심 로직으로 구현
  - 도메인 영역의 주요 구성요소
    - 엔티티
      - 고유의 식별자를 가즌ㄴ 객체로 자신의 라이프 사이클을 갖는다
      - 주문, 회원, 상품과 같이 도메인의 고유한 개념을 표현
      - 도메인 모델의 데이터를 포함하며 해당 데이터와 관련된 기능을 함께 제공
    - 벨류
      - 고유의 식별자를 갖지 않는 객체로 주로 개념적으로 하나인 값을 표현할 때 사용
      - 배송지 주소를 표현하기 위한 주소나 구매 금액을 위한 금액와 같은 타입이 밸류타입
      - 엔티티의 속성으로 사용할 뿐만 아니라 다른 밸류 타입의 속성으로도 사용할 수 있음
    - 애그리거트
      - 연관된 엔티티와 밸류 객체를 개념적으로 하나로 묶은 것
      - 예를들어 주문과 관련된 Order 엔티티, OrderLine 밸류, Orderer 밸류 객체를 '주문' 애그리거트로 묶을 수 있음
    - 리포지터리
      - 도메인 모델의 영속성을 처리
    - 도메인 서비스
      - 특정 엔티티에 속하지 않은 도메인 로직을 제공
      - '할인 금액 계산'은 상품, 쿠폰, 회원 등급, 구매 금액 등 다양한 조건을 이용해서 구현하게 되는데, 이렇게 도메인 로직이 여러 에닡티와 밸류를 필요로 하면 도메인 서비스에 로직을 구현 
### 2.4.1 엔티티와 밸류
  - DB 테이블의 엔티티와 도메인 모델의 엥ㄴ티티의 차이점
    - 도메인 모델의 엔티티는 데이터와 함께 도메인 기능을 함께 제공
  - 밸류는 불변으로 구현할 것을 권장하며, 이는 엔티티의 배률 타입 데이터 변경할 때는 객체 자체를 완전히 교체해야한다는 의미
    ``` java
    public class Order {
      private ShippingInfo shippingInfo ;
      ...
      // 도메인 모델 엔티티는 도메인 기능도 함께 제공
      public void changeShippingInfo(ShippingInfo newShippingInfo){
        checkShippingInfoChangeable();
        setShippingInfo(newShippingInfo);
      }
      
      private void setShippingInfo(ShippingInfo newShippingInfo){
        if (newShippingInfo == null) throw new IllegalArgumentException();
        // 밸류 타입의 데이터를 변경할 때는 새로운 객체로 교체
        this.shippingInfo = newShippingInfo;
      }
      
    }
    ```
### 2.4.2애그리거트
  - 도메인이 커질수록 개발할 도메인 모델도 커지면서 많은 엔티티와 밸류가 출현
  - 엔티티와 밸류 개수가 많아질수록 모델은 점점 더 복잡해진다
  - 이렇게 되면 전체 구조가 아닌 한 개 엔티티와 밸류에만 집중하는 상황이 발생
  - 도메인 모델에서 전체 구조를 이해하는 데 도움이 되는 것이 바로 애그리거트이다
  - 애그리거트
    - 관련 객체를 하나로 묶은 군집
  - 대표적인 예가 주문이다
  - 주문이라는 도메인 개념은 주문, 배송지정보 , 주문자, 주문 목록, 총 결제 금액의 하위 모델로 구성된다
  - 이 하위 개념을 표현한 모델을 하나로 묶어서 주문이라는 상위 개념으로 표현
  - 애그리거트를 사용하면 개별 객체가 아닌 관련 객체를 묶어서 객체 군집 단위로 모델을 바라볼 수 있음 
### 2.4.3 리포지터리
  - 도메인 객체를 지속적으로 사용하려면 RDBMS, NoSQL, 로컬 파일과 같은 물리적인 저장소에 도메인 객체를 보관해야한다. 이를 위한 도멩니 모델이 리포지터리다

## 2.5 요청 처리 흐름
  - 표현 영역은 사용자가 전송한 데이터 형식이 올바른지 검사하고 문제가 없다면 데이터를 이용해서 응용 서비스에 기능 실행을 위임
  - 응용 서비스는 도메인 모델을 이용해서 기능을 구현 + 트랜잭셔 관리

## 2.6 인프라스트럭처 개요
  - 인프라스트럭처는 표현 영역, 응용 영역, 도메인 영역을 지원 
  - 도메인 영역과 응용 영역에서 인프라스트럭처의 기능을 직접 사용하는 것보다 이 두 영역에 정의한 인터페이스를 인프라스트럭처 영역에서 구현하는 것이 시스템을 더 유연하고 테스트하기 쉽게 만들어준다
## 2.7 모듈의 구성




