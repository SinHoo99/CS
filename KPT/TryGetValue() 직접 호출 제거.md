# 🎮 Unity 공부 기록 (2025-02-28)

# 1. 오늘의 학습 목표 🎯
- [ ] TryGetValue() 직접 호출 제거하여 코드 가독성 높이기

---

# 2. 학습한 개념 📝
## 🔹 개념 1: GameManager 활용하여 TryGetValue() 직접 호출 제거
### 🔹 문제점  
- 여러 곳에서 TryGetValue()를 직접 호출하여 중복된 코드 발생
- GameManager에서 정적 데이터(FruitsData) 와 동적 데이터(CollectedFruitData) 를 가져오는 메서드를 만들어 최적화 가능

### 🔹 리팩토링 결과  
- GM.GetFruitsData(id) → 정적 데이터 (FruitsData) 가져오기  
- GM.GetCollectedFruitData(id) → 동적 데이터 (CollectedFruitData) 가져오기  
- GM.GetFruitAmount(id) → 보유한 과일 개수 가져오기  
- 각 스크립트에서 TryGetValue() 직접 호출하지 않고 GameManager의 메서드를 활용  
    
---

## 🔹 개념 2: DictionaryManager 최적화
### 🔹 문제점
- IsFruitCollected(fruitID) 사용 → GetFruitAmount(fruitID) > 0로 변경 가능
- Debug.Log()가 너무 자주 호출됨 → 값이 바뀔 때만 출력하도록 최적화
- GameManager.Instance 직접 참조 → 싱글톤 변수 GM 활용

### 🔹 리팩토링 결과
- IsFruitCollected(fruitID) 제거 → GM.GetFruitAmount(fruitID) > 0 사용
- Debug.Log() 최소화 → 값 변경될 때만 출력
- GameManager 활용 → 불필요한 TryGetValue() 제거

---

## 🔹 개념 3: FruitUIManager 최적화
### 🔹 문제점
- _fruitData.TryGetValue(id, out var fruitData) → GM.GetFruitsData(id)로 변경 가능
- UI 업데이트에서 불필요한 TryGetValue() 사용
- GameManager.Instance 직접 참조가 많음

### 🔹 리팩토링 결과
- GM.GetFruitsData(id), GM.GetFruitAmount(id), GM.GetCollectedFruitData(id) 활용
- TryGetValue() 중복 제거 및 코드 가독성 향상
- GameManager.Instance 직접 참조 최소화

---

## 🔹 개념 4: FruitItem 최적화
### 🔹 문제점
- TryGetValue()를 여러 번 호출하여 불필요한 데이터 접근 발생
- fruitButton.GetComponent<Image>() 사용 → TryGetComponent()로 최적화 가능
- GameManager.Instance 직접 참조

### 🔹 리팩토링 결과
- GM.GetFruitsData(fruitID), GM.GetCollectedFruitData(fruitID), GM.GetFruitAmount(fruitID) 활용
- fruitButton.GetComponent<Image>() → TryGetComponent<Image>(out var buttonImage) 사용
- GameManager.Instance 직접 참조 최소화
---



# 3. 기존 코드 vs 리팩토링 코드 비교 💻

✅ 1. GetFruitImage() 최적화

🔹 기존 코드
```csharp
public Sprite GetFruitImage(FruitsID id)
{
    return _fruitSprites.TryGetValue(id, out var sprite) ? sprite : null;
}
```
🔹 교체 후 코드
```csharp
public Sprite GetFruitImage(FruitsID id)
{
    return GM.GetFruitsData(id)?.Image;
}
```
✔ _fruitSprites 관리 필요 없이 GameManager에서 직접 가져옴

---
✅ 2. RemoveFruitUI() 최적화

🔹 기존 코드
```csharp
public void RemoveFruitUI(FruitsID id)
{
    if (_fruitUIItems.TryGetValue(id, out var fruitItem))
    {
        Destroy(fruitItem.gameObject);
        _fruitUIItems.Remove(id);
    }
}
```
🔹 교체 후 코드
```csharp
public void RemoveFruitUI(FruitsID id)
{
    if (!_fruitUIItems.ContainsKey(id)) return;

    Destroy(_fruitUIItems[id].gameObject);
    _fruitUIItems.Remove(id);
}
```
✔ TryGetValue() 대신 ContainsKey() 사용하여 코드 단순화

---

✅ 3. UpdateOrCreateFruitUI() 최적화

🔹 기존 코드
```csharp
public void UpdateOrCreateFruitUI(FruitsID id, int count)
{
    if (_fruitUIItems.TryGetValue(id, out var fruitItem))
    {
        fruitItem.UpdateFruit(id, count, GetFruitImage(id));
    }
    else
    {
        CreateFruitUI(id, count);
    }
}
```
🔹 교체 후 코드
```csharp
public void UpdateOrCreateFruitUI(FruitsID id, int count)
{
    if (_fruitUIItems.ContainsKey(id))
    {
        _fruitUIItems[id].UpdateFruit(id, count, GetFruitImage(id));
    }
    else
    {
        CreateFruitUI(id, count);
    }
}
```
✔ TryGetValue() 없이 ContainsKey() 활용하여 코드 최적화

