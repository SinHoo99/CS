# 🎮 Unity 공부 기록 (2025-02-25) 

## 1. 오늘의 학습 목표 🎯
- [ ] ``SoundManager`` 초기화 문제 해결
- [ ] ``PlaySFX()``에서 ``NullReferenceException`` 해결
- [ ] ``GameManager``에서 ``SoundManager``가 올바르게 초기화되도록 수정
- [ ] ``OnDie()``에서 보스가 다음 단계로 변경되지 않는 문제 분석
- [ ] ``BossID`` 변경 로직 수정 (미해결)

---

## 2. 학습한 개념 📝
### 🔹 SoundManager에서 NullReferenceException 발생 원인 분석 및 해결
📌 문제
- ``PlaySFX()`` 실행 시 ``SFXDicts``가 ``null`` 상태여서 ``NullReferenceException`` 발생.
- ``SoundManager.Initializer()``가 실행되지 않았거나, ``SFXs`` 데이터가 비어있어서 초기화가 실패했을 가능성.
- ``GameManager``에서 ``SoundManager``가 올바르게 초기화되지 않으면, ``PlaySFX()`` 호출 시 오류 발생.

✅ 해결 방법
- ``GameManager.InitializeComponents()``에서 ``SoundManager.Initializer()``를 명확하게 호출하도록 수정.
- ``PlaySFX()``에서 ``SFXDicts == null``인지 검사하여 예외 발생 방지.
- ``SoundManager.Initializer()``에서 ``SFXs``가 비어있으면 오류 메시지를 출력하도록 수정.

📌 🔹 ``GameManager``에서 ``SoundManager`` 초기화 보장
```csharp
private void InitializeComponents()
{
    _soundManager = GetComponentInChildren<SoundManager>();

    if (_soundManager != null)
    {
        _soundManager.Initializer();
    }
    else
    {
        Debug.LogError("[GameManager] SoundManager가 할당되지 않았습니다! Inspector에서 설정하세요.");
    }
}

```
📌 🔹 SoundManager.Initializer()에서 SFXs가 비어있는지 검사
```csharp
public void Initializer()
{
    SFXDicts = new Dictionary<SFX, List<AudioClip>>();

    if (SFXs == null || SFXs.Length == 0)
    {
        Debug.LogError("[SoundManager] SFXs가 비어있습니다! Inspector에서 SFX를 설정하세요.");
        return;
    }

    foreach (var sound in SFXs)
    {
        if (!SFXDicts.ContainsKey(sound.MyEnum))
        {
            SFXDicts.Add(sound.MyEnum, sound.MyClip);
        }
    }

    Debug.Log("[SoundManager] SFXDicts 초기화 완료. 등록된 SFX 개수: " + SFXDicts.Count);
}

```
📌 🔹 PlaySFX()에서 SFXDicts가 null인지 확인
```csharp
public void PlaySFX(SFX target)
{
    if (SFXDicts == null)
    {
        Debug.LogError("[SoundManager] SFXDicts가 초기화되지 않았습니다! `Initializer()`가 실행되었는지 확인하세요.");
        return;
    }

    if (!SFXDicts.ContainsKey(target))
    {
        Debug.LogError($"[SoundManager] SFXDicts에 `{target}`이 존재하지 않습니다!");
        return;
    }

    int index = Random.Range(0, SFXDicts[target].Count);
    SFXSource.pitch = 1f + Random.Range(-SoundEffectPitchVariance, SoundEffectPitchVariance);
    SFXSource.PlayOneShot(SFXDicts[target][index]);
}

```
✅ 결과
- ``SoundManager``가 초기화되지 않은 상태에서 ``PlaySFX()``가 실행될 경우, 오류 메시지를 출력하여 문제를 쉽게 파악할 수 있음.
- ``GameManager``에서 ``SoundManager.Initializer()``를 실행하도록 설정하여 ``SFXDicts``가 항상 초기화되도록 보장.
- ``SFXs`` 데이터가 비어 있으면 오류 메시지를 출력하여 문제를 쉽게 확인할 수 있음.

---

### 🔹 ``OnDie()``에서 보스가 다음 단계로 변경되지 않는 문제 분석
📌 문제
- 보스가 사망(``OnDie()``)한 후 ``Respawn()``에서 새로운 보스로 변경되지 않음.
- ``BossID``가 예상대로 ``A → B → C``로 바뀌어야 하는데, 의도한 순서대로 변경되지 않는 문제가 발생.
- ``GetNextBossID()``에서 ``BossID``가 제대로 증가하지 않는지 확인 필요.

📌 🔹 ``OnDie()``에서 보스 변경 로직 확인
```csharp
private void OnDie()
{
    spriteRenderer.enabled = false;
    boxCollider.enabled = false;
    HealthStatusUI.HideSlider();

    Debug.Log($"[Boss] 현재 보스 {BossRuntimeData.CurrentBossID} 사망");

    // ✅ 다음 보스로 변경 로직 확인 필요
    BossRuntimeData.CurrentBossID = GetNextBossID();

    Invoke(nameof(Respawn), 3f);
}

```
📌 🔹 GetNextBossID()에서 올바르게 순서대로 변경되는지 확인
```csharp
private BossID GetNextBossID()
{
    BossID[] bossIds = (BossID[])Enum.GetValues(typeof(BossID));
    int currentIndex = Array.IndexOf(bossIds, BossRuntimeData.CurrentBossID);

    Debug.Log($"[Boss] 현재 Index: {currentIndex}, 현재 보스: {BossRuntimeData.CurrentBossID}");

    return (currentIndex < bossIds.Length - 1) ? bossIds[currentIndex + 1] : bossIds[0];
}

```
✅ 진행 결과
- ``Debug.Log()``를 추가하여 BossID가 어떻게 변경되는지 확인 중.
- ``BossID``가 잘못 변경되는 경우, ``Enum.GetValues()``를 활용하여 올바르게 순서를 조정할 계획.
---

## 3. C# 코드 예제 💻
🔹 SoundManager 관련 오류 방지 코드
```csharp
public void PlaySFX(SFX target)
{
    if (SFXDicts == null)
    {
        Debug.LogError("[SoundManager] SFXDicts가 초기화되지 않았습니다! `Initializer()`가 실행되었는지 확인하세요.");
        return;
    }

    if (!SFXDicts.ContainsKey(target))
    {
        Debug.LogError($"[SoundManager] SFXDicts에 `{target}`이 존재하지 않습니다!");
        return;
    }

    int index = Random.Range(0, SFXDicts[target].Count);
    SFXSource.pitch = 1f + Random.Range(-SoundEffectPitchVariance, SoundEffectPitchVariance);
    SFXSource.PlayOneShot(SFXDicts[target][index]);
}

```
🔹 GameManager에서 SoundManager 초기화
```csharp
private void InitializeComponents()
{
    _soundManager = GetComponentInChildren<SoundManager>();

    if (_soundManager != null)
    {
        _soundManager.Initializer();
    }
    else
    {
        Debug.LogError("[GameManager] SoundManager가 할당되지 않았습니다! Inspector에서 설정하세요.");
    }
}

```
---
4. 내일의 학습 계획 📌
   - BossID가 올바르게 변경되지 않는 문제 수정
   - 게임성 높일만한 기획하기
   - 게임 에셋 찾기
