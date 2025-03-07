# 🎮 Unity 공부 기록 (2025-03-07)

## 1. 오늘의 학습 목표 🎯
- [ ] FruitUIManager와 FruitItem 코드리뷰
- [ ] 리뷰하며 맞지않거나 좀더 최적화할 부분 개선


---

## 2. 학습한 개념 📝
### 🔹 FruitItem 기존 코드
```csharp
using TMPro;
using UnityEngine;
using UnityEngine.UI;

public class FruitItem : MonoBehaviour
{
    private GameManager GM => GameManager.Instance;

    public Button fruitButton;
    public TextMeshProUGUI fruitText;
    private FruitsID fruitID;

    /// <summary>
    /// 과일 아이템을 업데이트하고 fruitID를 설정하는 함수
    /// </summary>
    public void UpdateFruit(FruitsID id, int count, Sprite icon)
    {
        fruitID = id;

        // 버튼 이미지 설정
        if (fruitButton.TryGetComponent<Image>(out var buttonImage))
        {
            buttonImage.sprite = icon;
        }

        // 과일 개수 텍스트 업데이트
        fruitText.text = $"{GM.GetFruitsData(id).Name} {count}개 보유 \n 판매가격 : {GM.GetFruitsData(id).Price}";
    }

    /// 과일 버튼 클릭 시 실행되는 이벤트
    public void OnFruitButtonClicked()
    {
        GM.UIManager.InventoryManager.FruitUIManager.OnFruitSelected(fruitID);

        // ObjectPool에서 과일 오브젝트 가져오기
        var objToReturn = GM.ObjectPool.FindActiveObject(fruitID.ToString());

        if (objToReturn != null)
        {
            GM.ObjectPool.ReturnObject(fruitID.ToString(), objToReturn);

            if (SubtractFruitAndCalculateCoins(fruitID, 1))
            {
                Debug.Log($"{fruitID} 반환 완료, 코인 획득 및 UI 갱신 완료");
            }
            else
            {
                Debug.LogWarning($"과일 데이터({fruitID})가 존재하지 않아 보상을 지급할 수 없습니다.");
            }
        }
        else
        {
            Debug.LogWarning($"{fruitID}에 해당하는 활성화된 오브젝트가 없습니다.");
        }

        GM.UIManager.InventoryManager.TriggerInventoryUpdate();
    }

    /// 과일 수량 감소 및 코인 계산
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
}

```
### 📝 코드 리뷰 및 개선 사항

### 1️⃣ UpdateFruit()에서 GM.GetFruitsData(id) 중복 호출

### 🔹 현재 문제
```csharp
fruitText.text = $"{GM.GetFruitsData(id).Name} {count}개 보유 \n 판매가격 : {GM.GetFruitsData(id).Price}";
```
- GM.GetFruitsData(id)를 두 번 호출하고 있음 → 불필요한 함수 호출이 발생.
- 해결 방법: fruitData 변수를 미리 만들어 활용.

### ✅ 개선 코드
```csharp
public void UpdateFruit(FruitsID id, int count, Sprite icon)
{
    fruitID = id;
    var fruitData = GM.GetFruitsData(id);

    if (fruitData == null)
    {
        Debug.LogWarning($"과일 데이터({id})를 찾을 수 없습니다.");
        return;
    }

    // 버튼 이미지 설정
    if (fruitButton.TryGetComponent<Image>(out var buttonImage))
    {
        buttonImage.sprite = icon;
    }

    // 과일 개수 텍스트 업데이트
    fruitText.text = $"{fruitData.Name} {count}개 보유 \n 판매가격 : {fruitData.Price}";
}

```
### 📌 개선 포인트
- fruitData 변수를 만들어 중복 호출을 제거.
- fruitData == null 체크를 추가하여 예외 방지.

### 2️⃣ OnFruitButtonClicked()에서 FindActiveObject() 실행 최적화

