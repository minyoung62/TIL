## 3.1 애그리거트
  - 상위 수준 개념을 이용해서 전체 모들을 정리하면 전반적인 관계를 이해하는데 도움이됨
  - 백 개 이상의 ㅇ테이블을 한 장의 ERD에 모두 표시하면 개별 테이블 간의 관계를 파악하느라 큰 틀에서 데이터 구조를 이해하는데 어려움
  - 주요 도메인 요소 간의 관계를 파악하기 어렵다는 것은 코드를 변경하고 확장하는 것이 어렵다는 의미
  - 복잡한 도메인을 이해하고 관리하기 쉬운 단위로 만드는 것이 애그리거트이다
  - 애그리거트는 관련 된 객체를 하나의 군으로 묶어준다
  - 애그리거트는 독립된 객체 군이며 각 애그리거트는 자신을 관리할 뿐 다른 애그리거트를 관리하지 않는다
  - 잘못된 애그리거트
    - 'A가 B를 갖는다'로 설계할 수 있는 요구사항이 있다면 A와 B를 한 애그리거트로 묶어서 생각하기 쉽다
    - 하지만 'A가 B를 갖는다'로 해석할 수 있는 요구사항이 있다고 하더라도 이것이 반드시 A와 B가 한 애그리거트에 속한다는 의미는 아님
    - ex) 상품과 리뷰가 있다고할 , 상품 상세 페이지에 들어가면 상품 상세 정보와 함께 리뷰 내용을 보여줘야 한다는 요구사항이 있을 때 Prouct 엔티티와 Review 엔티티가 한 애그리거트에 속한다고 생각할 수 있지만. 
Product와 Review는 함께 생성되지 않고, 함께 변경되지 않는다. 게다가 Product를 변경하는 주체가 상품 담당자라면 Review를 생성하고 변경하는 주체는 고객임

## 3.2 애그리거트 루트
  - 애그리거트에 속한 모든 객체가 일관된 상태를 유지하려면 애그리거트 전체를 관리할 주체가 필요한데, 이 책임을 지는 것이 바로 애그리거트의 루트 엔티티이다.
### 3.2.1 도메인 규칙과 일관성
  - 애그리거트 루트의 핵심 역할은 애그리거트의 일관성이 깨지지 않도록 하는 것
  - 이를 위해 애그리거트 루트는 애그리거트가 제공해야 할 도메인 기능을 구현
  - ex) 주문 애그리거트는 배송지 변경, 상품 변경과 같은 기능을 제공하고, 애그리거트 루트인 Order가 이 기능을 구현한 메서드를 제공
  - 애그리거트 외부에서 애그리거트에 속한 객체를 직접 변경하면 안 된다
  - 이것은 애그리거트 루트가 강제하는 규칙을 적용할 수 없어 모델의 일관성을 깬느 원인이 됨 
    ``` java
    ShippingInfo si = order.getShippingInfo();
    si.setAddress(newAddress);
    ```
    - 이 코드는 애그리거트 루트인 Order에서 ShippingInfo를 가져와 직접 정보를 변경하고 있음
    - 주문 상태에 상관없이 배송지 주소를 변경하느넫, 이는 업무 규칙을 무시하고 직접 DB 테이블의 데이터를 수정하는 것과 같은 결과를 만듬
    - 즉, 논리적인 데이터 일관성이 깨지게 되는 것
    - 일관성을 지키기 위해 다음과 같이 상태 확인 로직을 응용 서비스에 구현할 수 있지만 이렇게 되면 동일한 검사 로직를 여러 으용ㅇ 서비스에서 중복으로 구현할 가능성이 높아져 유지 보수에 도움이 되지 않음
  - 불필요한 중복을 피하고 애그리거트 루트를 통해서만 도메인 로직을 구현하게 만들려면 다음 두 가지 습관이 필요
    - 단순히 필드를 변경하는 set 메서드를 공개범위로 만들지 않는다
    - 밸류 타입은 불변으로 구현
    ``` java
    public class Order {
      private ShippingInfo shippingInfo;
      
      public void changeShippingInfo(ShippingInfo newShippingInfo) {
        verifyNotYetShipped();
        setShippingInfo(newShippingInfo);
      }
    }
    
    // set 메서드의 접근 허용 범위는 private이다
    private void setShippingInfo(ShippingInfo newShippingInfo) {
      // 밸류가 불변이면 새로운 객체를 할당해서 값을 변경해야 한다
      // 불변이므로 this.shippingInfo.setAddress(newShippingInfo.getAddress())와 같은
      // 코드를 사용할 수 없다
      this.shippingInfo = newShippingInfo;
    }
    ```
