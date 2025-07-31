# 함수형 프로그래밍(Functional Programming) 개념 정리

## 1. 함수형 프로그래밍이란?

함수형 프로그래밍은 프로그램을 \*\*순수 함수(Pure Function)\*\*의 조합으로 표현하는 프로그래밍 패러다임입니다.
여기서 "함수"란 수학적 함수처럼 입력이 정해지면 항상 같은 출력이 나오고, 외부 상태나 프로그램의 다른 부분에 영향을 주지 않는 것을 의미합니다.
핵심 철학은 **상태와 부수 효과(Side Effect)를 최소화**하고, \*\*데이터의 불변성(immutability)\*\*을 지키며, **함수 조합**을 통한 로직 구성을 지향합니다.

---

## 2. 함수형 프로그래밍의 주요 특징

### 2.1. **순수 함수(Pure Function)**

* **정의:** 동일한 입력값에 대해 항상 동일한 결과를 반환하며, 함수 외부의 상태를 변경하지 않는 함수입니다.
* **장점:** 예측 가능성, 테스트 용이성, 병렬 처리에 강함

```python
def add(x, y):
    return x + y
```

### 2.2. **불변성(Immutability)**

* **정의:** 데이터가 한 번 만들어지면 변경되지 않음. 모든 데이터 조작은 새로운 값을 반환.
* **장점:** 사이드 이펙트 감소, 동시성 프로그래밍에서의 안전성

```python
a = (1, 2, 3)
b = a + (4,)  # a는 변하지 않고, b는 새로운 값
```

### 2.3. **고차 함수(Higher-Order Function)**

* **정의:** 함수를 인자로 받거나, 함수를 반환하는 함수
* **활용:** 로직의 추상화, 재사용성 극대화

```python
def apply_fn(fn, value):
    return fn(value)

def square(x):
    return x * x

result = apply_fn(square, 3)  # 9
```

### 2.4. **함수 조합(Function Composition)**

* **정의:** 여러 개의 작은 함수를 조합해 새로운 함수를 만드는 기법

```python
def double(x): return x * 2
def increment(x): return x + 1

def composed(x): return double(increment(x))
```

### 2.5. **지연 평가(Lazy Evaluation)**

* **정의:** 실제로 값이 필요할 때까지 계산을 미룸
* **장점:** 성능 향상, 메모리 효율

```python
def numbers():
    n = 0
    while True:
        yield n
        n += 1

g = numbers()
next(g)  # 0
next(g)  # 1
```

### 2.6. **재귀(Recursion)**

* **정의:** 반복문 대신 자기 자신을 호출하여 반복을 구현
* **함수형 언어는 반복문 대신 재귀를 선호**

```python
def factorial(n):
    if n == 0:
        return 1
    return n * factorial(n-1)
```

### 2.7. **Side Effect(부수 효과) 최소화**

* **정의:** 함수가 외부 상태(변수, 파일, 네트워크 등)를 변경하거나 의존하지 않음
* **이점:** 버그 감소, 디버깅과 테스트 용이

---

## 3. 함수형 프로그래밍의 장점

* **코드의 예측 가능성**: 같은 입력이면 항상 같은 결과
* **병렬/동시 처리에 유리**: 상태 변경이 없으므로 race condition 문제 감소
* **테스트와 유지보수 용이**
* **로직의 모듈화와 재사용성 강화**

---

## 4. 함수형 프로그래밍의 한계와 현실적 고려사항

* 복잡한 상태 변경 로직에는 오히려 비효율적일 수 있음
* 너무 과도하게 적용하면, 오히려 코드가 읽기 어려워질 수 있음
* Python 같은 멀티 패러다임 언어에서는 함수형과 절차형/객체지향 스타일을 적절히 혼용하는 것이 실용적임

---

## 5. 함수형 프로그래밍의 대표 패턴 및 기법

| 패턴      | 설명                  | Python 예제                           |
| ------- | ------------------- | ----------------------------------- |
| map     | 컬렉션의 각 요소에 함수 적용    | `map(lambda x: x+1, [1,2,3])`       |
| filter  | 조건에 맞는 요소만 추출       | `filter(lambda x: x>1, [1,2,3])`    |
| reduce  | 누적 집계(aggregation)  | `reduce(lambda x, y: x+y, [1,2,3])` |
| lambda  | 익명 함수(간단한 함수 즉석 생성) | `lambda x: x+1`                     |
| partial | 인자가 고정된 함수 새로 만들기   | `partial(pow, exponent=2)`          |
| compose | 여러 함수를 합성           | 직접 구현 또는 라이브러리 사용                   |

---

## 6. 함수형 프로그래밍에 적합한 대표적 사례

* 데이터 파이프라인 및 스트림 처리
* 대량 데이터 집계, 변환, 분석
* 테스트가 중요한 업무 로직
* 멀티스레드/멀티프로세스 환경

---

## 7. 기타 참고

* Python 외에도 함수형 언어로는 Haskell, Clojure, Elixir, Scala 등이 있음
* Python에서도 함수형 패턴을 적용할 수 있지만, 완전한 함수형 언어만큼 철저하게 적용되진 않음

---

## 8. 예시: 데이터 변환 파이프라인 (map-filter-reduce)

```python
from functools import reduce

numbers = [1, 2, 3, 4, 5]
squared = map(lambda x: x * x, numbers)        # 각 원소 제곱
even = filter(lambda x: x % 2 == 0, squared)   # 짝수만 추출
result = reduce(lambda x, y: x + y, even)      # 합산

print(result)  # 20 (2^2 + 4^2)
```

---

# 결론

함수형 프로그래밍은 **함수 중심, 불변성, 부수 효과 최소화**를 통해
안정적이고 예측 가능한 소프트웨어를 만드는 데 목적이 있습니다.

