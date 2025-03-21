# 🎮 Unity 공부 기록 (2025-03-20)

## 1. 오늘의 학습 목표 🎯
- [ ] Unity에서 UI 설계 시 Action 기반 C# 이벤트의 적절한 사용 방법 이해하기
- [ ] UI 역할 분리(팝업, HUD 등)와 책임 분리(SRP)를 만족하는 구조 설계하기
- [ ] 인터페이스와 상속을 활용한 유연하고 유지보수 가능한 UI 아키텍처 설계 패턴 정리하기
---

## 2. 학습한 개념 📝
### 🔹 개념 1: UI 구조 설계의 원칙 – 역할과 책임의 분리

Unity에서 UI 시스템을 설계할 때 가장 중요하게 고려해야 할 것은 `UI의 책임과 역할을 분리하는 것`이다.  
하나의 UI가 너무 많은 기능을 처리하거나, 모든 UI가 비슷한 구조 없이 각자 알아서 구현되면  
결국 유지보수가 어렵고, UI 흐름을 제어하기 힘들어진다.

따라서 다음과 같은 3가지 원칙을 바탕으로 설계해야 한다:
    
    1. C# 이벤트는 Action 기반으로 개별 UI에서 정의하고 사용한다.
        → UI가 발생시키는 이벤트(버튼 클릭, 슬라이더 변경 등)는 해당 UI에서만 관리되어야 한다.
    
    2. 공통된 UI 기능(예: Refresh, Localize)은 인터페이스로 정의하여 필요할 때만 구현한다. 
        → 모든 UI가 갖지 않아도 되는 기능은 상속이 아닌 인터페이스로 부여해야 SRP를 지킬 수 있다.
    
    3. UI의 역할(팝업, HUD 등)은 추상 클래스(부모 클래스)로 나눠 상속받게 한다.  
        → UI의 구조나 기본 동작 방식이 유사하다면 공통 부모로 묶는 것이 유지보수에 좋다.

---
### 🔹 개념 2: 기반 UI 설계 – 구조와 동작의 분리
UI는 크게 두 가지 기준으로 나누어 설계할 수 있다.

✅ 역할에 따른 구조 (상속 사용)
| **역할** | **추상 클래스 (상속)** | **설명** |
|----------|------------------------|----------|
| 팝업 UI | `BasePopupUI` | 열고 닫는 UI, 애니메이션, 백그라운드 등 포함 |
| HUD UI | `BaseHUDUI` | 항상 화면에 고정되어 있는 상태바 등 |
| 일반 UI | `BaseUI` | 최소 기능만 포함된 기본 UI |

→ 역할은 UI가 어떤 형태인지에 따라 구분되므로, 추상 클래스를 사용해 구조를 공유하는 것이 적절하다.

✅ 기능에 따른 책임 (인터페이스 사용)
| **기능** | **인터페이스** | **설명** |
|----------|----------------|----------|
| 갱신(Refresh) | `IRefreshable` | 매 프레임 or 특정 조건에서 UI 업데이트 |
| 다국어 | `ILocalizable` | 언어 변경에 따라 텍스트 변경 |
| 데이터 바인딩 | `IBindable<T>` | 외부 데이터와 연결되어 자동 갱신되는 구조

→ 기능은 UI의 “부가적 역할”이므로, 필요할 때만 구현하도록 인터페이스로 분리하는 것이 SRP를 지키는 방법이다.

## 3. C# 코드 예제 💻
📌 1. UI 기능을 이벤트(Action)로 처리하는 예
```csharp
public class SettingsPopup : BasePopupUI
{
    // 외부에서 구독 가능한 이벤트
    public event Action<float> OnVolumeChanged;

    public void OnSliderValueChanged(float value)
    {
        OnVolumeChanged?.Invoke(value);
    }

    public void OnCloseButtonClicked()
    {
        ClosePopup();
    }
}

```
✅ 해당 UI의 이벤트는 해당 클래스 내부에서 정의하고 발생시킴
✅ 다른 클래스는 구독만 하면 됨 → 결합도 낮고 테스트 쉬움

---
📌 2. 공통 기능은 인터페이스로 분리해서 필요한 UI만 구현
```csharp
public interface IRefreshable
{
    void Refresh();
}
```
```csharp
public class ScoreUI : BaseHUDUI, IRefreshable
{
    public TextMeshProUGUI scoreText;

    public void Refresh()
    {
        scoreText.text = GameManager.Instance.Score.ToString();
    }
}

```
---
📌 3. UI 구조는 역할별 상속 구조로 통일
```csharp
public abstract class BaseUI : MonoBehaviour
{
    public virtual void Show() => gameObject.SetActive(true);
    public virtual void Hide() => gameObject.SetActive(false);
}

public abstract class BasePopupUI : BaseUI
{
    public virtual void OpenPopup() => Show();
    public virtual void ClosePopup() => Hide();
}

public abstract class BaseHUDUI : BaseUI
{
    // HUD 전용 로직 추가 가능
}
```
📌 4. UI 매니저가 IRefreshable을 한 번에 관리하는 예
```csharp
public class UIManager : MonoBehaviour
{
    private List<IRefreshable> _refreshUIs;

    private void Awake()
    {
        _refreshUIs = FindObjectsOfType<MonoBehaviour>(true)
            .OfType<IRefreshable>()
            .ToList();
    }

    private void Update()
    {
        foreach (var ui in _refreshUIs)
            ui.Refresh();
    }
}
```
✅ UIManager는 특정 역할만 처리 (ex. 공통 갱신, 팝업 흐름 제어 등)  
✅ 개별 UI의 로직에는 관여하지 않음 → 책임 분리 완벽

---
## 4. 최종 요약

| **구성 요소** | **사용 방식** | **목적** |
|----------------|------------------------------|--------------------------------|
| Action 이벤트 | 각 UI 내부에서 정의 | UI 외부와의 통신을 느슨하게 연결 |
| 인터페이스 | `IRefreshable`, `ILocalizable` 등 기능별 인터페이스 | 필요한 UI만 선택적으로 구현 |
| 상속 구조 | `BasePopupUI`, `BaseHUDUI` 등 역할별 추상 클래스 | 구조적 일관성과 재사용성 확보 |

