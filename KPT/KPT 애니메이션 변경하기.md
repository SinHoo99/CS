# 🎮 Unity 공부 기록 (2024-02-17)

## 1. 오늘의 학습 목표 🎯
- [ ] 보스가 사망하면 다음 보스로 변경되도록 구현
- [ ] 보스 애니메이션을 ``Animator``를 통해 자동 변경
- [ ] ``Enum``을 활용하여 보스 ID 순차적 으로 변경
- [ ] 비효율 적인 ``Enum.GetValues()`` 대신 간단한 ``bossID + 1`` 방식으로 개선
---

## 2. 학습한 개념 📝
### 🔹 개념 1: (보스 순차 변경 로직 최적화)
🚀 기존 문제점
- ``Enum.GetValues() ``를 리스트로 변환 후 ``Sort()``를 수행 => 불필요한 메모리 할당 발생
- ``IndexOf()``를 사용하여 보스 ID를 찾음 => 단순한 ``+1`` 연산이면 충분
- ``bossID``를 직접 변경하지 않아 보스가 진화하지 않는 문제 발생

✅ 해결 방법
```csharp
private BossID GetNextBossID()
{
    return (bossID < BossID.E) ? bossID + 1 : BossID.A; 
}
```
✔️ 이제 보스가 A → B → C → D → E 순서로 자연스럽게 변경됨 🚀
✔️ 불필요한 연산을 제거하여 성능 최적화

### 🔹 개념 2: (보스 애니메이션 자동 변경)
🚀 기존 문제점
- 보스가 변경되었지만 애니메이션이 그대로 유지됨
- ``Animator``의 ``Trigger``를 설정하여 애니메이션을 변경해야 함

```csharp
private void UpdateBossAnimation()
{
    if (animator == null)
    {
        Debug.LogError("Animator가 설정되지 않았습니다!");
        return;
    }
    animator.SetTrigger(bossID.ToString());
}

```
✔️ 보스가 변경될 때 bossID에 해당하는 애니메이션을 자동으로 실행!

---

## 3. C# 코드 예제 💻
```csharp
using UnityEngine;

public class Boss : MonoBehaviour
{
    private GameManager GM => GameManager.Instance;
    public BossID bossID; // 현재 보스 ID
    public int maxHealth;
    private int currentHealth;
    private SpriteRenderer spriteRenderer;
    private Animator animator;

    private void Awake()
    {
        spriteRenderer = GetComponentInChildren<SpriteRenderer>();
        animator = GetComponentInChildren<Animator>();
    }

    private void OnEnable()
    {
        Respawn();
        Debug.Log($"현재 보스: {bossID}, MaxHealth: {maxHealth}");
    }

    public void TakeDamage(int damage)
    {
        currentHealth -= damage;
        if (currentHealth <= 0)
        {
            Die();
        }
    }

    private void Die()
    {
        spriteRenderer.enabled = false;
        bossID = GetNextBossID(); // 보스 ID 변경하여 진화!
        Debug.Log($"보스 진화! 다음 보스: {bossID}");
        Invoke(nameof(Respawn), 3f);
    }

    private void Respawn()
    {
        if (GM.DataManager.BossDatas.TryGetValue(bossID, out BossData bossData))
        {
            maxHealth = bossData.MaxHealth;
            currentHealth = maxHealth;
            Debug.Log($"현재 보스: {bossID}, MaxHealth: {maxHealth}");
            UpdateBossAnimation();
        }
        else
        {
            Debug.LogError($"BossID {bossID}에 해당하는 데이터 없음!");
        }

        spriteRenderer.enabled = true;
    }

    private BossID GetNextBossID()
    {
        return (bossID < BossID.E) ? bossID + 1 : BossID.A;
    }

    private void UpdateBossAnimation()
    {
        animator.SetTrigger(bossID.ToString());
    }
}

```
---

KPT 회고 📌  
🔹 Keep (잘한 점)  
✅ bossID + 1을 활용하여 보스 순차 변경을 더 효율적으로 개선  
✅ Animator.SetTrigger()를 활용하여 보스 애니메이션을 자동 변경  
✅ Respawn()에서 BossID 데이터를 Dictionary에서 가져와 최대 체력을 자동으로 설정  

🔹 Problem (개선할 점)  
⚠️ bossID가 초기화되지 않으면 0이 될 수 있으므로 Awake()에서 기본값을 설정해야 함  
⚠️ 보스가 바뀔 때 이펙트 효과를 추가하면 좀 더 직관적인 변화 가능  

🔹 Try (다음에 시도할 점)    
🔹 보스가 변경될 때 페이드 인/아웃 효과 추가  
🔹 Animator에서 Has Exit Time을 조정하여 더 부드러운 애니메이션 전환  
🔹 보스별 고유한 스킬 및 공격 패턴 추가
