# MSA 정리 및 추가내용

### 모던 애플리케이션의 정의
빠른 개발 및 배포가 가능하고, 유지보수가 용이하며, 느슨하게 결합된 서비스들과 이벤트 중심의 구성 요소를 활용한다.  
따라서 수 많은 사용자들이 일관적이며 빠르고 안정적인 서비스를 제공받을 수 있다.  

**모던 애플리케이션 개발의 핵심**  
*최대한 작고 단순하게 유지하라*

1. 작고 독립적인 요소로 애플리케이션을 구축하면 전체적인 애플리케이션의 설계, 유지보수 및 관리가 쉬워진다. (쉽다 랑은 다르다)
2. 개발자의 생산성 극대화, 기능에만 몰두하며, 인프라와 CI/CD 에 대한 걱정이 필요가 없다. -> 서비스 통합(빌드)과 배포(출시)의 자동화

<br>

### 모놀리식 아키텍쳐
클라이언트 - 서버 - 데이터베이스가 하나의 프로젝트에 포함된 단일 코드 베이스 애플리케이션을 구성하는 설계 방법.
처음 구축되는 시스템의 가장 일반적인 모습이다.

**장점**  
- 단일 프로젝트기 때문에 구조가 단순하며, 배포가 쉽고, 디버깅이 용이하다.
- 규모가 작은 프로젝트고 사용자가 많지않은 서비스에서 효과적이다.

**단점**  
- 서비스가 커질경우 기능 확장 및 유지보수가 어렵다(서비스간 강한의존성) 
- 시스템에 장애가 발생할경우 모든 서비스에 영향이 발생한다. -> 데이터베이스 서버의 과부하 발생시 모든 서비스가 느려지거나 작동 X
- 여러 작업자가 작업한 내용을 병합하기 까다로우며, 시스템이 수정되는동안 다른 팀의 작업에 영향을 줄 수 있다 -> 상점상세 기능 업데이트를 위한 테스트 서버 재기동 시 로그인조차 안됨같은 개념인지..?

<br>

### 서비스 지향 아키텍쳐 (Service Oriented Architecture, SOA)
```
90년도 말에 상용화되기 시작한 소프트웨어 설계 유형
2000년도 말 유럽의 약 80% 기업이 SOA 형태로 소프트웨어를 설계한다고 한다.
```

// TODO: 서비스 지향 아키텍처 정리 

**한국에서 도입이 느렸던 이유?**  
느슨하게 결합된 비즈니스 도메인(소프트웨어로 해결해야할 문제들)을 식별하여 찾아낸 뒤, 어떤 클라이언트에서 사용할지 설계하는 과정이 필요하다.
개발은 잘하지만 분석과 설계에 능한 숙련된 경험자가 적었다고 한다.


### 마이크로 서비스 아키텍쳐 (Micro Service Architecture, MSA)
```
SOA와 아주 유사한 개념
```
하나의 거대한 시스템을 도메인별로 잘게 쪼갠뒤, 

**장점**  
- 서비스를 잘게 쪼개어, 기능 별 개발 및 유지보수를 용이하게하며, 서비스 별로 배포가가능하다.   
- 장애가 발생하더라도 해당 기능만 사용불가능할 뿐 나머지 기능은 정상적으로 동작한다.  
- 기존 API 방식과는 다르게 이벤트 기반으로 설계된 메시지 큐를 통해 (수신측?) 서비스에 장애가 발생하더라도 서비스가 정상화될 때 가져와서 복구할 수 있다는 장점이 있다.
- 구현적인 관점에서 서비스마다 다양한 기술들을 적용할 수 있다. -> 폴리글랏(polyglot) 프로그래밍


**단점**  
1. 느슨한 결합을 위한 서비스간의 경계를 명확하게 파악하기 어렵다. -> 설계의 어려움
2. 서비스간 통신 및 동기화가 어렵다.
3. 디버깅 및 오류 식별이 어렵다.


MSA 는 Component Based Development(CBD) 및 Service Oriented Architecture(SOA) 와 개념적으로 유사(거의 동일)하지만
SOA 는 구체적인 예시 및 표준화가 되어있지않고, 사내에서 만들어진 정책만이 있을뿐이다.

아마존, 넷플릭스, 배달의 민족 등 성공사례가 존재한다.

<br>

### 배달의 민족 MSA 적용기
배달의 민족은 하나의 거대한 모놀로식 시스템이였지만 해가 지날수록 급격한 성장으로 인한 장애가 빈번하게 발생하였다.  
특히 특정한 요일과 시간에만 트래픽이 몰리는 비즈니스 특성으로 인해 지속적인 모니터링 및 장애대응이 필요한 상황이였다.


- 2016년에 결제 시스템을 분리함.  
- 2017년에 가게, 메뉴, 정산 시스템 분리.  
- 2018년 하반기에 주문을 분리함.  

**기존 API 기반 데이터 전달**  

주문 도메인에서 주문 접수가 이루어지는경우
- 사장님 및 라이더스 시스템에 접수 이벤트 전달
- 새로운 시스템이 만들어지면서 과거의 데이터베이스 동기화 
- 앱 푸시 전송(ex: "리뷰 써주세요")을 위한 배달완료 이벤트 전달

문제점
1. 주문 시스템에서 리뷰 시스템에 배달완료 이번트 전달 중 리뷰 시스템 장애발생
2. 리뷰 시스템은 전달받은 이벤트가 없기때문에 앱 푸시를 날리지 않음

