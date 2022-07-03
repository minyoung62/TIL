# ch1. 도메인 모델 시작하기
## 1.1 도메인이란?
  - 개발자 입장에서 온라인 서점은 구현해야 할 소프트웨어 대상
  - 온라인 서점 소프트웨어는 상품 조회, 구매, 결제, 배송 추적 등의 기능 제공
  - 이때 온라인 서점은 소프트웨어로 해결하고자 하는 문제 영역, 즉 도메인(domain)에 해당 
  - 하위 도메인
    - 한 도메인은 다시 하위 도메인으로 나눌 수 있음
    - 하위도메인 
      - 회원, 혜택, 주문, 카탈로그, 리뷰, 정산, 배송, 결제
      - 카탈로그 하위 도메인은 고객에게 구매할 수 있는 상품 목록 제공
      - 주문 하위 도메인은 고객의 주문 처리
      - 혜택 하위 도메인은 쿠폰이나 특별 할인과 같은 서비스 제공
      - 배송 하위 도메인은 고객에게 구매한 상품을 전달하는 일련의 과정을 처리
    - 한 하위 도메인은 다른 하위 도메인과 연동하여 완전한 기능을 제공
      - 예를 들어 고객이 물건을 구매하면 주문, 결제, 배송, 혜택 하위 도메인의 기능이 엮임
    - 특정 도메인을 위한 소프트웨어라고 해서 도메인이 제공해야할 모든 기능을 직접구현하지는 않음
      - 많은 온라인 쇼핑몰이 자체적으로 배송 시스템을 구추가힉보다는 외부 배송 업체의 시스템을 사용
      - 결제 시스템도 마찬가지
      
## 1.2 도메인 전문가와 개발자 간 지식 공유
  - 도메인 전문가와 개발자 간 지식 공유
    - 온라인 홍보, 정산, 배송 등 각 영역에 전문가 존재
    - 이들 전문가는 해당 도메인에 대한 지식과 경험을 바탕으로 본인들이 원하는 기능 개발 요구
    - 개발자는 이런 요구사항을 분석하고 설계하여 코드를 작성하며 테스트하고 배포
    - 이 과정에서 요구사항은 첫 단추와 같음
    - 요구사항을 올바르게 이해하지 못하면 잘못 개발한 코드를 수정해 올바르게 고치려면 많은 노력 필요
  - 요구사항을 올바르게 이해하는 방법
    - 개발자와 전문가가 직접 대화하는 것
    - 도메인 전문가 만큼은 아니겠지만 이해관계자와 개발자도 도메인 지식을 갖춰야함 
    
## 1.3 도메인 모델
  - 도메인 델이란?
    - 특정 도메인을 개념적으로 표현하는 것
  - 예시(주문 도메인 생각)
    - 온라인 쇼핑물에서 주문하려면 상품을 몇개 살지 선택하고 배송지 입력
    - 선택한 상품 가격을 이용해 총 지불 금액을 계산
    - 금액 지불을위한 결제 수단 선택 
    - 주문한 뒤에도 배송 전이면 배송지 주소를 변경하거나 주문을 취소할 수 있음
