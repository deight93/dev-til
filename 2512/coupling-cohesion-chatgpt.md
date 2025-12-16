>> 소프트웨어 설계중에, 응집도와 결합도에서
>> 응집도가 높게 (좋게) 개발이 되면, 보일러플레이트 코드나, 
>> 어떤 같은기능을 수행하는 코드가 중복코드로 발생 할 수 있지않아?

---
---

## 1. 응집도(Cohesion)

**응집도는 “하나의 모듈(클래스, 패키지, 서비스)이 얼마나 한 가지 책임에 집중되어 있는가”**를 의미합니다.
높을수록 좋습니다.

### 응집도의 단계 (나쁨 → 좋음)

1. **우연적 응집도 (Coincidental)**

    * 전혀 관련 없는 기능들이 한 클래스/모듈에 섞여 있음
    * 예: `UserUtil` 안에 로그인, 파일 업로드, 날짜 포맷 함수가 뒤섞임

2. **논리적 응집도 (Logical)**

    * 비슷해 보이는 기능을 조건문으로 분기
    * 예: `process(type)` 안에서 `if type == A/B/C`

3. **시간적 응집도 (Temporal)**

    * 같은 시점에 실행된다는 이유로 묶임
    * 예: `init()` 안에 로깅 설정, DB 연결, 캐시 초기화 전부 포함

4. **절차적 응집도 (Procedural)**

    * 실행 순서 때문에 묶임
    * 예: 단계 1 → 2 → 3이 하나의 함수에 존재

5. **통신적 응집도 (Communicational)**

    * 동일한 데이터를 사용해서 묶임
    * 예: 같은 `User` 객체를 다루는 여러 처리

6. **순차적 응집도 (Sequential)**

    * 한 작업의 출력이 다음 작업의 입력
    * 파이프라인 구조

7. **기능적 응집도 (Functional)**

    * **하나의 명확한 책임만 수행**
    * 가장 이상적
    * 예: `UserPasswordHasher`, `CreateOrderUseCase`

👉 실무에서 목표는 **기능적 응집도**입니다.

---

## 2. 결합도(Coupling)

**결합도는 “모듈 간 서로 얼마나 강하게 의존하는가”**를 의미합니다.
낮을수록 좋습니다.

### 결합도의 단계 (나쁨 → 좋음)

1. **내용 결합도 (Content Coupling)**

    * 다른 모듈의 내부 구현/변수에 직접 접근
    * 예: `otherService._internalMap.clear()`

2. **공통 결합도 (Common Coupling)**

    * 전역 변수, 전역 상태 공유
    * 예: 전역 싱글톤 mutable 객체

3. **외부 결합도 (External Coupling)**

    * 외부 포맷, 프로토콜에 강하게 의존
    * 예: CSV 포맷에 직접 의존한 로직

4. **제어 결합도 (Control Coupling)**

    * 플래그 값으로 동작 제어
    * 예: `doSomething(isAdmin=true)`

5. **자료 결합도 (Data Coupling)**

    * 필요한 데이터만 파라미터로 전달
    * 예: `createUser(name, email)`

6. **메시지 결합도 (Message Coupling)**

    * 인터페이스/메시지 기반 통신
    * 예: 인터페이스, 이벤트, 메시지 큐
    * 가장 이상적

---

## 3. 핵심 요약 (한 줄로)

* **응집도**: *한 모듈이 얼마나 “한 가지 일”만 잘하느냐* → **높을수록 좋음**
* **결합도**: *모듈들이 서로 얼마나 “몰라도 되느냐”* → **낮을수록 좋음**

---

## 4. 실무 관점에서 중요한 오해

### ❌ “클래스가 작으면 응집도가 높다”

* 아닙니다.
* 작은 클래스여도 여러 책임을 섞으면 응집도 낮습니다.

### ❌ “결합도는 무조건 0이 좋다”

* 불가능합니다.
* **의존하되, 추상화에 의존**해야 합니다.

---

## 5. 백엔드 실무 예시

### ❌ 나쁜 예

```python
class OrderService:
    def create_order(self, data, is_admin):
        if is_admin:
            # 관리자 로직
        else:
            # 일반 사용자 로직
```

* 응집도 낮음 (여러 책임)
* 제어 결합도 높음 (`is_admin` 플래그)

### ✅ 개선

```python
class CreateOrderUseCase:
    def execute(self, command: CreateOrderCommand):
        ...
```

```python
class AdminCreateOrderUseCase:
    def execute(self, command: CreateOrderCommand):
        ...
```

* 역할 분리 → 응집도 증가
* 플래그 제거 → 결합도 감소

---

## 6. 설계 판단 기준 (실전 기준)

다음 질문을 던지면 됩니다.

1. **이 클래스 이름에 “그리고(and)”가 들어가나?**

    * 들어가면 응집도 낮음

2. **이 메서드를 수정하면 몇 군데가 같이 깨지나?**

    * 많으면 결합도 높음