### 3.2.2 애그리거트 루트의 기능 구현
  - 애그리거트 루트는 애그리거트 내부의 다른 객체를 조합해서 기능을 완성함
  - ex) Order는 총 주문 금액을 구하기 위해 OrderLine 목록을 사용
    ``` java
    public class Order {
      private Money totalAmounts;
      private List<OrderLine> orderLines;
      
      private void calculateTotalAmounts() {
        int sum = orderLines.stream()
            .mapToInt(ol -> ol.getPrice() * ol.getQuantity())
            .sum();
        this.totalAmounts = new Money(sum)l
      }
    }
    ```
  - 보통 한 애그리거트에 속하는 모델은 한 패키지에 속하기 때문에 패키지나 protected범위를 사용하면 애그리거트 외부에서 상태 변경 기능을 실행하는 것을 방지할 수 있다
### 3.2.3 트랜잭션 범위
  - 트랜잭션 범위는 작을수록 좋다
  - 한 트랜잭션이 한 개 테이블을 수정하는 것과 세 개의 테이블을 수정하는 것을 비교하면 성능에서 차이가 발생한다.
  - 한 개 테이블을 수정하면 트랜잭션 충돌을 막기 위해 잠그는 대상이 한개 테이블의 한 행으로 한정되지만, 세 개의 테이블을 수정하면 잠금 대상이 더 많아짐
  - 잠금 대상이 많아진다는 것은 그만큼 동시에 처리할 수 있는 트랜잭션 개수가 줄어든다는 것을 의미하고 이것은 전체  성능을 떨어트림

## 3.3 리포지터리와 애그리거트
  - 애그리거트는 개념상 왕ㄴ전한 한 개의 도메인 모델을 표현하므로 객체의 영속성을 처리하는 리포지터리는 애그리거트 단위로 존재
  - Order와 OrderLine을 물리적으로 각각 별도의 DB테이블에 저장하낟고 해서 Order와 OrderLine을 위한 리포지터리를 각각 만들지 않는다
  - Order가 애그리거트 루트고 OrderLine은 애그리거트에 속하는 구성요소이므로 Order를 위한 리포지터리만 존재 
## 3.4 ID를 이용한 애그리거트 참조
  - 한 객체가 다른 객체를 참조하는 것처럼 애그리거트도 다른 애그리거트를 참조한다.
  - 애그리거트 간의 참조는 필드를 통해 쉽게 구현 가능
  - 필드를 이용한 애그리거트 참조는 다음 문제를 야기
    - 편한 탐색 오용
    - 성능에 대한 고민
    - 확장 어려움
    ``` java
    public class Order {
      private Orderer orderer;
      
      public void changeShippingInfo(ShippingInfo newShippingInfo, boolean userNewShippingAddrAsMemberAddr) {
        if (userNewShippingAddrAsMemberAddr) {
          // 한 애그리거트 내부에서 다른 애그리거트에 접근할 수 있으면,
          // 구현이 쉬워진다는 것 때문에 다른 애그리거트의 상태를 변경하는
          // 유홉에 빠지기 쉬움
          order.getMember().changeAddress(newShippingInfo.getAddress())
        } 
      }
    }
    ```
    - 한 애그리거트에서 다른 애그리거트의 상태를 변경하는 것은 애그리거트 간의 의존 결합도를 높여서 결과적으로 애그리거트의 변경을 어렵게 만듬
    - 두 번재 문제는 직접 참조하면 성능과 관련된 여러가지 고민을 해야함(지연로딩, 즉시로딩)
    - 세 번째 문제는 확장이다. 초기에 단일 서버 단일 DBMS 서비스를 제공가능하지만 사용자가 몰리면 자연스럽게 부하를 분산하기 위해 하위 도메인별로 시스템을 분리하기 시작.
 이 과정에서 하위도메인마다 서로다른 DBMS를 사용할 때도 있다
  - 이런 세 가지 문제를 완화할 때 사용할 수 있는 것이 ID를 이용해서 다른 애그리거트를 참조하는 것
  - ID 참조를 사용하면 모든 객체가 참조로 연결되지 않고 한 애그리거트에 속한 객체들만 참조로 연결 
  - 이렇게 함으로서 물리적인 연결을 제거하여 모델의 복잡도는 낮추고 응집도를 높여주는 효과가 있다 
  - 추가로 다른 애그리거트를 직접 참조하지 않으므로 애그리거트 간 참조를 지연 로딩으로 할지 즉시 로딩으로 할지 고민하지 않아도 됨
    ``` java
    public class ChangeOrderService {
      
      @Transactional
      public void changeShippingInfo(OrderId id, ShippingInfo newShippingInfo, boolean userNeShippingAddrAsMemberAddr) {
        Order order = orderRepository.findbyId(id);
        if (order == null) throw new ...
        order.changeShippingInfo(newShippingInfo);
        if (userNewShippingAddrAsMemberAddr) {
          // ID를 이용해서 참조하는 애그리거트를 구한다
          Member member = memberRepository.findById(order.getOrderer().getMemberId());
          member.changeAddress(newShippingInfo.getAddress());
        }
      }
    }
    ...
    ```
    - 응용 서비스에서 필요한 애긜거트를 로딩하므로 애그리거트 수준에서 지연 로딩을 하는 것과 동일한 결과를 만듬
    - 외부 애그리거트를 직접 참조하지 않기 때문에 애초에 한 애그리거트에서 다른 애그리거트의 상태를 변경할 수 없다
    - 애그리거트별로 다른 구현 기술을 사용하는 것도 가능해진다 
      - 중요한 데이터인 주문 애그리거트는 RDBMS에 저장하고 조회 성능이 주용한 상품 애그리거트는 NoSQL에 저장 가능