```
이러한 문제점을 해결하기위해 이벤트 기반으로 변경하여 데이터를 전송하도록 변경
```

**이벤트 기반 아키텍쳐의 예시**

- 전사에 주문 시스템에서 발생하는 이벤트(생성, 접수, 배달완료, 취소 등)을 규정함.
- 주문 시스템은 메세지 큐를 통해 이벤트만 발행하도록 변경
- 주문 시스템의 이벤트가 필요한 서비스마다 메세지 큐를 구독한뒤 컨슘

```
기존 API 방식은 시스템 장애발생 시 이벤트가 사라짐
이벤트 기반에서는 메시지 큐에 쌓인 데이터를 다시 구독한뒤 컨슘하면 복구가능
```

```
또한 주문 이벤트를 활용한 새로운 서비스가 필요할 때 구독만 하면 이벤트를 받아서 사용할 수 있다.
```


가게정보변경
1. 사장님이 가게정보변경을 요청하면 가게정보 서비스가 aws sns 로 변경 이벤트를 발행한다.
2. 광고리스팅 가게노출 검색 바로결제 시스템에서 메세지 큐를 이미 구독하고 있으며, 변경된 이벤트를 받고 업데이트를 진행한다.

<br>

### CQRS(Command and Query Responsibility Segregation: 명령 질의 책임 분리) 
```
사용자가 늘어날수록 데이터 조회 요청도 급격하게 늘어난다.
어떻게하면 많은 사람들이 정보를 조회해도 시스템에 과부하가 걸리지 않을까?
에서 시작한 아키텍쳐
```

조회(Query) 와 핵심 비즈니스 명령(Command) 를 철저하게 분리한 아키텍처  
조회와 명령을 분리함으로써 시스템의 부하를 줄이기 위함 

```
-> Rest API 로 바꾸어말하면 Get 과 Post, Put, Delete 로 분리?
```

광고나 가게/업주와 같은 서비스는 명령(Command)부로 안정성이 높은 RDBMS 시스템 사용, 
광고리스팅, 가게노출, 바로결제, 검색과 같은 고객과 직접적 맞닿아있는 서비스는 조회(Query)부로 성능이 높은 Redis나 MongoDB 사용.

**이벤트의 전파와 동기화**  
- Eventually Consistency (최종적 일관성) = 언젠가는 데이터는 서로 맞춰진다
- 데이터 싱크에는 최대 3초까지 소요
- 문제 발생시 해당 시스템이 이벤트만 재발행
- Zero Payload 전파 방식 사용
    - 이벤트에 식별자(ex: 가게ID)와 최소한의 정보만 발행
    - 이벤트를 받은 구독자가 조회 API로 필요한 데이터를 조회해서 저장

**Zero Payload의 장점**
```
Zero Payload 방식은 최소한의 정보만 발행하기 때문에
1. 수신측에 필요없는 데이터를 줄일 수 있다.
2. 송신측의 이벤트 구조변경에도 문제 발생의 여지가 없을 듯 하다.
3. 수신측에서 보낸 이벤트와 송신측에서 받은 이벤트의 순서 일치를 고민하지 않아도된다. -> 메시지 큐인데 이벤트 순서가 어떻게 일치하지 않을 수 있는거지?
```


다건 조회시 데이터 애그리게이트
= 주문조회 시 각각 쪼개진 샤드 서버에서 과거의 데이터들을 어떻게 조회할 것인가?
= CQRS 로 구현되어있기 때문에 조회는 MongoDB 에서

복잡한 이벤트 아키텍쳐
= 수많은 서비스에서 발생하는 이벤트들을 어떻게 



<br>

### MSA 와 조직문화
기존의 모놀리식 아키텍처를 사용하여 개발을 진행할 때는 서비스 개발간에 팀간의 의사소통이 필요함
시스템도 이에 영향을 받음
예시로 UI 개발, 서버개발, DB 개발, 개발 팀장이 서로간의 의사소통이 필요하고
또 개발 팀장간에 의사소통을 통해서 전반적인 개발흐름을 결정하며 다시 실무자에게 전파해야함
서비스 개발의 많은 시간이 의사소통에서 사용됨

MSA는 역할, 기술별로 팀이 분리되는 것이 아닌 업무기능


아마존 - 투 피자 팀?
피자 두 판으로 서로 빈번하게 의사소통하며,
함께 식사할 정도의 인원으로 수행
한팀에 다양한 역할과 기능을 만드는 사람들이 있음
수평적인 조직문화


MSA 를 단순하게 기술로만 생각하지말고 서비스의 개발은 조직의 다양한 사람들의 의사소통으로 진행되기때문에


<br>

### 추가 내용

Event Driven Architecture 가 Micro Service Architecture 와 함께 언급되는 이유는 뭘까?
MSA 의 핵심 키워드 중 `느슨한 결합` 과 연관이 있다.

각 마이크로 서비스는 서로 간 느슨한 결합을 통해 서로간의 의존도를 줄이고 각 서비스의 목적에 집중,
강한 응집력을 갖는 시스템을 만들 수 있다. Event Driven 은 이를 돕는다.

<br>

### 키워드
- 이벤트 기반 아키텍처
- 샤딩과 파티셔닝
- Web service
- SOAP(Simple Object Access Protocol)
- 카프카
- 도커와 컨테이너
- IDC 와 클라우드 서비스(ex: aws)
- 애자일과 워터풀

<br>


