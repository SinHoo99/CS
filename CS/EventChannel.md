# 🎮 Unity 공부 기록 (2025-03-31)

## 1. 오늘의 학습 목표 🎯
- [ ] ScriptableObject 기반 이벤트 채널 구조 이해하기
- [ ] 이벤트 발행자(Publisher)와 수신자(Subscriber) 역할 구분하기
- [ ] 실습 코드로 기본 EventChannel 구조 구현하기

---

## 2. 학습한 개념 📝
### 🔹 개념 1: ScriptableObject 기반 EventChannel
- 이벤트를 ScriptableObject로 에셋화하여 통신하는 구조
- 발행자와 수신자가 서로를 직접 참조하지 않아도 통신 가능 (느슨한 결합)
- Unity 에디터에서 드래그 앤 드롭으로 이벤트 연결 가능
- 이벤트 채널을 통해 여러 수신자가 동시에 하나의 이벤트를 받을 수 있음
### 🔹 개념 2: EventChannel 흐름
- 발행자(Publisher): Raise() 메서드를 호출하여 이벤트를 발행
- 수신자(Subscriber): OnEnable()에서 += 로 이벤트를 구독하고, OnDisable()에서 -= 로 구독 해제
- 데이터 전달: 이벤트는 단순한 데이터(string, class 등)를 담아 수신자에게 전달함

---

## 3. C# 코드 예제 💻
```csharp
// 🎯 ScriptableObject 이벤트 채널 정의
[CreateAssetMenu(menuName = "Events/PlayerDieEventChannel")]
public class PlayerDieEventChannel : ScriptableObject 
{
    public Action<string> OnEventRaised;

    public void Raise(string playerName)
    {
        OnEventRaised?.Invoke(playerName);
    }
}

```
```csharp
// 🎮 DummyPlayer: 이벤트 발행자
public class DummyPlayer : MonoBehaviour 
{
    [SerializeField] private PlayerDieEventChannel dieEventChannel;

    public void Die()
    {
        dieEventChannel.Raise("Player1");
    }
}

```
```csharp
// 🖥️ EventChannel: 이벤트 수신자
public class EventChannel : MonoBehaviour
{
    [SerializeField] private PlayerDieEventChannel dieEventChannel;
    [SerializeField] private GameObject panel;
    [SerializeField] private Text playerNameText;

    private void OnEnable()
    {
        dieEventChannel.OnEventRaised += OnPlayerDied;
    }

    private void OnDisable()
    {
        dieEventChannel.OnEventRaised -= OnPlayerDied;
    }

    private void OnPlayerDied(string playerName)
    {
        panel.SetActive(true);
        playerNameText.text = playerName;
    }
}
```