### 3.4.1 ID를 이용한 참조와 조회 성능
  - 다른 애그리거트를 ID로 참조하면 참조하는 여러 애그리거트를 읽을 때 조회 속도가 문제 될 수 있다
    - 예를 들어 주문 목록을 보여주려면 상품 애그리거트와 회원 애그리거트를 함께 읽어야 하는데, 이를 처리할 때 다음과 같이 각 주문마다 상품과 회원 애그리거트를 읽어온다고 해보자
    - 한 DBMS에 데이터가 있다면 조인을 이용해서 한 번에 모든 데이터를 가져올 수 있음에도 불구하고 주문마다 상품 정보를 일거오는 쿼리를 실행하게된다
    - 이를 N+1조회문제라고 한다
  - N+1조회문제 해결방법
    - 전용 조인 쿼리를 만들어서 해결

## 3.5 애그리거트 간 집합 연관
  - 애그리거트 간 1:N과 M:N연관은 컬렉션을 이용한 연관이다.
  - 카테고리와 상품 간의 연관이 대표적이다.
  - 카테고리 입장에서 한 카테고리에 한 개 이상의 상품이 속할 수 있으니 카테고리와 상품음 1:N관계이다
  - 한 상품이 한 카테고리에만 속할 수 있다면 상품과 카테고리 관계는 N-1관계이다 

## 3.6 애그리거트를 팩토리로 사용하기
  - 고객이 특정 상점을 여러 차례 신고해서 해당 상점이 더 이상 물건을 등록하지 못하도록 차단한 상태라고 해보자.
  - 상품 등록 기능을 구현한 응용 서비스는 다음과 같이 상점 계정이 차단 상태가 아닌 경우에만 상품을 생성하도록 구현 가능
    ``` java
    public class RegisterProductService {
      public ProductId registerNewProduct(NewProductRequest req) {
        Store store = storeRepository.findById(req.getStoreId());
        checkNull(store);
        if(acount.isBlocked()) {
          throw new StoreBlockedException();
        }
        ProductId id = productRepository.nextId();
        Product product = new Product(id, store.getId(), ...생략);
        productRepository.save(product);
        return id;
      }
      ...
    }
    ```
    - Store가 Product를 생성할 수 있는지를 판단하고 Product를 생성하는 것은 논리적으로 하나의 도메인 기능인데 이 도메인 기능을 응용 서비스에서 구현하고 있는 것이다
    - Product를 생성하는 기능을 Store 애그리거트에 아래와 같이 옮겨보자!
    ``` java
    public class Store{
      
      public Product createProduct(ProductId newProductId, ...생략) {
        if (isBlocked()) throw new StoreBlockedException();
        return new Product(newProductId, getId(), ...생략);
      }
    }
    ```
    - Store 애그리거트의 createProduct()는 Product 애그리거트를 생성하는 팩토리 역할을 한다
    - 팩토리 역할을 하면서도 중요한 도메인 로직을 구현 

