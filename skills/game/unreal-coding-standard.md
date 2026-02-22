---
name: unreal-cpp-coding-standards
description: "Unreal Engine C++ coding standards based on Minimal Control Driven Development philosophy: Structure Minimization, State Control, Enum Strategy, Delegate-Driven UI, Plugin Reuse Policy, Pointer Policy, DataTable Helper Strategy, Progressive Refactoring, Dependency Control, Naming Convention, Change Strategy"
---

# Unreal C++ Coding Standards

Standards for readable, maintainable Unreal Engine C++ code based on Minimal Control Driven Development philosophy.

---

## Core Philosophy

- Unreal Engine는 실행 환경일 뿐, 게임 로직의 중심이 아니다.
- 구조는 최소 단위로 유지한다.
- 상태는 반드시 객체가 통제한다.
- 확장을 고려하되 과설계하지 않는다.
- 복잡성은 필요할 때만 증가시킨다.
- 이미 만들어둔 시스템이 있다면 새로 만들기보다 재사용을 우선한다.

---

## Plugin Reuse Policy

### 기본 원칙

- 기존에 제작한 플러그인이 있다면 우선적으로 참조한다.
- 동일 기능을 중복 구현하지 않는다.
- 기능 추가 시, 기존 플러그인 확장이 가능한지 먼저 검토한다.
- 플러그인의 책임 범위를 침범하지 않는다.
- 공통 기능은 프로젝트 내부 클래스보다 플러그인 레벨에 위치시키는 것을 우선 고려한다.

### 플러그인 문서화 규칙

- 프로젝트 루트 디렉토리 내 `/plugins` 폴더를 유지한다.
- 각 플러그인마다 별도의 `.md` 문서를 작성한다.
- 해당 문서에는 다음 정보를 포함한다:
  - 플러그인 목적
  - 제공 기능 목록
  - 주요 클래스 구조
  - 사용 방법
  - 확장 가능 지점
- 플러그인 관련 정보는 코드가 아닌 문서로 우선 파악 가능해야 한다.
- 새로운 기능을 만들기 전 `/plugins` 디렉토리를 먼저 확인한다.

### 판단 기준

새로운 클래스를 만들기 전, 기존 플러그인으로 해결 가능한지 먼저 판단한다.

---

## Encapsulation Strategy

### 기본 원칙

- 웬만한 값 정의는 private으로 둔다. (멤버 변수, 설정값, 내부 상태 등)
- 외부 읽기 접근은 Getter를 통해서만 허용
- 외부 변경은 명시적 Setter 또는 상태 변경 메서드를 통해서만 허용
- 외부에서 직접 값 변경 금지

### Getter 규칙

- Getter는 기본적으로 const로 선언한다.
- const로 선언이 불가능한 경우, 별도의 non-const Getter를 명시적으로 분리한다.
- 내부 상태 변경이 발생하는 Getter는 지양한다.

---

## Naming Convention

### 변수 규칙

- 모든 변수는 Pascal Case로 작성한다.
- Unreal의 b prefix 규칙을 따르되 Pascal Case 유지한다.
- private 멤버 또한 Pascal Case로 통일한다.
- 매직 넘버 사용 금지.
- 역할 중심 네이밍을 사용한다.

### 타입 규칙

- enum은 E prefix
- struct는 F prefix
- UObject 계열은 U prefix
- Actor 계열은 A prefix
- 인터페이스는 I prefix
- Delegate는 On + Verb 형식 사용

### C++ 파일 규칙

- C++ 소스/헤더 파일 생성 시 파일명 prefix에 프로젝트명을 붙이지 않는다.
- 예: `ProjectAnvilCraftingSubsystem.h` 대신 `CraftingSubsystem.h` 사용.

### 함수 인자 규칙 (const reference)

- **구조체/클래스 타입** (FString, FName, FText, FVector 등)은 인자를 **const reference**로 받는다.
  - 예: `void SetRowName(const FName& InRowName);`, `void LoadTable(const FString& Path);`
- **원시 타입** (int, float, bool, uint8 등)은 값으로 전달해도 된다.
- 복사 비용을 줄이고 불필요한 임시 객체 생성을 피하기 위함.

---

## UPROPERTY Exposure Policy

### 기본 원칙

- public UPROPERTY 사용 금지
- 런타임 상태 값은 에디터에 노출하지 않는다.
- 설정값만 노출한다.
- EditAnywhere 사용 최소화
- BlueprintReadWrite 최소화

### 노출 허용

- 밸런스 수치
- 데이터 에셋 참조
- 프리팹/이펙트/사운드 레퍼런스
- 튜닝 파라미터

### 노출 금지

- 런타임 상태 값
- 내부 캐싱 값
- 계산 결과
- 시스템 내부 플래그

### UCLASS / UFUNCTION 최소화

- 불필요한 Category, Blueprintable 등은 작성하지 않는다.
- 최대한 C++에서 처리할 수 있는 것은 블루프린트 노출을 늘리지 않는다.
- Blueprintable, BlueprintReadWrite, Category 지정은 필요한 경우에만 사용한다.

---

## UI Widget Binding Policy

### 기본 원칙

