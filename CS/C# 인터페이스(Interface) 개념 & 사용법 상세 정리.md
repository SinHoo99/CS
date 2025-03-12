# 🎮 Unity 공부 기록 (2025-03-12)

## 1. 오늘의 학습 목표 🎯
- [ ] 인터페이스(Interface)의 개념을 정확히 이해한다.
- [ ] 인터페이스를 C#에서 구현하고 사용하는 방법을 익힌다.
- [ ] 인터페이스를 활용한 코드 유지보수 및 확장성을 높이는 방법을 학습한다.

---

## 2. 학습한 개념 📝
### 🔹 개념 1: 인터페이스(Interface)란?
📌 설명:
- 인터페이스(Interface)는 클래스가 특정 동작을 반드시 구현하도록 강제하는 역할을 한다.
- 클래스와 달리 구현(Implementation)이 없으며, 메서드나 속성만 정의한다.
- 여러 개의 인터페이스를 동시에 구현 가능(다중 상속 가능)하여 코드의 유연성을 높인다.
- I(Interface의 약어)로 시작하는 네이밍을 사용하는 것이 일반적이다.
   - 예: `IInteractable`, `IAttackable`, `IMovable`

📌 인터페이스를 사용하는 이유:
- 클래스 간 결합도를 낮추고 유지보수를 쉽게 만들기 위해 사용
- 클래스들이 동일한 기능을 제공하도록 강제하여 일관된 설계 가능
- 다형성을 활용하여 객체를 더 유연하게 사용 가능

### 🔹 개념 2: 인터페이스 기본 문법
📌 인터페이스 정의 예시  
```csharp
public interface IExample
{
    void DoSomething();  // 메서드 정의 (구현 X)
}
```
- `IExample` 인터페이스를 정의했고, `DoSomething()` 메서드를 포함해야 함.
- 구현이 없으며, 이를 상속받는 클래스가 직접 구현해야 함.

📌 인터페이스를 구현하는 클래스
```csharp
public class ExampleClass : IExample
{
    public void DoSomething()
    {
        Debug.Log("IExample 인터페이스 구현 완료!");
    }
}
```
- ExampleClass는 IExample을 반드시 구현해야 함.
- DoSomething()을 구현하지 않으면 컴파일 오류 발생!

📌 인터페이스를 활용한 다형성 예제
```csharp
IExample example = new ExampleClass();
example.DoSomething(); // 출력: "IExample 인터페이스 구현 완료!"
```
- 인터페이스를 통해 객체를 다룰 수 있어 유연한 코드 설계 가능.

## 3. C# 코드 예제 💻
### 🔹 예제 1: 다중 인터페이스 구현 (다중 상속 가능)  
📌 설명:  
- C#에서는 클래스는 하나만 상속할 수 있지만, 인터페이스는 여러 개 구현 가능하다.
```csharp
public interface IWalkable
{
    void Walk();
}

public interface IRunnable
{
    void Run();
}

public class Character : IWalkable, IRunnable
{
    public void Walk()
    {
        Debug.Log("캐릭터가 걷습니다.");
    }

    public void Run()
    {
        Debug.Log("캐릭터가 뜁니다.");
    }
}

```
- Character는 IWalkable과 IRunnable을 동시에 구현할 수 있음.
- 코드 확장성이 높아지며 여러 동작을 클래스에 추가할 수 있음.
---
### 🔹 예제 2: 다형성을 활용한 인터페이스 사용  
📌 설명:  
- 동일한 인터페이스를 가진 여러 클래스를 같은 방식으로 처리 가능.
```csharp
public interface IAttackable
{
    void Attack();
}

public class Warrior : IAttackable
{
    public void Attack()
    {
        Debug.Log("전사가 공격합니다!");
    }
}

public class Archer : IAttackable
{
    public void Attack()
    {
        Debug.Log("궁수가 공격합니다!");
    }
}

public class Mage : IAttackable
{
    public void Attack()
    {
        Debug.Log("마법사가 공격합니다!");
    }
}

public class Game
{
    public static void ExecuteAttack(IAttackable character)
    {
        character.Attack();
    }
}
```
📌 사용 예시
```csharp
IAttackable warrior = new Warrior();
IAttackable archer = new Archer();
IAttackable mage = new Mage();

Game.ExecuteAttack(warrior);  // 출력: "전사가 공격합니다!"
Game.ExecuteAttack(archer);   // 출력: "궁수가 공격합니다!"
Game.ExecuteAttack(mage);     // 출력: "마법사가 공격합니다!"
```
- IAttackable을 구현한 객체는 다형성을 활용하여 동일한 방식으로 처리 가능.
- Game.ExecuteAttack() 메서드는 새로운 캐릭터가 추가되더라도 기존 코드를 수정하지 않고 확장 가능.
---
### 🔹 예제 3: 게임 개발에서 인터페이스 활용 (상호작용 시스템)
📌 설명:  
- 플레이어가 다양한 오브젝트와 상호작용하도록 구현
- `IInteractable`을 구현하면 문(Door), 상자(Chest) 등 다양한 오브젝트가 동일한 방식으로 동작 가능
```csharp
public interface IInteractable
{
    void Interact();
}

// 문(Door) 클래스
public class Door : MonoBehaviour, IInteractable
{
    public void Interact()
    {
        Debug.Log("문이 열렸습니다!");
    }
}

// 상자(Chest) 클래스
public class Chest : MonoBehaviour, IInteractable
{
    public void Interact()
    {
        Debug.Log("상자를 열었습니다!");
    }
}

// 플레이어가 상호작용할 때 사용
public class Player : MonoBehaviour
{
    private void OnTriggerEnter(Collider other)
    {
        IInteractable interactable = other.GetComponent<IInteractable>();
        if (interactable != null)
        {
            interactable.Interact();  // 모든 IInteractable 오브젝트와 상호작용 가능
        }
    }
}
```
- `Door`와 `Chest`가 `IInteractable`을 구현하면 `Player`가 상호작용 가능.
- 새로운 상호작용 오브젝트(Lever, NPC 등)를 추가할 때`IInteractable`만 구현하면 자동으로 확장 가능!

---

## 4. 인터페이스를 사용 정리
| 사용 상황 | 인터페이스를 활용하는 이유 |
|-----------|--------------------------------|
| ✅ **여러 개의 클래스가 같은 동작을 해야 할 때** | `IAttackable`, `IInteractable` 등 공통된 기능을 강제 |
| ✅ **객체 간 결합도를 줄이고 싶을 때** | `GameManager`가 직접 클래스 참조하지 않고 인터페이스로 처리 가능 |
| ✅ **클래스 간 다형성을 높이고 싶을 때** | `ExecuteAttack(IAttackable character)`와 같이 유연한 코드 작성 가능 |
| ✅ **추후 기능 확장이 예상될 때** | 새 기능을 추가해도 기존 코드 수정 없이 적용 가능 |

