# 🎮 Unity 공부 기록 (2024-02-17)

## 1. 오늘의 학습 목표 🎯
- ```Boss``` 오브젝트의 참조 방식 최적화
- ```Find``` 계열 함수를 사용한 이유 분석
- ```Boss```를 싱글톤으로 만들지 않은 이유 & ```GameManager```에서 관리하지 않은 이유 정리

---

## 2. 학습한 개념 📝
### 🔹 개념 1: (```Boss``` 참조 방식 최적화)
📌 문제점
- ```Boss```를 ```Inspector```에서 프리팹으로 할당하면 하이어라키에 있는 보스와 연동되지 않음.
- 결과적으로 보스가 비활성화되더라도, 유닛이 총을 계속 쏘는 문제 발생.
💡 해결 방법
- ```FindObjectOfType<Boss>()```를 사용하여 하이어라키에 존재하는 Boss를 동적으로 찾도록 변경.
- ```OnEnable()```에서 ```FindObjectOfType<Boss>()``` 실행 후 ```Boss```를 캐싱.
```csharp
private void OnEnable()
{
    if (Boss == null)
    {
        Boss = FindObjectOfType<Boss>(); // ✅ 하이어라키에서 보스를 동적으로 찾음
    }

    StartCoroutine(ShootCoroutine());
}

```

### 🔹 개념 2: (```Find``` 계열 함수를 사용한 이유)
📌 기존 문제점
- ``Boss``를 Inspector에서 할당하면, 하이어라키에 있는 실제 Boss와 연결되지 않음.
- ``GameObject.FindWithTag("Boss")``도 가능하지만, 태그를 설정해야 하는 번거로움이 있음.

💡 ``FindObjectOfType<Boss>()``을 사용한 이유

| 방법 | 장점 | 단점 | 채택 여부 |
|------|------|------|------|
| ``GameObject.FindWithTag("Boss")`` | 특정 태그가 있는 오브젝트 검색 가능 | 태그를 직접 설정해야 함 | ❌ |
| ``FindObjectOfType<Boss>()`` | ``Boss`` 컴포넌트가 있는 오브젝트 자동 검색 | 보스가 여러 개 있으면 문제 발생 가능 | ✅ (보스가 하나만 존재하므로 가장 적절한 방법!)|

✔️ ``FindObjectOfType<Boss>()``을 사용하여 보스를 자동으로 찾을 수 있어 코드 유지보수가 용이.

### 🔹 개념 3: (```Boss```를 싱글톤으로 만들지 않은 이유)
📌 ``Boss``를 싱글톤으로 만들면 발생하는 문제
- ``Boss``는 동적으로 사라졌다가 다시 등장하는 오브젝트
- 싱글톤으로 만들면, 보스를 ``Destroy()``할 때 싱글톤 인스턴스가 사라져버려, 새로운 ```Boss```를 생성할 수 없음.
- 보스는 게임의 핵심 오브젝트가 아니므로, 반드시 전역적으로 접근할 필요 없음.
💡 해결 방법
- ```Boss```는 씬 내에서 필요할 때만 찾도록 ```FindObjectOfType<Boss>()```을 사용.
- ```Boss```가 없는 경우 생성될 때까지 대기.
```csharp
private IEnumerator ShootCoroutine()
{
    while (Boss == null)
    {
        yield return null;
        Boss = FindObjectOfType<Boss>(); // ✅ 보스가 생성될 때까지 대기 후 할당
    }

    while (true)
    {
        float randomNum = Random.Range(0.5f, 1.5f);
        yield return new WaitForSeconds(randomNum);
        ShootBullet();
    }
}

```
✔️ 이제 Boss가 씬에서 동적으로 사라졌다가 다시 생성되더라도 유닛이 정상적으로 작동함!

### 🔹 개념 4: (```Boss```를 ```GameManager```에서 관리하지 않은 이유)
📌 ```GameManager```에서 관리하면 안 되는 이유
- ```GameManager```는 게임의 전체적인 상태(점수, 씬 전환, 설정 등)를 관리하는 싱글톤.
- ```Boss```는 특정 씬에서만 존재할 수 있으며, ```GameManager```가 보스를 관리하면 불필요한 의존성이 증가.
- 보스를 ```GameManager```에서 관리하면, 씬이 바뀔 때 보스가 유지되는 등의 문제가 발생할 수 있음.
💡 해결 방법
- ```GameManager```는 게임의 전체 흐름을 관리하고, ```Boss```는 독립적인 오브젝트로 유지.
- ```FindObjectOfType<Boss>()```를 사용하여 필요할 때만 ```Boss```를 참조.   
✔️ 이제 ```GameManager```는 게임 관리에 집중하고, 보스는 독립적으로 관리됨!


## 3. C# 코드 예제 💻

🔹 FindObjectOfType<Boss>()를 사용한 보스 참조 방식
```csharp
private void OnEnable()
{
    if (Boss == null)
    {
        Boss = FindObjectOfType<Boss>(); // ✅ 하이어라키에서 보스를 동적으로 찾음
    }

    StartCoroutine(ShootCoroutine());
}
```
🔹 ShootBullet()에서 보스가 비활성화되었을 경우 총알 발사 중지
```csharp
private void ShootBullet()
{
    if (Boss == null) return; // ✅ 보스가 없는 경우 실행하지 않음

    SpriteRenderer bossSprite = Boss.GetComponentInChildren<SpriteRenderer>();
    if (bossSprite == null || !bossSprite.enabled)
    {
        return; // ✅ 보스가 비활성화되면 총알 발사하지 않음
    }

    Vector2 direction = (Boss.transform.position - FirePoint.position).normalized;
    CreateBullet(Tag.Bullet, FirePoint.position, direction, gameObject.tag);
}

```
🔹 보스가 생성될 때까지 대기하는 ShootCoroutine()
```csharp
private IEnumerator ShootCoroutine()
{
    while (Boss == null)
    {
        yield return null;
        Boss = FindObjectOfType<Boss>(); // ✅ 보스가 생성될 때까지 대기 후 할당
    }

    while (true)
    {
        float randomNum = Random.Range(0.5f, 1.5f);
        yield return new WaitForSeconds(randomNum);
        ShootBullet();
    }
}

```
---
### 🔥 최종 정리   
✅ Boss 참조 방식 개선 → 하이어라키에서 자동으로 할당하도록 수정  
✅ FindObjectOfType<Boss>()를 사용하여 동적으로 Boss를 찾도록 변경  
✅ Boss를 싱글톤으로 만들지 않은 이유 → 동적으로 사라졌다가 다시 생성되기 때문  
✅ Boss를 GameManager에서 관리하지 않은 이유 → 불필요한 의존성 증가 방지  
