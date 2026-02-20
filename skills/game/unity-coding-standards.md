---
name: csharp-coding-standards
description: "CSharp coding standards for Unity services: Core Philosophy, Encapsulation Strategy, Field & Editor Exposure Policy, Null Comparison Rule, MonoBehaviour Design Rule, Dependency Policy, ScriptableObject Usage, Naming Convention, Manager Policy, Update Policy, Code Style Consistency, Change Strategy"
---

# CSharp Coding Standards

Standards for readable, maintainable C# (14) code in Unity services.

## Core Philosophy

- Unity는 프레임워크일 뿐, 게임 로직의 중심이 아니다.

- 게임 규칙은 순수 C# 객체에서 관리한다.

- 확장을 고려하되, 불필요한 추상화는 만들지 않는다.

- 변경 가능성이 높은 영역과 공통 시스템을 분리한다.

- 에디터는 편의 도구일 뿐, 설계의 중심이 아니다.

---

## Encapsulation Strategy

### 기본 원칙

- 모든 필드는 `private`

- 외부 읽기 필요 시 → `public { get; }`

- 외부 수정 필요 시 → `public { get; set; }`

- 내부에서만 수정 → `public { get; private set; }`

### 권장 패턴

1. 외부 읽기만 허용

```
private int hp;

public int Hp => hp;
```

또는

```
public int Hp { get; private set; }
```

2. 외부 수정 허용 (정말 필요한 경우만)

```
public float Speed { get; set; }
```

하지만 이 경우에도:

- 비즈니스 제약이 있다면 setter에서 검증 수행

- 무제한 public set은 지양

3. 상태 변경은 메서드로
   잘못된 방식:

```
player.Hp = 0;
```

권장 방식:

```
player.TakeDamage(amount);
```

이유:

- 무결성 보호

- 상태 변경 흐름 추적 가능

- 이벤트 트리거 제어 가능

---

## Field & Editor Exposure Policy

### 기본 원칙

- public field 사용 금지

- [SerializeField] private 최소 사용

- 런타임 상태는 Inspector에 노출하지 않는다

- 설정값만 에디터 노출

- FindObjectOfType 등으로 런타임에서 컴포넌트를 가져와야 한다면 사용하지 않고 에디터에서 직접 주입하도록 할 것

### Inspector 노출 기준

노출 가능:

- 밸런스 수치

- 프리팹 참조

- 사운드/이펙트 레퍼런스

- 튜닝 파라미터

노출 금지:

- 런타임 상태 값

- 내부 캐싱 값

- 계산 결과

- 시스템 내부 플래그

### 예시

잘못된 예:

```
public int currentHp;
```

올바른 예:

```
[SerializeField] private int maxHp;

private int currentHp;

public int CurrentHp => currentHp;
```

## Null Comparison Rule

### 통일 규칙

- `== null` 사용 금지

- `!= null` 사용 금지

- 명시적 비교가 필요할 경우 `is null`, `is not null` 사용

- 단순 호출 및 접근은 **null propagation (`?.`)을 우선 사용**

### 우선순위 규칙

#### 1️⃣ Null Propagation 우선

단순 메서드 호출:

```csharp
player?.Attack();
// 값 접근:
var hp = player?.Hp;
// 기본값이 필요한 경우:
var hp = player?.Hp ?? 0;
```

#### 2️⃣ 로직 분기가 필요한 경우만 명시적 검사

null 여부 자체가 의미를 가지는 경우에만 사용:

```
if (player is null)
{
    SpawnDefaultPlayer();
}
if (player is not null)
{
    StartCombat();
}
```

#### 3️⃣ null이 버그인 경우

null이 허용되지 않는 상태라면 명시적으로 예외 처리:

```
if (player is null)
{
    throw new InvalidOperationException("Player must exist.");
}
```

### 지양 패턴

```
if (player is not null)
{
    player.Attack();
}
```

단순 호출 목적의 null 체크는 ?.로 대체한다.

```
player?.Attack();
```

## MonoBehaviour Design Rule

- MonoBehaviour는 View Adapter

- 비즈니스 로직 작성 금지

- 상태 머신 또는 Domain 객체 위임

- Update 내부에 직접 로직 작성 금지

## Dependency Policy

- GameObject.Find 금지

- 런타임 검색 최소화

- Inspector 주입 우선

- 명시적 참조 사용

- 암묵적 전역 접근 금지

## ScriptableObject Usage

- 설정 데이터 전용

- 런타임 상태 저장 금지

- 로직 작성 금지 (단순 계산은 허용)

## Naming Convention

- bool → is/has/can prefix

- 인터페이스 → I prefix

- 이벤트 → On + Verb

- Manager 네이밍 최소화

- 역할 중심 네이밍

## Manager Policy

Manager 허용 조건:

- 전역 시스템

- 단일 인스턴스

- 교체 가능성 낮음

그 외:

→ Service class 또는 Domain 객체

## Update Policy

- 상태 감지 전용

- 분기 많아지면 상태 객체 분리

- 타이머/쿨다운은 별도 클래스로 분리

## Code Style Consistency

- 가능한 한 모든 지역 변수에는 var를 활용할 것

- 중괄호 생략 금지

- 한 메서드 30줄 이상이면 분리 고려

- private 필드는 camelCase

- public 프로퍼티는 PascalCase

## Change Strategy

기능 추가 시 체크리스트:

1. 기존 클래스 수정이 필요한가?

2. 수정 없이 확장 가능한가?

3. UnityEngine 의존이 퍼지고 있지 않은가?

4. Inspector 의존이 늘어나고 있지 않은가?