- UI 위젯 바인딩 시에는 Optional이 아닌 BindWidget을 사용한다.
- BindWidgetOptional은 사용하지 않는다. 바인딩할 위젯은 반드시 블루프린트에 존재해야 한다.

---

## Pointer Policy

### 기본 원칙

- 원시 포인터 사용을 최소화한다.
- Unreal에서 제공하는 포인터 타입을 우선 사용한다.
- UObject 계열은 TObjectPtr 사용을 기본으로 한다.
- 공유 소유가 필요한 경우 TSharedPtr 사용을 고려한다.
- 소유권이 없는 참조는 TWeakPtr 또는 TWeakObjectPtr 사용을 고려한다.
- 포인터 소유권이 불명확한 설계는 지양한다.

### 설계 기준

- 소유권이 어디에 있는지 명확히 정의한다.
- 포인터 수명 주기를 설명 가능해야 한다.
- GC 관리 대상과 비대상 객체를 구분한다.

---

## DataTable & Data Helper Policy

### 기본 원칙

- **DataTable 접근은 DataTableManager(FDataTableManager)를 통해서만 수행한다.** 다른 클래스에서 UDataTable을 보유하거나 직접 Load/FindRow 하지 않는다.
- 데이터 테이블 접근 로직을 분산시키지 않는다.
- 데이터 조회는 전용 Helper 클래스(DataTableManager)를 통해 수행한다.
- DataTable 직접 접근 코드를 여러 클래스에 중복 작성하지 않는다.
- 데이터 구조 변경 시 수정 범위를 최소화한다.

### 설계 기준

- DataTable 관련 로직은 하나의 책임 단위(DataTableManager)로 관리한다.
- Row 탐색, 캐싱, 변환 로직을 DataTableManager에 분리한다.
- 게임 로직에서 DataTable 구조를 직접 참조하지 않도록 한다.

---

## Bool → Enum Strategy

### 기본 원칙

- bool이 증가하기 시작하면 enum 통합을 우선 고려한다.
- 상태 조합이 의미를 가지는 경우 enum으로 관리한다.
- 즉시 구조 변경하지 않고 필요성을 먼저 확인한다.
- 상태 표현은 하나의 축으로 관리하는 것을 우선한다.

---

## Delegate-Driven UI Policy

### 기본 원칙

- 특정 값 변경에 따른 UI 변화는 Delegate를 통해 처리한다.
- UI는 직접 상태를 Polling 하지 않는다.
- UI는 Delegate에 바인딩하여 반응형으로 동작한다.
- 비즈니스 로직에서 UI를 직접 호출하지 않는다.
- 상태 변경은 이벤트 기반으로 전파한다.

---

## Early Return Rule

### 기본 원칙

- 중첩을 최소화한다.
- Guard Clause를 우선 사용한다.
- 깊은 조건문 구조를 지양한다.
- 흐름이 한 눈에 보이도록 작성한다.

---

## Readability Over Optimization Rule

### 기본 원칙

- 최적화보다 가독성을 우선한다.
- 성능 병목이 확인되기 전까지 구조를 복잡하게 만들지 않는다.
- 조기 최적화 금지.
- 복잡한 매크로 트릭 사용 금지.

---

## Unreal Dependency Policy

### 기본 원칙

- 전역 접근 최소화.
- 런타임 검색 최소화.
- 명시적 참조 전달을 우선한다.
- 암묵적 전역 접근 지양.
- GetWorld 기반 접근 남용 금지.

---

## UObject / Actor Usage Rule

- UObject는 데이터 또는 도메인 객체.
- Actor는 월드 표현 객체.
- Actor 내부에 비즈니스 로직 과도하게 작성하지 않는다.
- 상태는 전용 객체로 위임 가능하도록 설계한다.

---

## Subsystem Policy

Subsystem 허용 조건:

- 전역 시스템
- 단일 인스턴스가 논리적으로 맞는 경우
- 교체 가능성 낮음

그 외:

→ Service 클래스 또는 도메인 객체 사용

---

## Tick Policy

### 기본 원칙

- Tick 사용 최소화.
- 이벤트 기반 설계 우선.
- 상태 감지 전용으로만 사용한다.
- Tick 내부에 복잡한 분기 금지.
- 타이머/쿨다운은 별도 객체 분리 고려.

---

## Refactoring Rule

### 기본 원칙

- 리팩토링은 필요성이 체감되는 시점에 수행한다.
- 대규모 구조 변경 전 승인 요청.
- 전면 재작성 금지.
- 점진적 개선 우선.

---

## Change Strategy

기능 추가 시 체크리스트:

1. 기존 플러그인으로 해결 가능한가?
2. `/plugins` 문서를 먼저 확인했는가?
3. 기존 클래스 수정이 필요한가?
4. 수정 없이 확장 가능한가?
5. Unreal 의존이 불필요하게 퍼지고 있는가?
6. UPROPERTY 노출이 늘어나고 있는가?
7. bool이 증가하고 있는가?
8. Delegate 없이 직접 UI를 호출하고 있지 않은가?
9. Tick 의존이 늘어나고 있는가?
10. DataTable 접근이 분산되고 있지 않은가?
11. 포인터 소유권이 명확한가?