### 🔹 현재 문제
```csharp
var objToReturn = GM.ObjectPool.FindActiveObject(fruitID.ToString());

if (objToReturn != null)
{
    GM.ObjectPool.ReturnObject(fruitID.ToString(), objToReturn);
```
- fruitID.ToString()을 두 번 변환하고 있음.
- FindActiveObject()가 null이면 ReturnObject() 호출이 필요 없음.
- 해결 방법: fruitID.ToString()을 미리 변수에 저장하고, objToReturn == null일 때 바로 반환.

### ✅ 개선 코드
```csharp
public void OnFruitButtonClicked()
{
    GM.UIManager.InventoryManager.FruitUIManager.OnFruitSelected(fruitID);

    // ObjectPool에서 과일 오브젝트 가져오기
    string fruitKey = fruitID.ToString();
    var objToReturn = GM.ObjectPool.FindActiveObject(fruitKey);

    if (objToReturn == null)
    {
        Debug.LogWarning($"{fruitID}에 해당하는 활성화된 오브젝트가 없습니다.");
        return;
    }

    GM.ObjectPool.ReturnObject(fruitKey, objToReturn);

    if (SubtractFruitAndCalculateCoins(fruitID, 1))
    {
        Debug.Log($"{fruitID} 반환 완료, 코인 획득 및 UI 갱신 완료");
    }
    else
    {
        Debug.LogWarning($"과일 데이터({fruitID})가 존재하지 않아 보상을 지급할 수 없습니다.");
    }

    GM.UIManager.InventoryManager.TriggerInventoryUpdate();
}
```
### 📌 개선 포인트
- fruitID.ToString()을 fruitKey 변수로 미리 저장하여 중복 연산을 줄임.
- objToReturn == null이면 바로 반환하여 불필요한 코드 실행 방지.

### 3️⃣ SubtractFruitAndCalculateCoins()의 collectedFruit.Amount 변경 로직 개선

### 🔹 현재 문제
```csharp
collectedFruit.Amount -= amount;
GM.PlayerDataManager.NowPlayerData.PlayerCoin += fruitData.Price;
```
- 과일 가격은 개당 가격이므로, 여러 개 판매할 경우 총 가격 계산이 빠짐.
- 현재 개수보다 더 많은 개수를 빼는 경우 방지 코드가 있긴 하지만, 구조가 깔끔하지 않음.

### ✅ 개선 코드
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
    if (collectedFruit == null || collectedFruit.Amount < amount)
    {
        Debug.LogWarning($"{fruitID}의 수량이 부족하여 감소할 수 없습니다.");
        return false;
    }

    collectedFruit.Amount -= amount;
    GM.PlayerDataManager.NowPlayerData.PlayerCoin += fruitData.Price * amount;
    return true;
}

```

### 📌 개선 포인트
- 총 가격을 정확히 계산하도록 fruitData.Price * amount 적용.
- collectedFruit == null 체크 추가하여 예외 방지.

### 4️⃣ FruitItem의 Button 클릭 이벤트 연결이 명확하지 않음 
현재 OnFruitButtonClicked()는 과일 버튼 클릭 시 호출되는데, 버튼 클릭 이벤트가 어디서 연결되는지 명확하지 않음.   

### ✅ 개선 코드 (Awake에서 버튼 이벤트 설정)
```csharp
private void Awake()
{
    if (fruitButton != null)
    {
        fruitButton.onClick.AddListener(OnFruitButtonClicked);
    }
}

```

### 📌 개선 포인트
- Awake()에서 fruitButton.onClick.AddListener(OnFruitButtonClicked) 호출하여 버튼 클릭 이벤트가 정상적으로 연결되도록 보장.

---

### 🔹 FruitUIManager  기존 코드
```csharp
using System.Collections.Generic;
using UnityEngine;

public class FruitUIManager : MonoBehaviour
{
    private GameManager GM => GameManager.Instance;

    public GameObject fruitItemPrefab;
    public Transform fruitListParent;

    private readonly Dictionary<FruitsID, FruitItem> _fruitUIItems = new();
    private readonly Dictionary<FruitsID, Sprite> _fruitSprites = new();
  
