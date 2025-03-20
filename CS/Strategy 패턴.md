# 🎮 Unity 공부 기록 (2025-03-20)

## 1. 오늘의 학습 목표 🎯
- [ ] Strategy 패턴의 개념 이해
- [ ] 코드 비교
- [ ] Unity에서 Strategy 패턴을 적용하는 방법 학습

---

## 2. 학습한 개념 📝
### 🔹 개념 1: Strategy 패턴이란?  
Strategy 패턴은 객체의 특정 동작(알고리즘)을 동적으로 변경할 수 있도록 설계하는 디자인 패턴이다.  

즉, 여러 개의 알고리즘을 인터페이스로 분리하고, 필요할 때 이를 교체할 수 있도록 설계하는 방식이다.
- 객체가 특정 동작을 수행하는 방식(전략)을 외부에서 주입할 수 있음
- if-else 또는 switch 문을 제거하여 코드의 유지보수성을 높임
- 새로운 전략을 추가할 때 기존 코드를 수정하지 않아도 됨(OCP, 개방-폐쇄 원칙 적용)

### 🔹 개념 2: Strategy 패턴의 구조
```plaintext
+------------------+       +-----------------+
|   Context       |------> |  Strategy       |
| (전략을 실행)    |       | (인터페이스)     |
| setStrategy()   |       | execute()       |
+------------------+       +-----------------+
       |                     ▲
       |                     |
       |       +--------------------+
       |       | ConcreteStrategyA  |
       |       | (구체적인 전략1)    |
       |       +--------------------+
       |
       |       +--------------------+
       |       | ConcreteStrategyB  |
       |       | (구체적인 전략2)    |
       |       +--------------------+

```
| **요소** | **설명** |
|----------|---------|
| **Strategy (전략 인터페이스)** | 공통된 메서드를 선언하여 여러 전략을 통합 |
| **ConcreteStrategy (구체적인 전략 클래스)** | 특정 전략을 구현하는 클래스 |
| **Context (컨텍스트, 전략을 실행하는 역할)** | 특정 전략을 저장하고 실행 |
| **Client (클라이언트 코드)** | Context에 원하는 Strategy 객체를 전달하여 실행 |

---

## 3. C# 코드 예제 💻
안 좋은 코드 예시 (if-else로 동작을 변경)
```csharp
using UnityEngine;

public class Player : MonoBehaviour
{
    public enum MoveType { Walk, Run, Jump }
    public MoveType moveType;

    void Update()
    {
        if (moveType == MoveType.Walk)
        {
            Walk();
        }
        else if (moveType == MoveType.Run)
        {
            Run();
        }
        else if (moveType == MoveType.Jump)
        {
            Jump();
        }
    }

    void Walk() => Debug.Log(" 걷기 이동");
    void Run() => Debug.Log(" 달리기 이동");
    void Jump() => Debug.Log(" 점프 이동");
}
```
🚨 문제점:  
❌ if-else가 많아질수록 유지보수 어려움  
❌ 새로운 이동 방식 추가 시 기존 코드 수정 필요 → OCP(개방-폐쇄 원칙) 위배  
❌ Update() 안에서 모든 이동 로직을 직접 처리하여 SRP(단일 책임 원칙) 위배  

---

Strategy 패턴을 적용한 개선된 코드  

1️⃣ 전략 인터페이스 (공통된 이동 방식)
```csharp
public interface IMoveStrategy
{
    void Move();
}

```
✔ IMoveStrategy 인터페이스를 정의하여 모든 이동 방식이 이 인터페이스를 따르게 만듦

2️⃣ 구체적인 전략 클래스 (각 이동 방식)
```csharp
public class WalkStrategy : IMoveStrategy
{
    public void Move()
    {
        Debug.Log(" 걷기 이동");
    }
}

public class RunStrategy : IMoveStrategy
{
    public void Move()
    {
        Debug.Log(" 달리기 이동");
    }
}

public class JumpStrategy : IMoveStrategy
{
    public void Move()
    {
        Debug.Log(" 점프 이동");
    }
}
```
✔ 각각의 이동 방식이 IMoveStrategy를 구현  
✔ 새로운 이동 방식 추가 시 기존 코드 수정 없이 새로운 클래스를 만들면 됨

3️⃣ Context (플레이어 이동 클래스)
```csharp
public class PlayerMovement
{
    private IMoveStrategy _moveStrategy;

    // 전략(이동 방식) 설정
    public void SetMoveStrategy(IMoveStrategy strategy)
    {
        _moveStrategy = strategy;
    }

    //  현재 설정된 전략 실행
    public void Move()
    {
        if (_moveStrategy != null)
        {
            _moveStrategy.Move();
        }
        else
        {
            Debug.Log(" 이동 전략이 설정되지 않았습니다!");
        }
    }
}

```
✔ SetMoveStrategy() 메서드로 이동 전략을 동적으로 변경 가능  
✔ Move() 메서드 실행 시 현재 설정된 전략을 실행  

4️⃣ 실행 코드 (전략 변경)  
```csharp
public class StrategyPatternExample : MonoBehaviour
{
    private void Start()
    {
        PlayerMovement player = new PlayerMovement();

        // 전략 설정 및 실행
        player.SetMoveStrategy(new WalkStrategy());
        player.Move();  // 출력: "걷기 이동"

        player.SetMoveStrategy(new RunStrategy());
        player.Move();  // 출력: "달리기 이동"

        player.SetMoveStrategy(new JumpStrategy());
        player.Move();  // 출력: "점프 이동"
    }
}
```
✔ 이동 방식을 SetMoveStrategy()로 동적으로 변경 가능  
✔ 새로운 이동 방식 추가 시 기존 PlayerMovement 코드를 수정할 필요 없음 → 유지보수성 증가  

---
## 4.  Strategy 패턴의 장점 & 단점
| **장점** | **단점** |
|----------|---------|
| ✔ **코드 유지보수성 증가** → 알고리즘이 분리되어 수정이 용이 | ⚠ **객체 수 증가** → 전략 클래스로 인해 코드량 증가 |
| ✔ **코드 중복 제거** → if-else문을 제거하고 전략 클래스로 관리 | ⚠ **전략이 너무 자주 변경되면 오버헤드 발생** |
| ✔ **유연한 확장성** → 새로운 전략을 추가해도 기존 코드 수정 없음 |  |

