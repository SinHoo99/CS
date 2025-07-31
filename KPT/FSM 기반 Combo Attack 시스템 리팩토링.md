
# Unity 콤보 공격 시스템 구조 리팩토링 정리

## ✅ 기존 구조: 단일 AttackState 내에서 ComboIndex로 제어

### 특징
- 하나의 `PlayerAttackState`만 존재
- ComboIndex (int 또는 enum)을 기준으로 Animator의 공격 애니메이션 분기
- 애니메이션 완료 시점에서 입력이 버퍼되어 있다면 `ComboIndex++` 후 재진입

### 단점
- **단일 스테이트가 너무 많은 책임을 가짐**
- 각 콤보 공격의 타이밍, 입력 버퍼링, 종료 조건 등을 모두 하나의 클래스에서 처리
- **읽기 어렵고 유지보수가 힘듦**
- 애니메이션 흐름을 명확하게 추적하기 어려움

---

## ✅ 개선 구조: 콤보 단계별로 State 분리

### 구조 요약
- `PlayerBaseComboAttackState`를 기반으로 각 콤보 단계를 별도 클래스로 분리
  - `PlayerAttackState` → `PlayerAttack2State` → `PlayerAttack3State` ...
- 각 상태는 자신만의 `ComboIndex`를 갖고, 애니메이션 이벤트를 받아서 다음 상태로 전이하거나 종료

### 핵심 메서드
```csharp
protected override void TryComboTransition()
{
    _stateMachine.ChangeState(new PlayerAttack2State(_stateMachine));
}
```

---

## 🔍 구조 변경의 차이점 및 장점

| 항목 | 기존 구조 | 개선 구조 |
|------|-----------|------------|
| **책임 분리** | 단일 클래스에서 모든 콤보 로직 처리 | 콤보 단계별 상태로 분리 (SRP 원칙 적용) |
| **가독성** | `ComboIndex` 기반 조건문 남발 | 명확한 상태 이름으로 흐름 추적 쉬움 |
| **확장성** | 추가 콤보마다 조건문 증가 | 상태 하나만 추가하면 끝 |
| **유지보수** | 버그 위치 파악 어려움 | 문제 발생 상태 클래스만 보면 됨 |
| **테스트 용이성** | 콤보 흐름 전체를 테스트해야 함 | 특정 상태 단위로 단위 테스트 가능 |

---

## 🧠 설계 관점에서의 개선

- 상태 전이 흐름이 명시적 (`TryComboTransition()` override)
- 애니메이션 이벤트와의 결합을 `BaseState`에 집중시켜 중복 제거
- `IBlockInputState`, `IComboAttackState` 등의 인터페이스로 명시적 역할 구분 가능

---

## 💎 캡슐화와 확장성

- 각 콤보 상태가 자신만의 로직을 가지면서도 **공통 동작은 부모 클래스에 캡슐화**
- 추후 `ChargeAttackState`, `SpinAttackState`처럼 완전히 다른 타입도 추가 가능
- 상태 별 애니메이션 클립/속도/이펙트 등을 쉽게 독립 설정 가능

---

## 🔧 향후 개선 고려사항

- `ComboIndex`를 SO 또는 Enum으로 외부화하면 더 유연하게 확장 가능
- 공격 데미지나 속도 등을 상태 별로 세밀하게 커스터마이징 가능
- `TryComboTransition()`을 공통 규칙으로 추상화하거나 테이블화 가능

---

## ✅ 결론

이번 구조 개선은 단순한 코드 리팩토링을 넘어, **"FSM 기반 아키텍처 설계 적용"** 이라는 점에서 구조적 완성도가 높음.  
유지보수성과 확장성을 크게 향상시켰으며, 상태 기반 캐릭터 FSM에 좋은 모범 사례가 될 수 있음.
