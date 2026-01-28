# Java 블로그 주제 #

### 동시성 / 멀티스레드
1. synchronized vs ReentrantLock
[synchronized와 ReentrantLock을 상황별로 비교해봤다]
* intrinsic lock vs explicit lock 차이
* 공정성(fair lock)
* tryLock / lockInterruptibly
* 실무에서 synchronized를 피하는 이유

2. volatile은 왜 원자성이 아닌가?
[volatile이 만능이 아닌 이유를 코드로 증명해보기]
* 메모리 가시성 vs 원자성
* CPU 캐시 개념
* count++이 왜 위험한지
* AtomicInteger로 해결

3. ThreadLocal을 잘못 쓰면 생기는 문제
[ThreadLocal을 잘못 쓰면 메모리 누수가 발생하는 이유]
* ThreadPool 환경
* remove() 안 했을 때 문제
* Spring에서 사용 사례
* 실제 장애 시나리오

### 컬렉션 내부 동작
1. HashMap은 왜 트리로 변하는가?
[HashMap은 왜 특정 순간부터 트리로 변할까? (Java 8)]
* 버킷 구조
* 충돌 발생 과정
* LinkedList → Red-Black Tree 전환 조건
* 성능 변화

2. ArrayList vs LinkedList 실제 성능 비교
[ArrayList와 LinkedList를 실제로 비교해보니]
* 접근 / 삽입 / 삭제 성능
* CPU 캐시 친화성
* 벤치마크 코드

3. HashMap에서 equals / hashCode 실수 사례
[equals만 오버라이드했을 때 실제로 터지는 버그]
* equals/hashCode 계약
* HashSet / HashMap 오작동
* 실무에서 발생한 문제 시나리오

### Java 핵심 개념
1. String은 왜 immutable인가?
[String은 왜 불변 객체로 설계됐을까]
* String Pool
* 보안 이슈
* 성능 이점
* StringBuilder와의 차이

2. JVM에서 객체는 어디에 생성되는가?
[객체는 항상 Heap에 생성될까? JVM 메모리 다시 보기]
* Stack / Heap / Metaspace
* Escape Analysis
* GC 관점

3. final / static / abstract 설계 관점
[final, static, abstract를 설계 관점에서 다시 생각해보자]
* 변경 가능성 차단
* 테스트 영향
* 객체지향 설계와의 관계

### 객체지향 & 설계
1. if-else 지옥을 어떻게 없앴는가
[if-else 지옥을 전략 패턴으로 제거해봤다]
* 문제 상황
* 인터페이스 분리
* 전략 패턴 적용
* 코드 가독성 개선

2. SOLID 원칙 적용기
[SOLID 원칙을 적용해보니 코드가 이렇게 바뀌었다]
* 적용 전 / 후 코드 비교
* 유지보수성 변화


### Spring 핵심
1. Spring은 왜 기본 Bean을 싱글톤으로 관리할까
[Spring은 왜 Bean을 싱글톤으로 관리할까?]
* 메모리
* 성능
* Stateless 설계
* 멀티스레드 환경

2. DI를 안 쓰면 테스트가 얼마나 괴로운가
[DI 없이 테스트해보니 왜 Spring을 쓰는지 알겠다]

* 강한 결합 문제
* Mock 테스트 어려움
* DI 도입 효과

### Spring MVC & 웹
1. 요청 하나가 Controller까지 가는 전 과정
[HTTP 요청 하나가 Controller에 도달하기까지]
* DispatcherServlet
* HandlerMapping
* ViewResolver

2. Filter vs Interceptor 차이
[Filter와 Interceptor를 동시에 쓰는 이유]

* 실행 시점
* 용도 차이
* 인증/로깅 사례

### JPA / Hibernate
1. N+1 문제 재현과 해결
[N+1 문제를 직접 재현하고 해결해보기]
* 문제 발생 원인
* fetch join
* 성능 비교

2. 왜 엔티티에 Setter를 열면 안 되는가
[엔티티에 Setter를 열면 생기는 문제들]
* 객체 무결성
* 변경 추적
* 도메인 설계

### 트랜잭션 & 예외
1. @Transactional이 private에 안 먹는 이유
[@Transactional이 private 메서드에 적용되지 않는 이유]
* 프록시 기반 AOP
* 호출 구조

2. 트랜잭션은 어디까지 묶어야 할까
[트랜잭션 범위를 어디까지 잡아야 할까]

### 테스트 & 실무 감각 (가산점)
1. 테스트하기 어려운 코드를 리팩토링해봤다
[테스트하기 어려운 코드를 리팩토링해봤다]

2. Spring 테스트가 느린 이유와 해결법
[Spring 테스트가 느린 이유와 해결 방법]