## 1.4 도메인 모델 패턴
  - 애플리케이션의 아키텍처는 아래 [그림1.5] 같이 네 개의 영역으로 구성
    - [그림1.5]
    - 아키텍처 구성
      - 사용자 인터페이스 UI 또는 표현: 사용자의 요청을 처리하고 사용자에게 정보를 보여준다. 여기서 사용자는 소프트웨어를 사용하는 사람뿐만 아니라 외부 시스템일 수도 있다
      - 응용: 사용자가 요청한 기능을 실행한다. 업무 로직을 직접 구현하지 않으며 도메인 계층을 조합해서 기능을 실행한다
      - 도메인: 시스템이 제공할 도메인 규칙을 구현한다
      - 인프라스트럭처: 데이터베이스나 메시징 시스템과 같은 외부 시스템과 연등을 처리한다
    ``` java
    public class Order{
      private OrderState state;
      private ShipppingInfo shippingInfo;
      
      public void changeShippingInfo(ShippingInfo newShippingInfo) {
        if(!state.isShippingChangeable()){
          throw new IllegalStateException("can't change shipping in" + state);
        }
        this.shippingInfo = newShippingInfo;
      }
      ...
    }
    
    public enum OrderState{
      PAYMENT_WAITING {
        public boolean isShippingChangeable(){
          return true;
        }
      },
      PREPARING {
        public boolean isShippingChangeable() {
          return true;
        ]
      },
      SHIPPED, DELIVERING, DELIVERY_COMPLETED;
      
      public boolean isShippingChangeable() {
        return false;
      }
    }
    ```
    - 이 코드는 주문 도메인의 일부 기능을 도메인 모델 패턴으로 구현한 것
    - 주문 상태를 표현하는 OrderState는 배송지를 변경할 수 있는지를 검사할 수 있는 isShippingChangeable() 메서드 제공
    - 코드를 보면 주문 대기 중(PAYMENT_WATING) 상태와 상품 준비 중(PREPARING) 상태의 isShippingChangeable() 메서드는 true를 리턴
    - 즉 OrderState는 주문 대기 중이거나 상품 준비 중에는 배송지를 변경할 수 있다는 도메인 규칙을 구현
    - OrderState는 Order에 속한 데이터이므로 배송지 정보 변경 가능 여부를 판단하는 코드를 Order로 이동할 수 있음 
    - 아래는 수정 코드 이다 
    ``` java
    public class Order{
      private OrderState state;
      private ShippingInfo shippingInfo;
      
      public void changeShippingInfo(ShippingInfo newShippingInfo) {
        if (!isShippingChangeable()) {
          throw new IllegalStateException("can't change shipping in" + state);
        }
        this.shippingInfo = new ShippingInfo;
      }
      
      private boolean isShippingChangeable() {
        return state == OrderState.PAYMENT_WAITING || state == OrderState.PREPARING;
      
    }
    
    public enum OrderState {
      PAYMENT_WAITING, PREPARING, SHIPPED, DELIVERING, DELIVERY_COMPLETED;
    }
    ```
  - 개념 모델과 구현 모델
    - 개념 모델
      - 순수하게 문제를 분석한 결과물
      - 데이터베이스, 트랜잭션 처리, 성능, 구현 기술과 같은 것을 고려하지 않음
    - 구현 모델 
      - 실제 데이터베이스, 트랜잭션 처리, 성능, 구현 기술들을 고려한 것 
      - 실제 결과물 
  - 처음부터 완벽한 개념 모델을 만들기 보다는 전반저깅ㄴ 개요를 알 수 있는 수준으로 개념모델을 작성해야함
  - 프로젝트 초기에는 개요 수준의 개념 모델로 도메인에 대한 전체 윤곽을 이요하는데 집중
  - 구현하는 과정에서 개념 모델을 구현 모델로 점차 발전 해야함
      
