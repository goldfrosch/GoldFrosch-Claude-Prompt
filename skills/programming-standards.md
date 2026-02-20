---
name: programming-standards
description: "This document defines the fundamental development principles that AI must follow when generating code, suggesting architectural decisions, or proposing refactoring. The philosophy behind this document is to minimize structural complexity, strictly control internal state, prioritize readability over premature optimization, and introduce architectural complexity only when it becomes clearly necessary. The objective is to build systems that are simple, predictable, maintainable, and capable of evolving over time without being burdened by unnecessary abstraction or over-engineering."
---

# 0. 핵심 정체성

이 문서는 최소 구조와 상태 통제를 중심으로 하는 개발 철학을 정의한다.  
AI는 코드 작성, 구조 설계, 리팩토링 제안 시 아래 규칙을 우선순위 기반 의사결정 기준으로 적용해야 한다.

---

# 1. 구조 최소화 규칙 (Structure Minimization Rule)

## 원칙

구조는 반드시 필요한 수준까지만 유지한다.  
복잡성은 문제를 해결할 때만 추가한다.

## AI 행동 지침

- 불필요한 폴더 depth 증가 제안 금지
- 필요하지 않은 추상화 레이어 도입 금지
- 패턴 적용 자체가 목적이 되는 설계 제안 금지
- 복잡한 DI 구조를 기본값으로 제안하지 말 것
- 싱글톤으로 충분히 해결 가능한 경우 추가 레이어 생성 금지

## 판단 기준

현재 해결해야 할 명확한 문제가 없다면 구조를 추가하지 않는다.

---

# 2. 상태 통제 규칙 (State Control Rule)

## 원칙

객체의 내부 상태는 외부에서 직접 변경되어서는 안 된다.  
상태는 반드시 객체 스스로 통제해야 한다.

## AI 행동 지침

- 모든 필드는 기본적으로 private
- 외부 접근은 getter
- 외부 변경은 setter 또는 명시적 메서드
- public field 제안 금지
- 외부에서 직접 상태를 변경하는 코드 제안 금지

## 추가 지침

Setter는 단순 값 대입이 아닌, 필요 시 유효성 검사를 포함할 수 있어야 한다.

---

# 3. 상태 확장 규칙 (Bool → Enum 전략)

## 원칙

하나의 bool 상태가 확장될 조짐이 보이면  
bool을 추가하기 전에 enum으로 통합하는 방안을 우선 고려한다.

## AI 행동 지침

- 관련된 bool이 2개 이상 필요해지는 상황을 감지
- 상태 조합이 의미를 가지기 시작하면 enum 제안
- 즉시 구조 변경하지 말고 먼저 확인 요청

## 중요

확장 가능성은 고려하되, 과설계는 금지한다.

---

# 4. Early Return 규칙

## 원칙

중첩을 줄이고 코드 흐름을 직선화한다.

## AI 행동 지침

- Guard clause 형태의 early return 우선 제안
- 불필요한 else 블록 제거
- 깊은 중첩 구조 지양
- 한 눈에 흐름이 보이도록 구성

---

# 5. 가독성 우선 규칙 (Readability Over Optimization Rule)

## 원칙

최적화보다 읽기 쉬운 코드가 우선이다.

## AI 행동 지침

- 마이크로 최적화 기본 제안 금지
- 이해하기 어려운 트릭 코드 금지
- 성능 병목이 확인되기 전까지 단순 구현 유지
- 구조 복잡도를 올려야 하는 최적화는 자동 제안 금지

## 판단 기준

실제 병목이 확인되기 전까지는 단순성이 우선이다.

---

# 6. 점진적 리팩토링 규칙 (Progressive Refactoring Rule)

## 원칙

리팩토링은 상시 수행하지 않는다.  
필요성이 체감되는 시점에 수행한다.

## AI 행동 지침

- 매 작업마다 리팩토링 강요 금지
- 대규모 구조 변경 전 반드시 허락 요청
- 전면 재작성 제안 금지
- “지금은 유지, 이후 개선 가능” 형태 제안 선호

---

# 7. 복잡도 상승 승인 규칙 (Complexity Escalation Permission Rule)

## 원칙

구조 복잡도 상승은 명확한 필요성과 승인 기반으로 한다.

## AI 행동 지침

다음 항목들은 자동 도입 금지:

- 추가 추상 레이어
- 추상 클래스 체계 도입
- 과도한 인터페이스 분리
- 이벤트 버스 구조
- 무거운 DI 프레임워크

필요 시 선택지로만 제시한다.

---

# 8. 커뮤니케이션 규칙 (Communication Rule)

## 원칙

위로보다 명확한 해결책을 우선한다.

## AI 행동 지침

- 불필요한 칭찬 최소화
- 모르면 솔직히 명시
- 추측 금지
- 이유 중심 설명
- 결론만 제시하지 말고 판단 근거 제시

---

# 9. 기본 의사결정 우선순위

판단이 모호할 경우 다음 순서를 따른다:

1. 가독성
2. 상태 통제
3. 구조 최소화
4. 확장 가능성
5. 최적화

---

# 10. 한 줄 철학

> 확장을 염두에 두되 지금은 단순하게.  
> 상태는 통제하고, 복잡성은 필요할 때만 올린다.
