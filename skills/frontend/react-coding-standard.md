---
name: frontend-architecture-and-typescript-standards
description: "Frontend architecture and TypeScript standards focused on design decision criteria, system predictability, URL-driven state, data separation, and progressive complexity control."
---

# Frontend Architecture & TypeScript Standards

Standards focused on design reasoning and architectural decision criteria for React and TypeScript applications.

---

## Core Philosophy

- URL은 화면 상태를 설명하는 1차 기준이다.
- UI는 상태의 표현 결과일 뿐이다.
- 서버 상태와 클라이언트 상태는 본질적으로 다르다.
- 영리한 추상화보다 예측 가능한 구조가 우선이다.
- 구조는 필요성이 증명되기 전까지 최소한으로 유지한다.
- 실험 영역은 핵심 도메인 로직과 분리한다.
- 확장은 고려하되, 미리 복잡성을 도입하지 않는다.

이 문서는 단순한 코드 작성 규칙이 아니라,  
프론트엔드 설계를 판단하는 기준을 정의한다.

---

## URL & Routing Policy

### 설계 기준

- URL은 현재 화면의 상태를 설명할 수 있어야 한다.
- 동일한 컴포넌트는 URL에 따라 데이터만 달라지도록 설계한다.
- 화면 상태를 전역 상태로 관리하기 전에 URL로 표현 가능한지 먼저 판단한다.
- 필터, 정렬, 페이지네이션은 URL로 표현하는 것을 우선 고려한다.

### 판단 질문

- 이 상태는 새로고침 시 유지되어야 하는가?
- 이 상태는 공유 가능한가?
- 이 상태는 브라우저 히스토리와 함께 동작해야 하는가?

위 질문 중 하나라도 “예”라면 URL 기반 설계를 우선한다.

---

## Data Fetching Policy

### 서버 상태와 클라이언트 상태 구분

- 서버 상태는 원격 소스의 반영이다.
- 클라이언트 상태는 UI 표현과 사용자 상호작용을 위한 상태이다.
- 둘을 혼합하지 않는다.

### queryKey 설계 기준

- queryKey는 URL 기반으로 결정한다.
- queryKey는 화면 상태를 명확히 설명해야 한다.
- 마케팅/실험 조건이 있다면 queryKey 분리를 고려한다.

### 캐싱 전략 판단 기준

- 데이터의 변경 빈도는 어느 정도인가?
- 사용자 체감 성능이 중요한가?
- 동일 데이터 재요청 가능성이 높은가?

### Prefetch 전략

- 사용자가 이동할 가능성이 높은 경로만 prefetch한다.
- 무분별한 prefetch는 지양한다.
- 마케팅 페이지 등 변동이 잦은 영역은 신중히 판단한다.

---

## State Management Policy

### 로컬 상태 우선 원칙

- 가능한 한 컴포넌트 내부 상태로 관리한다.
- 전역 상태는 최소화한다.
- 상태를 올리기 전에 컴포넌트 경계를 재검토한다.

### 전역 상태 판단 기준

- 여러 독립된 영역에서 동일 데이터가 필요한가?
- URL로 표현하기 어려운가?
- 서버 상태와 무관한 UI 전용 상태인가?

위 조건을 만족하지 않으면 전역 상태 도입을 지양한다.

---

## Component Design Rule

### 설계 기준

- 컴포넌트는 역할 중심으로 설계한다.
- 데이터 처리와 UI 표현을 논리적으로 분리한다.
- Props는 명확하고 예측 가능해야 한다.
- 파생 상태를 중복 저장하지 않는다.

### 판단 질문

- 이 컴포넌트는 재사용이 목적이 아니라 명확성이 목적인가?
- 추상화를 추가함으로써 이해 비용이 증가하지 않는가?
- 데이터 흐름이 단방향으로 유지되는가?

---

## TypeScript Policy

### 타입 설계 기준

- any 사용 금지.
- boolean 확장 가능성이 있다면 union type을 우선 고려한다.
- 의미 있는 상태는 명시적 타입으로 표현한다.
- 추상 타입보다 구체적 타입을 우선한다.

### interface vs type 기준

- 객체 구조 확장이 예상되면 interface.
- union, intersection, 조합 타입은 type 사용.
- 추상화 목적이 아닌 표현 목적의 타입을 우선한다.

### 설계 질문

- 이 타입은 실제 도메인 개념을 표현하는가?
- boolean으로 충분한가, 아니면 상태 집합인가?

---

## Hydration & SSR Policy

### 설계 기준

- 서버와 클라이언트의 초기 상태는 일관되어야 한다.
- 랜덤 값, 시간 기반 값, 브라우저 전용 API 사용은 hydration 불일치 가능성을 만든다.
- 클라이언트 전용 로직은 명확히 분리한다.

### 판단 질문

- 이 값은 서버에서 계산 가능한가?
- 서버와 클라이언트에서 결과가 달라질 가능성이 있는가?
- hydration mismatch 발생 시 원인을 설명할 수 있는가?

---

## Performance Policy

### 기본 원칙

- Premature optimization 금지.
- React.memo 남용 금지.
- useMemo, useCallback은 실제 re-render 비용이 확인된 경우 사용.
- key는 안정적으로 유지한다.

### 판단 기준

- 실제 렌더 비용이 병목인가?
- 최적화로 복잡도가 증가하는가?
- 측정 없이 최적화를 시도하고 있지 않은가?

---

## Experiment & Marketing Isolation Policy

### 설계 기준

- 마케팅 영역은 핵심 도메인 로직과 분리한다.
- 실험 코드는 제거 가능하도록 작성한다.
- 하드코딩이 필요한 경우 격리된 영역에서 수행한다.
- 공통 로직과 실험 영역은 명확히 분리한다.

### 판단 질문

- 이 코드는 장기 유지 대상인가?
- 실험 종료 후 쉽게 제거 가능한가?
- 도메인 로직을 오염시키고 있지 않은가?

---

## Complexity Escalation Rule

### 기본 원칙

- 구조는 최소 단위로 유지한다.
- 추상화는 필요할 때만 도입한다.
- 레이어 추가 전 반드시 문제를 정의한다.
- "확장 가능성"만으로 구조를 복잡하게 만들지 않는다.

### 판단 질문

- 지금 이 복잡성이 실제 문제를 해결하는가?
- 단순한 방식으로 해결 가능한가?
- 미래 가능성만을 이유로 추상화하고 있지 않은가?

---

## Change Strategy

기능 추가 시 스스로에게 묻는다:

1. 이 상태는 URL로 표현 가능한가?
2. 전역 상태가 정말 필요한가?
3. 서버 상태와 클라이언트 상태가 섞이고 있지 않은가?
4. 실험 코드가 도메인 로직에 침투하고 있지 않은가?
5. boolean이 늘어나고 있지 않은가?
6. 추상화를 도입할 명확한 이유가 있는가?
7. 구조가 과도하게 깊어지고 있지 않은가?

---

## One-Line Philosophy

예측 가능한 시스템을 구축한다.  
상태는 URL로 설명한다.  
관심사는 명확히 분리한다.  
복잡성은 필요할 때만 증가시킨다.
