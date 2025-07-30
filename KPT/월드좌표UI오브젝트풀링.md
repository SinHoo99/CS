# World UI Damage Text Object Pooling Troubleshooting

## 🧭 처음에 구조 잡기

1. `PoolManager`를 이용하여 `DamageText` 프리팹을 미리 생성해두기
2. `TestEnemy` 스크립트에서 데미지를 받으면 프리팹을 활성화시키기
   - 이때 데미지 텍스트 프리팹은 ✅ **World 좌표 캔버스에 존재해야 자연스럽게 표현됨**
3. `DamageText.cs`를 만들어서 이 안에서 코루틴을 통해 자연스럽게 사라지도록 처리

---

## ❌ 문제 상황

- 텍스트 프리팹을 오브젝트 풀링으로 사용하고 싶었으나,
- `PoolManager`에서 생성되기 때문에 UI 요소인 텍스트가 **World Space Canvas에 존재하지 않아 보이지 않음**

---

## ⚙️ 시도했던 방법

### 1. `DamageTextSpawner` 중간 계층 사용
- 캔버스 밑에 두고 따로 오브젝트 생성 시도
- ❌ `PoolManager`와 `DamageTextSpawner`에서 **동일 프리팹이 중복 생성**됨
- 구조적으로 부자연스럽고, 관리 포인트가 늘어나므로 비추천

### 2. 프리팹에 `Canvas` 포함시키기
- ✅ 간단하긴 함
- ❌ 최적화 문제가 발생할 수 있음 → 프리팹마다 `Canvas` 생성은 매우 무거움 → 기각

---

## ✅ 해결 방법

1. `ObjectPool.cs`에 **World UI용 오브젝트 생성 함수**를 추가:

```csharp
public void AddObjectPoolInternal(string tag, PoolObject prefab, int size, Transform parent)
{
    List<PoolObject> objectPool = new List<PoolObject>();

    for (int i = 0; i < size; i++)
    {
        PoolObject obj = Instantiate(prefab, parent);
        obj.gameObject.SetActive(false);
        objectPool.Add(obj);
    }

    PoolDictionary.Add(tag, objectPool);
}
```

2. `PoolManager.cs`에서 `worldCanvasParent` Transform을 설정:

```csharp
[Header("WorldSpace Canvas 부모")]
[SerializeField] private Transform worldCanvasParent;
public Transform WorldCanvasParent => worldCanvasParent;
```

3. `TestEnemy.cs`에서 직접 UI 생성 호출:

```csharp
private void OnTriggerEnter2D(Collider2D collision)
{
    int testDamage = Random.Range(10, 50);
    ShowDamage(transform.position + Vector3.up * 1.5f, testDamage);
}

public void ShowDamage(Vector3 worldPos, int damage)
{
    var obj = ObjectPool.Instance.SpawnFromPool(Tag.DamageText);
    if (obj != null)
    {
        obj.transform.SetParent(PoolManager.Instance.WorldCanvasParent);
        obj.transform.position = worldPos;
        obj.GetComponent<DamageText>().Setup(damage.ToString());
    }
}
```

---

## 🧾 결론

- `DamageTextSpawner`는 구조상 불필요한 계층
- `ObjectPool`과 `PoolManager`가 역할을 잘 나눠 가지면 더 간결하고 유지보수도 쉬움
- ✅ 현재 구조는 유연성과 확장성을 모두 고려한 **최적 구조**
