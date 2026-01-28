# Java 블로그 주제 #

## 웹 크롤링

## 동시성 / 멀티스레드
1. synchronized vs ReentrantLock
[synchronized와 ReentrantLock을 상황별로 비교해봤다]
* intrinsic lock vs explicit lock 차이
* 공정성(fair lock)
* tryLock / lockInterruptibly

2. ThreadLocal을 잘못 쓰면 생기는 문제
[ThreadLocal을 잘못 쓰면 메모리 누수가 발생하는 이유]
* ThreadPool 환경
* remove() 안 했을 때 문제
* Spring에서 사용 사례
* 실제 장애 시나리오


---------------------------------------------------------------------------


### Java 동시성 기초 – synchronized vs ReentrantLock (공유 자원에 동시에 접근하는 것을 제어)
여러 스레드가 동시에 같은 데이터를 수정하면 예상치 못한 결과가 나올 수 있습니다.
예: 은행 계좌 프로그램

Thread A: 잔액 읽음 → 1000원
Thread B: 잔액 읽음 → 1000원
Thread A: +500 → 1500원 저장
Thread B: -200 → 800원 저장  // ???!


→ 이런 현상을 **race condition(경쟁 상태)**라고 합니다.

이 글에서는 synchronized와 ReentrantLock을 통해
멀티스레드 환경에서 안전하게 데이터를 다루는 방법을 그림과 코드로 알아봅니다.

1. synchronized – 자동 잠금
synchronized는 자바가 제공하는 자동 잠금 장치입니다.
JVM이 자동으로 잠금/해제
메서드 단위, 코드 블록 단위로 사용 가능
장점: 문법 간단
단점: 세밀한 제어 어려움, Deadlock 가능

그림으로 이해하기
[Thread A] ─┐
             │
         ┌───▼───┐
         │ 잠금    │
         │ balance│
         └───▲───┘
             │
[Thread B] ─┘

Thread A가 먼저 들어가면 Thread B는 기다림
자동으로 잠금이 걸리고 풀림

사용 예시
public class BankAccount {
    private int balance = 0;

    public synchronized void deposit(int money) {
        balance += money; // 동시에 한 스레드만 접근 가능
    }
}

2. ReentrantLock – 직접 잠금
ReentrantLock은 개발자가 직접 잠금/해제를 제어하는 장치입니다.
장점: 공정성(Fair Lock) 가능
tryLock: 잠금 시도 후 실패 시 다른 작업 가능
lockInterruptibly: 스레드 인터럽트 가능
단점: unlock 안 하면 Deadlock 발생 → 반드시 finally에서 해제

그림으로 이해하기
Thread A ──lock()────▶ [Critical Section] ──unlock()────▶ 완료
Thread B ──tryLock()─┐
                     └─> 실패하면 다른 작업


사용 예시
import java.util.concurrent.locks.ReentrantLock;

public class BankAccount {
    private int balance = 0;
    private final ReentrantLock lock = new ReentrantLock();

    public void deposit(int money) {
        lock.lock();
        try {
            balance += money; // critical section
        } finally {
            lock.unlock(); // 반드시 해제
        }
    }
}

3. 비교 표
항목	synchronized	ReentrantLock
잠금 방식	JVM 자동	개발자 직접
공정성(Fair)	불가	가능
시도 후 포기	불가	가능(tryLock)
인터럽트 지원	불가	가능(lockInterruptibly)
코드 간결성	간단	조금 복잡

비유
synchronized → 문에 자물쇠 하나, 들어오려면 기다림
ReentrantLock → 개인 열쇠, 상황 따라 잠그거나 안 잠글 수 있음



ThreadLocal (스레드별 독립 데이터 관리, 스레드 안전하게 동작하게 함)
ThreadLocal: 스레드마다 자기 전용 공간을 갖는 변수
다른 스레드와 값을 공유하지 않음
주로 요청 단위 데이터 관리, 트랜잭션 정보, 로그 MDC 등에 사용