## 1.5 도메인 모델 도출
  - 도메인 모델링할 때 기본이 되는 작업은 모델을 구성하는 핵심 구성요서, 규칙, 기능을 찾는 것
  - 이 과정은 요구사항에서 부터 출발 
  - 주문 도메인과 관련된 몇 가지 요구사항을 보자 
    - 최소 한 종류 이상의 상품을 주문해야 한다
    - 한 상품을 한 개 이상 주문할 수 있따
    - 총 주문 금액은 각 상품의 구매 가격 합을 모두 더한 금액
    - 각 상품의 구매 가격 합은 상품 가격에 구매 개수를 곱한 값이다
    - 주문할 때 배송지 정보를 반드시 지정해야 한다
    - 배송지 정보는 받는 사람 이름, 전화번호, 주소로 구성된다
    - 출고를 하면 배송지를 변경할 수 없다
    - 출고 전에 주문을 취소할 수 있다
    - 고객이 결제를 완료하기 전에는 상품을 준비하지 않는다
  - 이 요구사항에서 알 수 있는 것은 주문은 '출고 상태로 변경하기','배송지 정보 변경하기', '주문 취소하기', '결제 완료하기' 기능 제공
    ``` java
    public class Order {
      public void changeShipped() {...}
      public void changeShippingInfo(ShippingInfo newShipping) {...}
      public void cancel() {...}
      public void completPayment() {...}
    }
    ```
  - 다음 요구사항은 주문 항목이 어떤 데이터로 구성되는지 알려줌
    - 한 상품을 한 개 이상 주문할 수 있다
    - 각 상품의 구매 가격 합은 상품 가격에 구매 개수를 곱한 값
    - 두 요구사항에 따르면 주문 항목을 표현하는 OrderLine은 적어도 주문할 상푸므, 상품의 가격, 구매 개수를 포함해야하고 추가로 각 구매 항목의 구매 가격도 제공해야함
    ``` java
    public class OrderLine {
      private Product product;
      private int price;
      private int quantity;
      private int amounts;
      
      public OrderLine(Product product, int private, int quantity) {
        this.product = product;
        this.price = price;
        this.quantity = quantity;
        this.amounts = calculateAmounts();
      }
      
      private int calculateAmounts() {
        return price * quantity;
      }
      
      public int getAmounts() {...}
      ...
    }
    ```
    - OrderLine은 한 상품을 얼마에 몇 개 살지를 담고 있고 calculateAmounts()메서드로 구매 가격을 구하는 로직을 구현하고 있음 
  - 다음 요구사항
    - 최소 한 종류 이상의 상품을 주문해야 한다
    - 총 주문 금액은 각 상품의 구매 가격 합을 모두 더한 금액이다
  - 한 종류 이상의 상품을 주문할 수 있으므로 Order는 최소 한 개 이상의 OrderLine을 포함해야 한다.
  - 또한 총 주문 금액은 OrderLin에서 구할 수 있다
    ``` java
    public class Order{
      private List<OrderLine> orderLines;
      private Monty totalAmounts;
      
      public Order(List<OrderLine> orderLines) {
        setOrerLines(orderLines);
      }
      
      private void setOrderLines(List<OrderLine> orderLines) {
        verifyAtLeastOneOrMoreOrderLines(orderLines);
        this.orderLines = orderLines;
        caculateTotalAmounts();
      }
      
      private void verifyAtLeastOneOrMoreOrderLines(List<OrderLine> orderLines) {
        if (orderLines == null || orderLines.isEmpty()) {
          throw new IllegalArgumentException("no OrderLine");
        }
      }
      
      private void calculateTotalAmounts() {
        int num = orderLines.stream()
                            .mapToInt(x->x.getAmounts())
                            .sum();
        this.totalAmounts = new Money(sum);
      }
      ...// 다른 메서드
    }
    ```
    - Order는 한 개 이상의 OrderLine을 가질 수 있으므로 Order를 생성할 때 OderLine 목록을 List로 전달
    - 생성자에서 호출하는 setOrderLines() 메서드는 요구사항에 정의한 제약 조건을 검사 
    - calculateTotalAmounts() 메서드를 이용하여 총 주문 금액 계산
    ``` java
    public class ShippingInfo {
      private String receiverName;
      private String receiverPhoneNumber;
      private String shippingAddress1;
      private String shippingAddress2;
      private String shippingZipcode;
    }
    ```
    - 앞서 요구사항 중에 '주문할 때 배송지 정보를 반드시 지정해야 한다"라는 내용이 있음
    - 이는 Order를 생성할 때 OrderLine의 목록뿐만 아니라 ShippingInfo도 함께 전달해야 함을 의미
    ``` java
    public class Order{
      private List<OrderLine> orderLines;
      private Shipping shippingInfo;
      ...
      
      public Order(List<OrderLine> orderLines, ShippingInfo shippingInfo) {
        setOrderLines(orderLines);
        setShippingInfo(shippingInfo);
      }
      
      private void setShippingInfo(ShippingInfo shippingInfo) {
        if (shippingInfo == null) 
          throw new IllegalArgumentException("no ShippingInfo");
        this.shippingInfo = shippingInfo;
      }
    ```
  - 주문 요구사항에서의 제약과 규칙
    - 출고를 하면 배송지 정보를 변경할 수 없다
    - 출고 전에 주문을 취소할 수 없다
  - 상태와 관련있는 요구사항
    - 고객이 결제를 완료하기 전에는 상품을 준비하지 않는다
    ``` java
    public enum OrderState {
      PAYMENT_WAITING, PREPARING, SHIPPED, DELIVERING, DELIVERY_COMPLETED;
    }
    ```
    ``` java
    public class Order {
      private OrderState state;
      
      public void changeShippingINfo(ShippingInfo newShippingInfo) {
        verifyNotYetShipped();
        setShippingINfo(newShippingInfo);
      }
      
      public void cancel() {
        verifyNotYetShipped();
        this.state = OrderState.CANCELED;
      }
      
      private void verifyNotYetShipped() {
        if (state != OrderState.PAYMENT_WAITING && state != OrderState.PREPARING)
          throw new IllegalStateException("aleary shipped");
      }
    }
    ```
    