    public void SetFruitData(Dictionary<FruitsID, FruitsData> fruitData)
    {
        if (fruitData == null) return;

        _fruitSprites.Clear();
        foreach (var (id, data) in fruitData)
        {
            _fruitSprites[id] = data.Image;
        }
    }

    public void UpdateFruitCountsUI(Dictionary<FruitsID, int> fruitCounts)
    {
        foreach (var (fruitID, count) in fruitCounts)
        {
            if (count > 0) UpdateOrCreateFruitUI(fruitID, count);
            else RemoveFruitUI(fruitID);
        }
    }

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


    public void CreateFruitUI(FruitsID id, int count)
    {
        var fruitData = GM.GetFruitsData(id);
        if (fruitData == null)
        {
            Debug.LogWarning($"과일 데이터({id})를 찾을 수 없습니다.");
            return;
        }

        var fruitItemObject = Instantiate(fruitItemPrefab, fruitListParent);
        if (!fruitItemObject.TryGetComponent<FruitItem>(out var fruitItem))
        {
            Debug.LogError("FruitItemPrefab에 FruitItem 스크립트가 연결되지 않았습니다.");
            Destroy(fruitItemObject);
            return;
        }

        fruitItem.UpdateFruit(id, count, fruitData.Image);
        _fruitUIItems[id] = fruitItem;
    }

    public void RemoveFruitUI(FruitsID id)
    {
        if (!_fruitUIItems.ContainsKey(id)) return;

        Destroy(_fruitUIItems[id].gameObject);
        _fruitUIItems.Remove(id);
    }

    public void OnFruitSelected(FruitsID selectedFruitID)
    {
        var fruitData = GM.GetFruitsData(selectedFruitID);
        if (fruitData == null)
        {
            Debug.LogWarning($"ID {selectedFruitID}에 해당하는 과일 데이터를 찾을 수 없습니다.");
            return;
        }

        Debug.Log($"과일 선택됨: {selectedFruitID}");
    }

    public Sprite GetFruitImage(FruitsID id)
    {
        return GM.GetFruitsData(id)?.Image;
    }

}
```
### 📝 코드 리뷰 및 개선 사항

### 1️⃣ SetFruitData에서 _fruitSprites 딕셔너리의 역할이 애매함

### 🔹 현재 문제
```csharp
private readonly Dictionary<FruitsID, Sprite> _fruitSprites = new();
```
- _fruitSprites가 초기화되지만 GetFruitImage()에서 사용되지 않고 있음.
- GetFruitImage(id)에서는 _fruitSprites를 조회하지 않고, GM.GetFruitsData(id)?.Image를 직접 사용함.
- 개선 방향: _fruitSprites를 유지할 거면 GetFruitImage()에서 활용하도록 변경하거나, 불필요하면 삭제.

### 2️⃣ 2️⃣ UpdateOrCreateFruitUI()와 CreateFruitUI()의 중복된 로직 정리

### 🔹 현재 문제
1. UpdateOrCreateFruitUI()에서 _fruitUIItems에 존재하면 업데이트
2. 없으면 CreateFruitUI() 호출하여 새 UI 생성
3. CreateFruitUI()에서도 GM.GetFruitsData(id)를 다시 호출함
### 🔹 개선 방향
- CreateFruitUI()를 호출하기 전에 fruitData를 미리 가져와 중복 호출을 방지

### ✅ 개선 코드
```csharp
public void UpdateOrCreateFruitUI(FruitsID id, int count)
{
    if (!_fruitUIItems.TryGetValue(id, out var fruitItem))
    {
        var fruitData = GM.GetFruitsData(id);
        if (fruitData == null)
        {
            Debug.LogWarning($"과일 데이터({id})를 찾을 수 없습니다.");
            return;
        }

        fruitItem = CreateFruitUI(id, count, fruitData);
        if (fruitItem == null) return;
    }

    fruitItem.UpdateFruit(id, count, GetFruitImage(id));
}

