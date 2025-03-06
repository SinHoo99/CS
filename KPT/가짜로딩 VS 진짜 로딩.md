# 🎮 Unity 공부 기록 (2025-03-06)

# 1. 오늘의 학습 목표 🎯
- [ ] 가짜 로딩과 진짜 로딩 씬의 차이점 이해
- [ ] 가짜 로딩과 진짜 로딩을 Unity에서 구현해보기
- [ ] 어떤 경우에 각각의 방식을 적용해야 하는지 분석

---

# 2. 학습한 개념 📝
## 🔹 개념 1: 가짜 로딩 씬 (Fake Loading Scene)

### 설명:  
- SceneManager.LoadScene("GameScene") 호출 전에 일정 시간 동안 UI만 보여주는 방식.  
- 실제 데이터 로딩 없이, 프로그래스 바를 조작해 사용자가 기다리는 느낌을 연출.

### 장점:
✅ 로딩 시간을 개발자가 직접 조절 가능 → 짧거나 일정한 로딩 시간을 유지 가능  
✅ 사용자가 기다리는 느낌을 줄 수 있음 → UI 애니메이션으로 부드러운 전환  
✅ 씬을 바로 로드할 수 있음 → SceneManager.LoadScene() 호출만으로 전환  

### 단점:  
❌ 실제 데이터 로딩을 하지 않기 때문에, 게임 씬에서 오브젝트 로딩이 느려질 가능성 있음  
❌ 게임 씬의 초기 로딩이 길어질 수 있음 → 진짜 로딩이 필요한 경우에는 부적절  

### 언제 사용하면 좋을까?  
- 씬 전환이 빠르고, 별다른 리소스 로드가 필요하지 않을 때
- 플레이어가 기다리는 느낌을 줄 필요가 있을 때 (연출용)
- 모바일 게임에서 빠른 전환이 필요할 때

### 정리: 
- 일정한 시간 동안 프로그래스 바만 증가하며, 실제 데이터 로드는 없음.
- SceneManager.LoadScene()을 사용하여 씬 전환
- 빠른 씬 전환이 필요하거나, 연출을 위해 사용됨.

## 🔹 개념 2: 진짜 로딩 씬 (Real Loading Scene)

### 설명:
- SceneManager.LoadSceneAsync("GameScene")을 사용하여 비동기 로딩(Async Loading) 진행.
- asyncLoad.progress 값을 이용해 실제 로딩 진행률을 UI에 표시.

### 장점:
✅ 실제 데이터 로딩이 진행됨 → 씬이 준비되면 바로 플레이 가능 
✅ 로딩이 완료될 때까지 씬이 전환되지 않으므로, 버벅임 없이 부드러운 플레이 가능  
✅ 큰 씬을 로딩할 때 적합 → 초기 프레임 드롭 방지  

### 단점:
❌ 로딩 시간이 기기 성능이나 데이터 양에 따라 변동됨 → 일정한 로딩 시간이 보장되지 않음  
❌ 로딩 UI가 너무 짧거나 길어질 수 있음 → 유저 경험이 예측하기 어려움  
❌ 씬이 로딩되기 전까지 게임이 완전히 멈춘 상태 → 로딩 중에도 움직이는 UI/애니메이션 추가 필요  

### 언제 사용하면 좋을까?  
- 씬 로드 시 많은 오브젝트, 프리팹, 리소스가 필요할 때
- 게임 씬에서 버벅임 없이 부드러운 플레이가 필요할 때
- AAA급 게임이나 고사양 씬 로딩이 필요한 경우

### 정리: 
- SceneManager.LoadSceneAsync()를 사용하여 비동기 로딩 진행.
- asyncLoad.progress 값을 사용하여 실제 로딩 진행 상황을 표시.
- 게임 오브젝트가 많거나, 씬 전환 시 프레임 드랍이 발생할 경우 사용됨.

---
## 3. C# 코드 예제 💻

✅ 가짜 로딩 씬 구현
```csharp
using System.Collections;
using TMPro;
using UnityEngine;
using UnityEngine.SceneManagement;
using UnityEngine.UI;

public class FakeLoadingManager : MonoBehaviour
{
    public Slider progressBar;
    public TextMeshProUGUI progressText;

    private void Start()
    {
        StartCoroutine(FakeLoading());
    }

    private IEnumerator FakeLoading()
    {
        float progress = 0f;
        while (progress < 1f)
        {
            progress += Time.deltaTime * 0.5f; // 2초 동안 100% 도달
            progressBar.value = progress;
            progressText.text = $"Loading... {Mathf.FloorToInt(progress * 100)}%";
            yield return null;
        }

        SceneManager.LoadScene("GameScene");
    }
}

```
✅ 진짜 로딩 씬 구현
```csharp
using System.Collections;
using TMPro;
using UnityEngine;
using UnityEngine.SceneManagement;
using UnityEngine.UI;

public class RealLoadingManager : MonoBehaviour
{
    public Slider progressBar;
    public TextMeshProUGUI progressText;

    private void Start()
    {
        StartCoroutine(LoadSceneAsync());
    }

    private IEnumerator LoadSceneAsync()
    {
        AsyncOperation asyncLoad = SceneManager.LoadSceneAsync("GameScene");
        asyncLoad.allowSceneActivation = false;

        while (!asyncLoad.isDone)
        {
            float progress = Mathf.Clamp01(asyncLoad.progress / 0.9f);
            progressBar.value = progress;
            progressText.text = $"Loading... {Mathf.FloorToInt(progress * 100)}%";

            if (asyncLoad.progress >= 0.9f)
            {
                asyncLoad.allowSceneActivation = true;
            }

            yield return null;
        }
    }
}

```
---
## 4. ✅ 최종 결론  
### 📌 가짜 로딩 씬은?  
✅ 일정한 로딩 시간을 유지하고 싶을 때  
✅ 빠른 씬 전환이 필요할 때  
✅ 연출을 위해 부드러운 UI 애니메이션을 원할 때  

### 📌 진짜 로딩 씬은?  
✅ 씬이 복잡하고 많은 리소스를 불러와야 할 때  
✅ 씬 전환 후 프레임 드랍 없이 플레이하고 싶을 때  
✅ 실제 데이터 로딩 진행률을 유저에게 보여주고 싶을 때  