## 1.6 엔티티와 밸류
### 1.6.1 엔티티
  - 도출한 모델은 엔티티와 벨류로 구분 가능
  - 엔티티
    - 엔티티의 특징은 식별자를 가진다 
    - 식별자는 엔티티 객체마다 교유해서 각 엔티티는 서로 다른 식별자를 갖는다 
    - 예를들어 주문에서 배송지 주소가 바뀌거나 상태가 바뀌더라도 주문번호가 바뀌지는 않는 것처럼 엔티티의 식별자는 바뀌지 않음
    - 엔티티를 구현한 클래스는 다음과 같이 식별자를 이용해서 equals() 메서드와 hashCode() 메서드를 구현할 수 있음
    ``` java
    public class Order {
      private String orderNumber;
      
      @Override
      public boolean equals(Object obj) {
        if (this == obj) return true;
        if (obj == null) return false;
        if (obj.getClass() != Order.class) return false;
        Order other = (Order)obj;
        if (this.orderNumber == null) return false;
        return this.orderNumber.equals(other.orderNumber);
       
      }
      
      @Override
      public int hashCode() {
        final int prime = 31;
        int result = 1;
        result = prime * result + ((orderNumber == null) ? 0 " orderNumber.hashCode());
        return result;
      }
    }
    ```
### 1.6.2 엔티티의 식별자 생성자
  - 엔티티 식별자 생성 방식 
    - 특정 규칙에 따라 생성
    - UUID나 Nano ID와 같은 고유 식별자 생성기 사용
    - 값을 직접 입력
    - 일련번호 사용(시퀀스나 DB의 자동 증가 컬럼 사용)
  - 주문번호, 운소장번호, 카드번호와 같은 식별자는 특정 규칙에 따라생성
  - UUID 식별자 생성
    - 자바는 java.util.UUID 클래스를 사용해 UUID를 생성할 수 있음
    ``` java
    UUID uuid = UUID.randomUUID();
    String strUuid = uuid.toString();
    ```
  - 회원의 아이디나 이메일과 같은 식별자 값은 직접 입력
  - 데이터베이스에 저장할 때 자동으로 증가 칼람을 사용하여 식별자로 사용 가능