private FruitItem CreateFruitUI(FruitsID id, int count, FruitsData fruitData)
{
    var fruitItemObject = Instantiate(fruitItemPrefab, fruitListParent);
    if (!fruitItemObject.TryGetComponent<FruitItem>(out var fruitItem))
    {
        Debug.LogError("FruitItemPrefab에 FruitItem 스크립트가 연결되지 않았습니다.");
        Destroy(fruitItemObject);
        return null;
    }

    fruitItem.UpdateFruit(id, count, fruitData.Image);
    _fruitUIItems[id] = fruitItem;
    return fruitItem;
}
```
### 📌 개선 포인트
- CreateFruitUI()는 FruitsData를 파라미터로 받도록 수정하여 불필요한 GM.GetFruitsData(id) 호출 제거.
- UpdateOrCreateFruitUI()에서 TryGetValue를 사용하여 null 체크를 좀 더 간결하게 함.
- CreateFruitUI()가 실패할 경우 null을 반환하여 이후 코드가 실행되지 않도록 방어 코드 추가.

### 3️⃣ UpdateFruitCountsUI()에서 RemoveFruitUI() 호출 최적화

### 🔹 현재 문제
```csharp
public void UpdateFruitCountsUI(Dictionary<FruitsID, int> fruitCounts)
{
    foreach (var (fruitID, count) in fruitCounts)
    {
        if (count > 0) UpdateOrCreateFruitUI(fruitID, count);
        else RemoveFruitUI(fruitID);
    }
}
```
### ✅ 개선 코드
```csharp
public void UpdateFruitCountsUI(Dictionary<FruitsID, int> fruitCounts)
{
    foreach (var (fruitID, count) in fruitCounts)
    {
        if (count > 0) 
            UpdateOrCreateFruitUI(fruitID, count);
        else if (_fruitUIItems.ContainsKey(fruitID)) 
            RemoveFruitUI(fruitID);
    }
}
```

### 📌 개선 포인트
- 이미 _fruitUIItems에 없는 아이템을 RemoveFruitUI(fruitID) 호출하면 딕셔너리 조회 비용이 낭비됨.
- RemoveFruitUI() 호출 전에 _fruitUIItems.ContainsKey(fruitID)로 체크하면 불필요한 호출 방지.

### 4️⃣ OnFruitSelected()에서 fruitData 가져오는 로직 통일

### 🔹 현재 문제
```csharp
public void OnFruitSelected(FruitsID selectedFruitID)
{
    var fruitData = GM.GetFruitsData(selectedFruitID);
    if (fruitData == null)
    {
        Debug.LogWarning($"ID {selectedFruitID}에 해당하는 과일 데이터를 찾을 수 없습니다.");
        return;
    }

    Debug.Log($"과일 선택됨: {selectedFruitID}");
}
```
### ✅ 개선 코드 (Awake에서 버튼 이벤트 설정)
```csharp
public void OnFruitSelected(FruitsID selectedFruitID)
{
    if (!_fruitUIItems.ContainsKey(selectedFruitID))
    {
        Debug.LogWarning($"선택한 과일 UI가 존재하지 않습니다: {selectedFruitID}");
        return;
    }

    Debug.Log($"과일 선택됨: {selectedFruitID}");
}

```

### 📌 개선 포인트
- GM.GetFruitsData(selectedFruitID)를 조회할 필요 없이 이미 _fruitUIItems에서 관리되는 데이터인지 체크하는 방식으로 변경.
- 불필요한 데이터 조회를 최소화하여 성능 최적화.


### 5️⃣ GetFruitImage()에서 _fruitSprites 활용

### 🔹 현재 문제  
현재 GetFruitImage()는 _fruitSprites를 전혀 활용하지 않음. 만약 SetFruitData()에서 _fruitSprites를 저장하는 이유가 있다면 활용하는 것이 좋음.  

### ✅ 개선 코드
```csharp
public Sprite GetFruitImage(FruitsID id)
{
    if (_fruitSprites.TryGetValue(id, out var sprite))
        return sprite;
    
    return null;
}
```

### 📌 개선 포인트
- _fruitSprites를 우선 조회하고 없을 경우 null 반환하여 불필요한 GM.GetFruitsData() 호출 방지.
---