핵심: 여러 스레드가 동시에 실행될 때, 스레드별로 독립적인 변수 공간 제공
공유 자원 접근과 직접적으로 경쟁하지 않지만, 멀티스레드 환경에서 안전하게 데이터 관리가 목적
스레드 풀 환경에서 잘못 사용하면 메모리 누수나 데이터 섞임 문제가 발생 → 동시성 관련 실무 문제

ThreadLocal을 잘못 쓰면 생기는 문제
ThreadPool 환경: 스레드 재사용 → 이전 값이 남아 있음
remove() 안 했을 때: 메모리 누수 발생
Spring 사용 사례: RequestContextHolder, TransactionSynchronizationManager

===========================================

## 컬렉션 내부 동작
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

---------------------------------------------------------------------------
### Java 컬렉션과 내부 동작 이해
1. String 참조자료형과 String Pool
Java에서 String은 immutable(변경 불가) 자료형입니다.
String을 바꾸면 새 객체가 생성됨
메모리를 효율적으로 관리하기 위해 String Pool을 사용합니다.

String Pool 이해
String a = "hello";
String b = "hello";
String c = new String("hello");

System.out.println(a == b); // true → 같은 Pool 객체
System.out.println(a == c); // false → 새 객체
System.out.println(a.equals(c)); // true → 값은 같음

== : 참조 비교 → Pool 여부 확인 가능
equals() : 값 비교
💡 면접 포인트
String은 immutable → HashMap key로 안전하게 사용 가능
Pool과 new 생성 차이 이해
hashCode()와 equals()가 컬렉션 동작에 어떻게 영향을 주는지 설명 가능

2. HashMap은 왜 트리로 변하는가? (Java 8+)
HashMap은 내부적으로 버킷(bucket) 구조를 사용하며, **충돌(Collision)**이 발생하면 LinkedList로 연결합니다.
Java 8 이후, 충돌이 많은 버킷은 LinkedList → Red-Black Tree로 변환
이유: 성능 최적화 (검색 시간 O(n) → O(log n))

구조 예시
Bucket 0: [key1=value1] → [key2=value2] → ...
Bucket 1: [key3=value3] (충돌 많으면 트리로 변환)

트리 변환 조건
한 버킷에 8개 이상의 엔트리가 연결되어 있고,
전체 HashMap 크기가 64 이상일 때

💡 면접 포인트
HashMap 내부 구조 이해
충돌 처리와 성능 변화 설명 가능
equals/hashCode 중요성 강조 가능

3.  ArrayList vs LinkedList 성능 비교
1) 접근 성능
ArrayList: 인덱스 접근 O(1)
LinkedList: 순차 탐색 O(n)

2) 삽입/삭제
ArrayList: 중간 삽입/삭제 O(n) → 요소 이동 필요
LinkedList: 노드 연결 변경 O(1) (참조만 바꿈)

3) CPU 캐시 친화성
ArrayList는 연속된 메모리 배열 → CPU 캐시 친화적 → 반복 접근 빠름
LinkedList는 메모리 단편화 → 접근 시 느림

벤치마크 코드 예시
List<Integer> arrayList = new ArrayList<>();
List<Integer> linkedList = new LinkedList<>();

// 1000000개 데이터 삽입 성능 비교
long start = System.nanoTime();
for(int i=0;i<1000000;i++) arrayList.add(i);
System.out.println("ArrayList: " + (System.nanoTime()-start));

start = System.nanoTime();
for(int i=0;i<1000000;i++) linkedList.add(i);
System.out.println("LinkedList: " + (System.nanoTime()-start));


===========================================


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

===========================================

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

===========================================

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

===========================================

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

===========================================

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

===========================================

### 트랜잭션 & 예외
1. @Transactional이 private에 안 먹는 이유
[@Transactional이 private 메서드에 적용되지 않는 이유]
* 프록시 기반 AOP
* 호출 구조

2. 트랜잭션은 어디까지 묶어야 할까
[트랜잭션 범위를 어디까지 잡아야 할까]

===========================================

### 테스트 & 실무 감각 (가산점)
1. 테스트하기 어려운 코드를 리팩토링해봤다
[테스트하기 어려운 코드를 리팩토링해봤다]

2. Spring 테스트가 느린 이유와 해결법
[Spring 테스트가 느린 이유와 해결 방법]