### 1.6.3 밸류 타입
  - ShippingInfo 클래스는 다음과 같이 받는 사람과 주소에 대한 데이터를 갖고 있음
    ``` java
    public class ShippingInfo {
      private String receiverName;
      private String receiverPhoneNumber;
      private String shippingAddress1;
      private String shippingAddress2;
      private String shippingZipcode;
    }
    ```
    - receiverName와 receiverPhoneNumber는 서로 다른 두 데이터를 담고 있지만 개념적으로 받는 사람을 의미
    - shippingAddress1, shippingAddress2, shippingZipcode 또한 주소라는 하나의 개념을 표현
    - 벨류 타입은 개념적으로 완전한 하나를 표현할 때 사용
  - Receiver 
    ``` java
    public class Receiver {
      private String name;
      private String phoneNumber;
      
      public Receiver(String name, String phoneNumber) {
        this.name = name;
        this.phoneNumber = phoneNumber;
      }
      
      public String getName() {
        return name;
      }
      
      public String getPhoneNumber() {
        return phoneNumber;
      }
    }
    ```
    - Receiver는 '받는 사람'이라는 도메인 개념을 표현
  - Address
    ``` java
    public class Address {
      private String address1;
      private String address2;
      private String zipcode;
      
      public Address(String address1, String address2, String zipcode) {
        this.address1 = address1;
        this.address2 = address2;
        this.zipcode = zipcode;
      }
    }
    ```
  - ShippingInfo
    ``` java
    public class ShippingInfo {
      private Receiver receiver;
      private Address address;
    }
    ```
  - 밸류 타입이 꼭 두 개 이상의 데이터를 가져야하는 것은 아님 
  - 아래의 OrderLin이 좋은 예 
  - OrderLine
    ``` java
    public class OrderLine {
      private Product product;
      private int price;
      private int quantity;
      private int amount;
    }
    ```
    - OrderLIne의 price와 amounts는 int 타입의 숫자를 사용하고 있지만 이들은 '돈'을 의미하는 값 
    - 따라서 '돈'을 의미하는 Money 타입을 만들어서 사용하면 코드를 이해하는 데 도움이 됨
  - Money
    ``` java
    public class Money {
      private int value;
      
      public Money(int value) {
        this.money = money
      }
      
      public int getValue() {
        return this.value;
      }
    }
    ```
  - 아래는 Money를 사용하도록 OrderLine을 변경한 코드
  - Money 타입 덕에 price나 amount가 금액을 의미한다는 것을 쉽게 알 수 잇음
  - OrderLine
    ``` java
    public class OrderLine {
      private Product product;
      private Money price;
      private int quantity;
      private Money amounts;
    }
    ```
  - 밸류 타입으 ㅣ또 다른 장점은 밸류 타입을 위한 기능을 추가할 수 있다는 것이다.
  - 예를 들어 Money 타입은 다음과 같이 돈 계산을 위한 기능을 추가할 수 있음
  - Money
    ``` java
    public class Money {
      private int value;
      
      public Money add(Money money) {
        return new Money(this.value + moeny.value)
      }
      
      public Money multiply(int multiplier) {
        return new Money(value * multiplier);
      }
    }
    ```
    - Money를 사용하는 코드는 이제 '정수 타입 연산'이 아니라 '돈 계산'이라는 의미로 코드를 작성할 수 있다
  - 밸류 객체의 데이터를 변경할 때는 기존 데이터를 변경하기보다는 변경한 데이터를 갖는 새로운 밸류 객체를 생성하는 방식을 선호
  - Money처럼 데이터 변경 기능을 제공하지 않는 타입을 불변(immutable)이라고 함
  - 불변으로 구현하는 가장 중요한 이유는 안전한 코드를 적상할 수 있다는데 있음
    ``` java
    Money price = ...;
    OrderLine line = new OrderLine(product, price, quantity);
    // 만약 price.setValue(0)로 값을 변경할 수 있다면?
    ```
    - 그런데 만약 Money가 setValue()와 같은 메서드를 제공해서 값을 변경할 수 있따면
    ``` java
    Money price = new Money(1000)
    OrderLine line = new OrderLine(product, price, 2); #[price=1000, quantity=2, amounts=2000]
    price.setValue(2000);                              #[price=2000, quantity=2, amounts=2000]
    ```
    - 이런 문제가 발생한다 때문에 이러한 문제가 발생하는 것을 방지하려면 새로운 Moneyr 객체를 생성하도록 코드를 작성해야한다
    ``` java
    public class OrderLine {
      ...
      private Money price;
      
      public OrderLine(Product product, Money price, int quantity) {
        this.product = product;
        // Money가 불변 객체가 아니라면,
        // price 파라미터가 변경될 때 발생하는 문제를 방지하기 위해
        // 데이터를 복사한 새로운 객체를 생성해야 한다
        this.rpice = new Money(price.getValue());
        this.quantity = quantity;
        this.amounts = calculateAmounts();
      }
    }
    ```
    - Money가 불변이라면 이런 코드를 작성할 필요가 없음
    - Money의 데이터를 바꿀 수 없기 때문에 파라미터로 전달받은 price를 안전하게 사용가능
  - 두 밸류 객체를 비교
    ``` java
    public class Receiver {
      private String name;
      private String phoneNumber;
      
      pbulic boolean equals(Object other) {
        if(other == null) return false;
        if(this == other) return true;
        if(!(other instanceof Receiver)) return false;
        Receiver that = (Receiver)other;
        return this.name.equals(that.name) &&
            this.phoneNumber.equals(that.phoneNumber)
      } 
    ]
    ```
