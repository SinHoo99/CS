# 🎮 Unity 공부 기록 (YYYY-MM-DD)

## 1. 오늘의 학습 목표 🎯
- [ ] Event Bus의 개념을 정확히 이해하기
- [ ] Event Bus의 구현 방식 학습
- [ ] 실전 Unity 예제에 Event Bus 적용해보기

---

## 2. 학습한 개념 📝
### 🔹 개념 1: Event Bus란?
- 정의
    - `Event Bus(이벤트 버스)`는 객체 간 통신을 느슨하게(loosely coupled) 만들어주는 중앙 메시지 허브 역할을 하는 시스템이다.
- 동작 구조
    - Publisher (발행자): 이벤트를 발생시키는 쪽
    - Subscriber (구독자): 이벤트를 감지하고 반응하는 쪽
    - Event Bus: 양쪽을 연결시켜주는 중간 다리
- 왜 필요한가?
    - Unity에서 `GameManager`, `UI`, `Player` 등이 서로 직접 참조하면 결합도가 높아진다.
    - 유지보수, 확장성, 테스트가 어려워짐 → 이벤트 버스를 쓰면 이러한 강한 의존성 없이 이벤트로 통신 가능!
- 예시 상황
    - 플레이어가 죽었을 때, UI에 게임 오버 패널 표시
    - 아이템을 먹었을 때, 인벤토리에 추가
    - 씬이 바뀔 때, 음악 변경 등
### 🔹 개념 2: Event Bus의 타입 안전 구현
- 핵심 개념
    - 이벤트를 `string`이나 `int`로 구분하는 것이 아니라, `이벤트 타입(class, struct)`으로 구분함 → 타입 안전(Type-safe)
- 이점
    - 컴파일 시점에 타입 검사가 가능 (오타나 잘못된 캐스팅 방지)
    - 유지보수성과 가독성 향상
    - 구조화된 이벤트 데이터 전달 가능

---

## 3. C# 코드 예제 💻
```csharp
# region 타입 안전한 EventBus 시스템
public static class EventBus
{
    // 각 이벤트 타입(Type)별로 콜백(Action<object>)을 저장
    // object는 실제로는 이벤트 타입 T가 들어오며, T로 캐스팅되어 처리됨
    private static readonly Dictionary<Type, Action<object>> _events = new();

    // 원본 콜백과 래핑된 Action<object>를 매칭해서 저장
    //  → Unsubscribe 시 정확히 동일한 람다를 제거하기 위해 필요
    private static readonly Dictionary<Delegate, Action<object>> _delegateLookup = new();

    // 특정 타입의 이벤트(T)를 구독 등록함
    // 이벤트 발생 시 callback(T)가 호출됨
    public static void Subscribe<T>(Action<T> callback)
    {
        Action<object> wrapper = (obj) => callback((T)obj);
        _delegateLookup[callback] = wrapper;

        if(_events.TryGetValue(typeof(T), out var existing))
            _events[typeof(T)] += wrapper;
        else
            _events[typeof(T)] = wrapper;
    }
    // 특정 타입의 이벤트(T) 구독을 해제함
    // 등록한 콜백과 일치하는 래퍼만 제거됨
    public static void Unsubscribe<T>(Action<T> callback)
    {
        if (_delegateLookup.TryGetValue(callback, out var wrapper))
        {
            if (_events.TryGetValue(typeof(T), out var existing))
                _events[typeof(T)] -= wrapper;

            _delegateLookup.Remove(callback);
        }
    }
    // T 타입 이벤트를 발행(Publish)하여 등록된 모든 콜백을 호출함
    // "evt"가 object로 들어감
    public static void Publish<T>(T evt)
    {
        if (_events.TryGetValue(typeof(T), out var action))
            action?.Invoke(evt);
    }
}

// 1. 이벤트 데이터 타입 정의
// 이벤트가 발생할 때 전달할 데이터를 담는 구조체나 클래스를 생성 해야함
public class PlayerDieEvent
{
    public string playerName;
    public PlayerDieEvent(string name)
    {
        playerName = name;
    }
}

public class Player : MonoBehaviour 
{
    // 이벤트 발행
    void Die()
    {
        EventBus.Publish(new PlayerDieEvent("Player1"));
    }
}

public class UIManager : PersistenSingleton<UIManager> 
{
    [SerializeField] private GameObject gameOverPanel;
    protected override void Awake()
    {
        base.Awake();
    }
    // 이벤트 구독
    private void OnEnable()
    {
        EventBus.Subscribe<PlayerDieEvent>(OnPlayerDied);
    }

    private void OnDisable()
    {
        EventBus.Unsubscribe<PlayerDieEvent>(OnPlayerDied);
    }

    void OnPlayerDied(PlayerDieEvent e)
    {
        Debug.Log("플레이어 사망" + e.playerName);
        gameOverPanel.SetActive(true);
    }
}

#endregion
```

## 4. 이벤트 버스를 활용한 컴포넌트 독립화(각 이벤트는 임시로 만든 이벤트임으로 중복문제는 신경쓰지않음)
```csharp
#region 이벤트 버스활용 컴포넌트 독립화 과정 만들어보기
public class AObject : MonoBehaviour
{
    public GameObject AObj;
    public Text AObjTxt;

    private void OnEnable()
    {
        EventBus.Subscribe<AObjectEvent>(OnAobjectEvent);
    }

    private void OnDisable()
    {
        EventBus.Unsubscribe<AObjectEvent>(OnAobjectEvent);
    }

    void OnAobjectEvent(AObjectEvent e)
    {
        Debug.Log("플레이어 사망" + e.playerName);
        AObjTxt.text = e.playerName;
        AObj.SetActive(true);
    }
}
public class BObject : MonoBehaviour
{
    public GameObject BObj;
    public Text BObjTxt;

    private void OnEnable()
    {
        EventBus.Subscribe<BObjectEvent>(OnBobjectEvent);
    }

    private void OnDisable()
    {
        EventBus.Unsubscribe<BObjectEvent>(OnBobjectEvent);
    }

    void OnBobjectEvent(AObjectEvent e)
    {
        Debug.Log("플레이어 사망" + e.playerName);
        BObjTxt.text = e.playerName;
        BObj.SetActive(true);
    }
}
public class CObject : MonoBehaviour 
{
    public GameObject CObj;
    public Text CObjTxt;

    private void OnEnable()
    {
        EventBus.Subscribe<CObjectEvent>(OnCobjectEvent);
    }

    private void OnDisable()
    {
        EventBus.Unsubscribe<CObjectEvent>(OnCobjectEvent);
    }

    void OnCobjectEvent(AObjectEvent e)
    {
        Debug.Log("플레이어 사망" + e.playerName);
        CObjTxt.text = e.playerName;
        CObj.SetActive(true);
    }
}


public class AObjectEvent
{
    public string playerName;
    public AObjectEvent(string name)
    {
        playerName = name;
    }
}

public class BObjectEvent
{
    public string playerName;
    public BObjectEvent(string name)
    {
        playerName = name;
    }
}

public class CObjectEvent
{
    public string playerName;
    public CObjectEvent(string name)
    {
        playerName = name;
    }
}
#endregion
```
