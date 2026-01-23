# 객체지향에서의 역할·상속·의존성

Service / Repository / Controller부터 DI까지

개발을 하다 보면 이런 고민을 하게 된다.
이건 extends로 상속해야 하나?
아니면 implements?
추상 클래스랑 인터페이스는 언제 나눠 쓰지?
Service, Repository 역할은 왜 나뉘어 있지?

어느 시점에 어떻게 사용하는게 좋은 프로그래밍일까


=======









##객체지향에서 각 계층의 역할

### Controller
요청(Request)을 받는다
Service에 일을 시킨다
응답(Response)을 반환한다

class ExportController {
    public function export(ExportService $service) {
        return $service->handle();
    }
}



### Service
비즈니스 로직 담당
여러 객체를 조합해서 “일”을 수행

class ExportService {
    public function __construct(private Exportable $export) {}

    public function handle() {
        return $this->export->export();
    }
}

### Repository
데이터 접근 전용
DB 쿼리 책임

class EvidenceRepository {
    public function findAll() {
        // DB query
    }
}


Service는 Repository를 사용하고
Repository는 Service를 몰라야 한다

## 상속(extends)은 언제 쓰는가
“개념적으로 같은 종류”이고
공통 구조 + 공통 동작이 명확할 때

좋은 상속 예)
abstract class BaseExcelExport {
    public function chunkSize() {
        return 1000;
    }
    abstract protected function baseQuery();
}

class EvidenceExport extends BaseExcelExport {}


* 둘 다 “Excel Export”라는 같은 도메인
* 기본 동작이 명확
* 구현 강제 필요

나쁜 상속 예)
class OrderService extends PaymentService {}


* Order ≠ Payment
* 개념적으로 같은 종류 아님
* 이건 의존성(DI) 으로 풀어야 함

## 의존성(DI)은 언제 쓰는가
다른 역할을 수행하는 객체를 사용해야 할 때
실무에서 거의 전부 이 케이스

Service → Repository
Service → 외부 API
Controller → Service
교체 가능성이 있는 구현체

*  DI 아님 (강한 결합)
class OrderService {
    public function pay() {
        $gateway = new KakaoPay();
        $gateway->pay();
    }
}


교체 ❌
테스트 ❌
수정 범위 큼

✅ DI (정답)
class OrderService {
    public function __construct(PaymentGateway $gateway) {
        $this->gateway = $gateway;
    }
}


구현체 교체 ⭕
테스트 ⭕
유지보수 ⭕

* DI의 핵심은 단 하나
 “new를 내가 하지 않는다”

## implements는 DI가 아니다 (중요)
class KakaoPay implements PaymentGateway {}

역할 선언
의존성은 ‘사용 관계’

class OrderService {
    public function __construct(PaymentGateway $gateway) {}
}

## abstract class vs interface 차이
* interface
“무엇을 할 수 있는가”
구현 없음
다중 구현 가능

interface Exportable {
    public function export(): array;
}

* abstract class

“공통 구현 + 강제 규칙”
일부 구현 가능
단일 상속

abstract class BaseExport implements Exportable {
    public function export(): array {
        return $this->baseQuery();
    }

    abstract protected function baseQuery(): array;
}

## 실무 판단 공식 (이거 기억하면 된다)
Q1. 같은 종류인가?

YES → 상속
NO → 의존성

Q2. 교체 가능성 있나?

YES → interface + DI
NO → 상속 가능

Q3. 공통 로직 있나?

YES → abstract
NO → interface

## 실무에서 가장 많이 쓰는 조합 ⭐
interface   → 역할 정의
abstract    → 공통 구현
DI          → 실제 사용

## 실제로 맞물려 동작하는 예시
① 역할 정의 (interface)
interface Exportable {
    public function export(): array;
}

* 공통 틀 (abstract)
abstract class BaseExport implements Exportable {
    public function export(): array {
        return $this->baseQuery();
    }

    abstract protected function baseQuery(): array;
}

* 실제 구현
class EvidenceExport extends BaseExport {
    protected function baseQuery(): array {
        return [
            ['id' => 1, 'title' => '증거1'],
            ['id' => 2, 'title' => '증거2'],
        ];
    }
}

class NoticeExport extends BaseExport {
    protected function baseQuery(): array {
        return [
            ['id' => 10, 'title' => '공지1'],
        ];
    }
}

*서비스 (의존성 사용)
class ExportService {
    public function __construct(private Exportable $export) {}

    public function handle(): void {
        $data = $this->export->export();
        print_r($data);
    }
}

* 실행 흐름
$export = new EvidenceExport();
$service = new ExportService($export);

$service->handle();

* 실행 순서
ExportService::handle()
 → Exportable::export()
   → BaseExport::export()
     → EvidenceExport::baseQuery()
다형성 완성

## 이 구조의 장점
항목	효과
확장성	새 Export 추가 시 기존 코드 수정 ❌
테스트	Mock Exportable 가능
유지보수	책임 분리
가독성	역할이 명확

* 요약)
상속은 ‘틀을 확장’할 때
의존성(DI)은 ‘역할을 사용’할 때

그리고
DI는 “new를 내가 하지 않는 것”

* 구조의 이름 (실무 패턴)
Template Method Pattern
Strategy Pattern
Dependency Injection