### 1.6.4 엔티티 식별자와 밸류 타입
  - 엔티티 식별자의 실제 데이터는 String과 같은 문자열로 구성된 경우가 많다
  - 신용카드 번호 16개의 숫자로 구성된 문자열이며 온라인 서비스에서 회원을 구분할 때는 이메일 주소도 문자열 
    ``` java
    public class Order {
      // OrderNo 타입 자체로 id가 주문번호임을 알 수 있다
      private OrderNo id;
      ...
      public OrderNo getId() {
        return id;
      }
    ```
    - OrderNo 대신에 String 타입을 사용한다면 id라는 이름만으로는 해당 필드가 주문번호인지 알기 힘듦
    - OrderNo 타입을 만들면 타입 자체로 주문번호인 것을 알 수 있음
### 1.6.5 도메인 모델에 set 메서드 넣지 않기
  - changeShippingInfo()가 배송지 정보를 새로 변경한다는 의미를 가졌다면 setShippingInfo() 메서드는 단순히 배송지 값을 설정한다는 것을ㅇ ㅢ미
  - set 메서드의 또 다른 문제는 도메인 객체를 생성할 때 온전하지 않은 상태가 될 수 있음 
    ``` java
    // set 메서드로 데이터를 전달하도록 구현하면
    // 처음 Order를 생성하는 시점에서 order는 완전하지 않다
    Order order = new Order();
    
    // set 메서드로 필요한 모든 값을 전달해야 함
    order.setOrderLine(lines);
    order.setShippingInfo(shippingInfo);
    
    // 주문자(Orderer)를 설정하지 않은 상태에서 주문 완료 처리
    order.setState(OrderState.PREPARING);
    ```
    - 위 코드는 주만자 설정을 누락하고 있다
    - 주문자 정보를 담고 있는필드인 orderer가 null인 상황에서 order.setState()메서드를 호출해서 상품 준비 중 상태로 바꾼 것
    - orderer가 정상인지 확인하기 위해 ordererrk null인지 검사하는 코드를 setState() 메서드에 위치하는 것도 맞지 않음
  - 도메인 객체가 불완전한 상태로 사용되는 것을 막으려면 생성 시점에 필요한 것을 전달해 주어야 한다.
  - 생성자로 필요한 것을 모두 받으므로 생성자를 호출하는 시점에서 필요한 데이터가 올바른지 검사할 수 있음
    ``` java
    public class Order {
      public Order(Orderer orderer, List<OrderLine> orderLines, ShippingInfo shippingInfo, OrderState state) {
        setOrderer(orderer);
        setOrderLines(orderLines);
        ...// 다른 설정
      }
      
      private void setOrderer(Orderer orderer) {
        if (orderer == null) throw new IllegalArgumentException("no orderer");
        this.orderer =orderer;
      }
      
      private void setOrderLines(List<OrderList> orderLines) {
        verfyAtLesatOneOrMoreOrderLines(orderLines);
        this.orderLines = orderLines;
        calculateTotalAmounts();
      }
      
      private void verifyAtLeastOneOrMoreOrderLines(List<OrderLine> orderLines) {
        if(orderLines = null || orderLines.isEmpty()) {
          throw new IllegalArgumentException("no OrderLine");
        }
      }
      
      private void calculateTotalAmounts() {
          ...
      }
    }
    
    ```
    - 이 코드의 set메서드는 앞서 언급한 set메서드와 차이점은 접근 점위가 private라는 것이다 
## 1.7 도메인 용어와 유비쿼터스 언어
  - 에릭 에반스는 도메인 주도 설계에서 언어의 중요함을 강조하기 위해 유비쿼터스 언어라는 용어를 사용했다
  - 전문가, 관계자, 개발자가 도메인과 관련된 공통의 언어를 만들고 이를 대화, 문서, 도메인 모델, 코드, 테스트 등 모든 곳에서 같은 용어를 사용 
