# 🎮 Unity 공부 기록 (2025-02-14)

## 1. 오늘의 학습 목표 🎯
-  Unity Addressables의 기본 개념 이해하기
-  Addressables을 활용한 메모리 최적화 방법 학습
-  Addressables을 사용하여 동적 애셋 로딩 구현하기

---

## 2. 학습한 개념 📝
### 🔹 개념 1: (Addressables 개요)
Addressables(어드레서블)는 Unity에서 제공하는 메모리 관리 및 애셋 로딩 시스템으로,
기존의 Resources.Load()나 AssetBundle 방식보다 더 강력한 최적화 기능을 제공함.

🔹 Addressables의 주요 특징  
✔️ 메모리 최적화 → 사용하지 않는 애셋을 자동 해제 가능  
✔️ 비동기 로딩 → 게임의 로딩 속도 개선  
✔️ 원격 애셋 지원 → 클라우드에서 애셋 다운로드 가능  
✔️ 의존성 자동 관리 → 필요한 애셋만 로드하여 용량 절감  

---


### 🔹 개념 2: (Addressables 설정 및 사용 방법)
(1) Addressables 설정하기  
1️⃣ Addressables 패키지 추가

Window → Package Manager에서 Addressables 검색 후 설치  
2️⃣ Addressables 시스템 활성화

Window → Asset Management → Addressables → Groups 클릭  
"Create Addressables Settings" 버튼 클릭  
3️⃣ 애셋을 Addressables로 등록  

프로젝트 내의 ```Prefab```, ```Material```, ```Texture``` 등을 선택  
```Inspector``` 창에서 "Addressable" 체크박스 활성화  
** 주소(Address) **를 설정하여 로드 가능하도록 만듦  

---

### 🔹 개념 3: (Addressables을 활용한 애셋 로딩)  
🔹 기본적인 Addressables 로딩 예제
```csharp
using UnityEngine;
using UnityEngine.AddressableAssets;
using UnityEngine.ResourceManagement.AsyncOperations;

public class AddressableLoader : MonoBehaviour
{
    public string addressableKey = "MyPrefab"; // Addressables에서 등록한 애셋의 Key 값

    void Start()
    {
        // 비동기 로딩 시작
        Addressables.LoadAssetAsync<GameObject>(addressableKey).Completed += OnAssetLoaded;
    }

    void OnAssetLoaded(AsyncOperationHandle<GameObject> obj)
    {
        if (obj.Status == AsyncOperationStatus.Succeeded)
        {
            Instantiate(obj.Result); // 성공적으로 로드되면 인스턴스화
        }
    }
}
```
✅ 비동기 로딩 지원 → Addressables.LoadAssetAsync<T>() 사용  
✅ 메모리를 효율적으로 관리 가능 → 사용 후 필요하면 해제 가능

---

### 🔹 개념 4: (Addressables 메모리 최적화 방법)  
(1) 불필요한 리소스 해제 (Unload)

```csharp
Addressables.Release(obj);
```
🔹 사용이 끝난 애셋은 반드시 해제해야 메모리 누수를 방지

```csharp
public string addressablePrefab = "EnemyPrefab";

void SpawnEnemy()
{
    Addressables.InstantiateAsync(addressablePrefab);
}
```
🔹 InstantiateAsync()를 사용하면 애셋을 직접 로드할 필요 없이 메모리 관리 가능

(2) Addressables.InstantiateAsync() 활용
```csharp
public string addressablePrefab = "EnemyPrefab";

void SpawnEnemy()
{
    Addressables.InstantiateAsync(addressablePrefab);
}
```
🔹 InstantiateAsync()를 사용하면 애셋을 직접 로드할 필요 없이 메모리 관리 가능


(3) 원격 애셋 관리 (Remote Catalog)  
 * Remote Catalog 기능을 활성화하면 게임 업데이트 없이도 서버에서 새로운 애셋 다운로드 가능
 * 게임 클라이언트가 특정 애셋을 필요할 때만 다운로드하도록 설정하여 용량 절약

---

## 3. C# 코드 예제 💻
1️⃣ Addressables을 활용한 비동기 Prefab 로딩
```csharp
using UnityEngine;
using UnityEngine.AddressableAssets;
using UnityEngine.ResourceManagement.AsyncOperations;

public class LoadPrefab : MonoBehaviour
{
    public string prefabKey = "PlayerPrefab"; // Addressables에서 등록한 Key 값

    void Start()
    {
        LoadPrefabAsync();
    }

    void LoadPrefabAsync()
    {
        Addressables.LoadAssetAsync<GameObject>(prefabKey).Completed += OnPrefabLoaded;
    }

    void OnPrefabLoaded(AsyncOperationHandle<GameObject> obj)
    {
        if (obj.Status == AsyncOperationStatus.Succeeded)
        {
            GameObject instance = Instantiate(obj.Result);
        }
    }
}
```
2️⃣ Addressables을 이용한 UI 이미지 로딩
```csharp
using UnityEngine;
using UnityEngine.UI;
using UnityEngine.AddressableAssets;
using UnityEngine.ResourceManagement.AsyncOperations;

public class LoadUIImage : MonoBehaviour
{
    public string imageKey = "MainMenuBackground";
    public Image targetImage;

    void Start()
    {
        LoadImage();
    }

    void LoadImage()
    {
        Addressables.LoadAssetAsync<Sprite>(imageKey).Completed += OnImageLoaded;
    }

    void OnImageLoaded(AsyncOperationHandle<Sprite> obj)
    {
        if (obj.Status == AsyncOperationStatus.Succeeded)
        {
            targetImage.sprite = obj.Result;
        }
    }
}
```

3️⃣ Addressables에서 원격 애셋 로드 (서버에서 다운로드)
```csharp
using UnityEngine;
using UnityEngine.AddressableAssets;
using UnityEngine.ResourceManagement.AsyncOperations;

public class LoadRemoteAsset : MonoBehaviour
{
    public string remoteAssetKey = "CloudTexture"; // 서버에 등록된 Addressables Key

    void Start()
    {
        Addressables.LoadAssetAsync<Texture2D>(remoteAssetKey).Completed += OnRemoteAssetLoaded;
    }

    void OnRemoteAssetLoaded(AsyncOperationHandle<Texture2D> obj)
    {
        if (obj.Status == AsyncOperationStatus.Succeeded)
        {
            Debug.Log("Remote Asset Loaded!");
        }
    }
}
```
✅ 서버에서 필요한 애셋만 다운로드 가능  
✅ 게임 업데이트 없이도 새로운 애셋 제공 가능  

---
4. 정리 및 결론 ✨  
1️⃣ 메모리 효율적으로 관리 가능  
2️⃣ 비동기 로딩 지원으로 프레임 속도 유지  
3️⃣ 서버에서 애셋 다운로드 가능 (게임 업데이트 부담 감소)  
4️⃣ 불필요한 애셋을 자동 해제하여 메모리 최적화  
5️⃣ 기존 AssetBundle보다 훨씬 편리하고 강력한 기능 제공  

💡 즉, Addressables을 활용하면 메모리 최적화된 Unity 프로젝트를 만들 수 있음! 🚀🔥
