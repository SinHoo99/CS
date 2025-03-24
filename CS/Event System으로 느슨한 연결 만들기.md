# 🎮 Unity 공부 기록 (2025-03-24)

## 1. 오늘의 학습 목표 🎯
- [ ] C# Action의 개념과 작동 방식 이해하기
- [ ] Unity에서 이벤트 시스템으로 Action을 어떻게 활용하는지 익히기
- [ ] Action<T>로 데이터 전달까지 가능한 구조 학습하기

---

## 2. 학습한 개념 📝
### 🔹 개념 1: `Action`이란?
`Action`은 C#에서 제공하는 `델리게이트(delegate)` 중 하나로,  
반환값이 없고 매개변수도 없는 메서드를 참조할 수 있다.

Unity에서는 이를 이벤트 시스템으로 자주 사용한다.  
`Action`은 단순히 "어떤 일이 일어났다"라는   
신호를 보낼 때 쓰인다고 생각하면 쉽다.  

예를들어 플레이어가 죽었을때 UI나 게임오버 팝업 등 여러 시스템이 반응해야 할 때  
`Action`을 쓰면 그 이벤트를 여러 곳에서 구독하고,  
그 일이 발생했을 때 한꺼번에 실행할 수 있다.
```csharp
public event Action OnDeath;

void TakeDamage(float damage)
{
    CurHP -= damage;
    if (CurHP <= 0)
    {
        CurHP = 0;
        OnDeath?.Invoke(); // 죽었음을 알림
    }
}

```
여기서 `OnDeath?.Invoke()`는 죽었다는 신호를 보내는 역할일 뿐,  
죽는 처리는 `CurHP` 로직이 따로 하고 있다.

---

### 🔹 개념 2: `Action<T>` — `Action`의 확장형  
`Action<T>`는 `Action`의 확장버전으로,  
이벤트를 발생시킬때 필요한 데이터를 함께 전달할 수 있다.  
```csharp
public event Action<float> OnChangeHP;

void TakeDamage(float damage)
{
    CurHP -= damage;
    OnChangeHP?.Invoke(CurHP); // 현재 체력도 같이 알림
}

```
이렇게 하면 이벤틀르 구독한 쪽에서 단순히 "체력이 바뀌었다"가 아니라 "체력이 얼마 남았는지"까지 알 수 있음.  

언제 Action<T>를 쓰나?
- 이벤트 발생과 함께 부가적인 데이터가 필요할 때
- 예: 데미지량, 현재 체력, 아이템 이름, 경험치 등
-  `Action<T1, T2>`처럼 2개 이상의 매개변수도 넘길 수 있음.

또한 Action<T1, T2>처럼 2개 이상의 매개변수도 넘길 수 있음.

---

## 3. C# 코드 예제 💻
```csharp
// 체력 변화 및 이벤트 전달 예제
using UnityEngine;
using System;

public class HealthSystem : MonoBehaviour
{
    public float MaxHP;
    public float CurHP;

    // 사망 이벤트: 단순 알림만 전달
    public event Action OnDeath;

    // 체력 변화 이벤트: 현재 체력과 최대 체력 함께 전달
    public event Action<float, float> OnChangeHP;

    public void InitHP(float curHP, float maxHP)
    {
        CurHP = curHP;
        MaxHP = maxHP;

        // 초기 체력 정보 전달
        OnChangeHP?.Invoke(CurHP, MaxHP);
    }

    public void TakeDamage(float damage)
    {
        CurHP -= damage;
        if (CurHP <= 0)
        {
            CurHP = 0;
            OnDeath?.Invoke(); // 죽었음을 알림
        }

        OnChangeHP?.Invoke(CurHP, MaxHP); // 체력 바뀐 걸 알림
    }

    public void Heal(float amount)
    {
        CurHP += amount;
        CurHP = Mathf.Min(CurHP, MaxHP);

        OnChangeHP?.Invoke(CurHP, MaxHP); // 회복해도 체력 바뀐 걸 알림
    }
}

```
이 구조의 핵심은:
- HP 깎이는 건 HealthSystem 내부에서 처리하고
- 그 사실을 알림(이벤트)으로 바깥에 전파하는 구조
- 외부에서는 그 이벤트를 구독해서, 예를 들어 HP UI를 업데이트하거나, 사운드를 재생할 수 있음