3. **이 로직을 다른 프로젝트로 그대로 옮길 수 있나?**

    * 못 옮기면 결합도 높음

---

네, **그럴 수 있습니다.**
그리고 이건 **설계가 틀린 게 아닙니다.**

결론부터 말씀드리면:

> **응집도를 높이면 “의도된 중복”은 반드시 생깁니다.
> 이 중복을 무조건 제거하려는 순간, 응집도가 다시 무너집니다.**

아래에서 정확히 정리해 드리겠습니다.

---
---

## 1. 왜 응집도가 높아지면 중복이 생기나

응집도가 높다는 건 **각 모듈이 하나의 책임만 가진다**는 뜻입니다.

그 결과로:

* 서로 다른 유스케이스
* 서로 다른 컨텍스트
* 서로 다른 변경 이유

를 가진 코드들이 **구조적으로 분리**됩니다.

### 예시 (의도된 중복)

```python
class CreateOrderUseCase:
    def execute(self, cmd):
        validate_user(cmd.user_id)
        save_order(cmd)
        publish_event("ORDER_CREATED")
```

```python
class AdminCreateOrderUseCase:
    def execute(self, cmd):
        validate_admin(cmd.admin_id)
        save_order(cmd)
        publish_event("ORDER_CREATED")
```

* `save_order`
* `publish_event`

이 부분은 **같아 보이지만**,
**유스케이스 관점에서는 다른 흐름**입니다.

👉 이걸 “중복 제거”한다고 합쳐버리면:

* 권한 분기
* 조건 플래그
* if/else 폭증

이 생기고, 응집도가 급격히 떨어집니다.

---

## 2. 중복에는 “나쁜 중복”과 “좋은 중복”이 있습니다

### ❌ 나쁜 중복 (반드시 제거)

* **알고리즘 중복**
* **비즈니스 규칙 중복**
* 한 군데 수정하면 반드시 다른 곳도 수정해야 하는 코드

```python
# 할인율 계산 로직이 여러 곳에 복붙
price = price - price * 0.1
```

→ 이런 건 **즉시 공통화**해야 합니다.

---

### ✅ 좋은 중복 (허용해야 함)

* **유스케이스 흐름의 유사성**
* **애플리케이션 레벨 orchestration 코드**
* 얇은 보일러플레이트

```python
class XxxUseCase:
    def execute(self, cmd):
        self._validate(cmd)
        self._save(cmd)
```

→ 이건 “중복”이 아니라 **구조적 반복**입니다.

---

## 3. DRY 원칙이 오히려 독이 되는 순간

DRY는 **“변경 이유가 하나일 때만”** 적용해야 합니다.

### 잘못된 DRY

```python
def create_order(cmd, is_admin):
    if is_admin:
        ...
```

* 서로 다른 변경 이유를 하나로 묶음
* 결과: 응집도 ↓, 결합도 ↑

### 올바른 판단 기준

> **“이 두 코드가 항상 같이 변경될 것인가?”**

* YES → 중복 제거
* NO → 중복 유지

---

## 4. 실무에서 쓰는 판단 기준 (아주 중요)

### 1️⃣ 중복 제거를 하기 전 질문

* 이 코드는 **도메인 규칙인가?**

    * 예 → 공통화
* 이 코드는 **유스케이스 흐름인가?**

    * 아니오 → 분리 유지

---

### 2️⃣ 계층별 중복 허용 정도

| 계층                               | 중복 허용   |
| -------------------------------- | ------- |
| Domain (Entity, Policy)          | ❌ 거의 불가 |
| Application (UseCase)            | ✅ 허용    |
| Interface (Controller, Resolver) | ✅ 매우 허용 |
| Infra (Repository impl)          | ⚠️ 상황별  |

---

## 5. “보일러플레이트가 많아지는 게 싫다”에 대한 솔직한 답

**정상입니다.**
특히 다음 구조에서는 더 그렇습니다.

* Clean Architecture
* Hexagonal
* DDD Lite
* CQRS

이 구조들의 공통점:

> **가독성과 변경 안정성을 위해 코드 양을 희생합니다.**

---

## 6. 그럼에도 불구하고 줄일 수 있는 것들

### 1️⃣ 패턴으로 통일 (복붙 X)

```python
class UseCase:
    def execute(self, cmd):
        self._validate(cmd)
        self._run(cmd)
```

→ 구조만 공통화
→ **로직은 절대 합치지 않음**

---

### 2️⃣ 공통 유틸과 정책 객체 분리

```python
class PricePolicy:
    def calculate(self, ...):
        ...
```

→ 유스케이스는 호출만 함

---

## 7. 한 줄 요약 (가장 중요)

* **응집도 ↑ → 코드 중복 ↑ : 정상**
* **중복 제거 집착 → 응집도 붕괴**
* **중복 제거 기준은 “변경 이유”**
* **보일러플레이트는 설계 비용이지, 악이 아님**