---

✅ 4. UpdateDictionaryUI() 최적화

🔹 기존 코드
```csharp
public void UpdateDictionaryUI(FruitsID fruitID)
{
    if (!_fruitDictionaryItems.TryGetValue(fruitID, out var item))
    {
        Debug.LogWarning($"[DictionaryManager] 도감에 {fruitID} 아이템이 없습니다.");
        return;
    }

    bool isCollected = GM.GetFruitAmount(fruitID) > 0;
    item.SetCollected(isCollected);
}
```
🔹 교체 후 코드
```csharp
public void UpdateDictionaryUI(FruitsID fruitID)
{
    if (!_fruitDictionaryItems.ContainsKey(fruitID)) return;

    var item = _fruitDictionaryItems[fruitID];
    bool isCollected = GM.GetFruitAmount(fruitID) > 0;
    
    item.SetCollected(isCollected);
}
```
✔ 딕셔너리 접근 횟수 줄이고 코드 단순화

---

✅ 5. UpdateAllDictionaryUI() 최적화

🔹 기존 코드
```csharp
public void UpdateAllDictionaryUI()
{
    Debug.Log("[DictionaryManager] UpdateAllDictionaryUI() 실행");

    foreach (var (id, item) in _fruitDictionaryItems)
    {
        bool isCollected = GM.GetFruitAmount(id) > 0;
        Debug.Log($"[DictionaryManager] {id} UI 업데이트 - 수집 여부: {isCollected}");

        item.SetCollected(isCollected);
    }
}

```
🔹 교체 후 코드
```csharp
public void UpdateAllDictionaryUI()
{
    foreach (var (id, item) in _fruitDictionaryItems)
    {
        bool isCollected = GM.GetFruitAmount(id) > 0;
        item.SetCollected(isCollected);
    }
}

```
✔ 불필요한 Debug.Log() 제거 → 값 변경 시만 출력하도록 변경 가능


---

✅ 6. SubtractFruitAndCalculateCoins() 최적화

🔹 기존 코드
```csharp
public bool SubtractFruitAndCalculateCoins(FruitsID fruitID, int amount)
{
    if (!GM.PlayerDataManager.NowPlayerData.Inventory.TryGetValue(fruitID, out var collectedFruit))
    {
        Debug.LogWarning($"과일 데이터({fruitID})를 찾을 수 없습니다.");
        return false;
    }

    if (!GM.DataManager.FruitDatas.TryGetValue(fruitID, out var fruitData))
    {
        Debug.LogWarning($"과일 정보({fruitID})를 찾을 수 없습니다.");
        return false;
    }

    if (collectedFruit.Amount < amount)
    {
        Debug.LogWarning($"{fruitID}의 수량이 부족하여 감소할 수 없습니다.");
        return false;
    }

    collectedFruit.Amount -= amount;
    GM.PlayerDataManager.NowPlayerData.PlayerCoin += fruitData.Price;
    return true;
}

```
🔺 문제점  
✔ TryGetValue()를 두 번 사용 (Inventory, FruitDatas)  
✔ GameManager에 GetFruitsData()와 GetCollectedFruitData() 메서드를 만들었으므로, 중복된 코드 제거 가능  

🔹 교체 후 코드
```csharp
public bool SubtractFruitAndCalculateCoins(FruitsID fruitID, int amount)
{
    var fruitData = GM.GetFruitsData(fruitID);
    if (fruitData == null)
    {
        Debug.LogWarning($"과일 데이터({fruitID})를 찾을 수 없습니다.");
        return false;
    }

    var collectedFruit = GM.GetCollectedFruitData(fruitID);
    if (collectedFruit.Amount < amount)
    {
        Debug.LogWarning($"{fruitID}의 수량이 부족하여 감소할 수 없습니다.");
        return false;
    }

    collectedFruit.Amount -= amount;
    GM.PlayerDataManager.NowPlayerData.PlayerCoin += fruitData.Price;
    return true;
}
```
✔ TryGetValue() 중복 호출 제거 → 성능 최적화  
✔ GameManager 활용하여 코드 가독성 향상  
✔ 데이터 접근 방식 일관성 유지 → 유지보수 용이  

---
🎯 오늘의 리팩토링 결론  
🚀 GameManager의 메서드를 활용하여 TryGetValue() 직접 호출 제거  
🚀 ContainsKey() vs TryGetValue() 적절한 사용으로 코드 최적화  
🚀 불필요한 데이터 관리 (_fruitSprites 등) 제거 → GameManager에서 직접 가져옴  
🚀 코드 가독성 개선 + 유지보수 편리해짐  
