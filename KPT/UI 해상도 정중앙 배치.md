# 🎮 Unity 공부 기록 (2025-02-27)

## 1. 오늘의 학습 목표 🎯
- [ ] 모든 해상도에서 UI가 정중앙에 위치하도록 수정
- [ ] DOTWeen을 활용한 UI 애니메이션 적용
- [ ] Canvas의 RenderMode별 UI 위치 조정 방식 이해

---

## 2. 학습한 개념 📝
### 🔹 개념 1: Unity에서 UI를 해상도에 맞게 정중앙으로 배치하는 방법
📌 문제 상황
- UI가 특정 해상도(예: 1080p)에서는 정상적으로 중앙에 표시되었지만, 다른 해상도에서는 중앙에서 벗어나는 문제 발생.
- ``targetPositionY = 820`` 같은 고정된 값을 사용했기 때문.

🔍 해결 방법  
    ✅ Screen.height * 0.5f를 사용하여 모든 해상도에서 정중앙 좌표 계산  
    ✅ Canvas.renderMode가 Screen Space - Camera일 경우 Camera.main.ScreenToWorldPoint()를 활용하여 정확한 위치 변환  
    ✅ RectTransformUtility.WorldToScreenPoint()를 사용하여 CanvasScaler 설정과 관계없이 UI가 중앙에 오도록 조정  

 ---
    
### 🔹 개념 2: DOTween을 활용한 UI 애니메이션 개선
📌 문제 상황
- UI를 활성화할 때 정중앙으로 이동하도록 했지만, 자연스러운 애니메이션이 부족했음.
- UI가 중앙으로 이동하는 과정에서 위치가 살짝 튀는 문제 발생.

🔍 해결 방법  
✅ UI를 이동하기 전에 Z 값을 먼저 조정하여 애니메이션 중 튀는 문제 방지  
✅ Ease.OutCubic을 사용하여 부드러운 이동 효과 적용  
✅ OnComplete()를 활용하여 비활성화 시 UI의 위치를 원래대로 복구  

---

## 3. C# 코드 예제 💻
```csharp
using DG.Tweening;
using UnityEngine;

public class UIManager : MonoBehaviour
{
    [SerializeField] private InventoryManager _inventoryManager;
    public InventoryManager InventoryManager => _inventoryManager;

    private GameObject currentActiveUI = null;
    private Vector3 originalPosition;
    private float frontZ = -5f;

    private void Start()
    {
        _inventoryManager.TriggerInventoryUpdate();
    }

    public void OnDoTween(GameObject uiObject, Vector3 originalPos)
    {
        bool isVisible = uiObject.activeSelf;

        if (!isVisible)
        {
            if (currentActiveUI != null && currentActiveUI != uiObject)
            {
                HideUI(currentActiveUI);
            }

            originalPosition = uiObject.transform.position;
            uiObject.transform.position = new Vector3(originalPosition.x, originalPosition.y, frontZ);

            // 해상도에 맞게 중앙 좌표 계산
            float targetPositionY = GetUIScreenCenterY(uiObject);

            uiObject.SetActive(true);
            uiObject.transform.DOMoveY(targetPositionY, 0.5f).SetEase(Ease.OutCubic);
            currentActiveUI = uiObject;
        }
        else
        {
            HideUI(uiObject);
        }
    }

    private void HideUI(GameObject uiObject)
    {
        uiObject.transform.DOMoveY(originalPosition.y, 0.5f).SetEase(Ease.InCubic)
            .OnComplete(() =>
            {
                uiObject.SetActive(false);
                ResetZPosition(uiObject);
            });

        if (currentActiveUI == uiObject)
            currentActiveUI = null;
    }

    private void ResetZPosition(GameObject uiObject)
    {
        uiObject.transform.position = new Vector3(originalPosition.x, originalPosition.y, originalPosition.z);
    }

    private float GetUIScreenCenterY(GameObject uiObject)
    {
        Canvas canvas = uiObject.GetComponentInParent<Canvas>();
        if (canvas == null)
        {
            return Screen.height * 0.5f;
        }

        if (canvas.renderMode == RenderMode.ScreenSpaceOverlay)
        {
            return Screen.height * 0.5f;
        }
        else
        {
            RectTransform canvasRect = canvas.GetComponent<RectTransform>();
            Vector3 screenCenter = new Vector3(Screen.width * 0.5f, Screen.height * 0.5f, 0);
            Vector3 worldCenter = Camera.main.ScreenToWorldPoint(screenCenter);
            return worldCenter.y;
        }
    }
}
```
---

## 4. KPT 회고 📌
✅ Keep (잘한 점)
1. Screen.height * 0.5f를 사용하여 모든 해상도에서 중앙 정렬 문제 해결
2. DOTween 애니메이션을 추가하여 UI 이동이 부드러워짐
3. Canvas.renderMode에 따른 위치 변환을 고려하여 모든 환경에서 정상 작동하도록 개선
---
🔄 Problem (어려웠던 점)
1. UI가 Screen Space - Camera일 때 위치 계산이 틀어지는 문제 발생
2. UI가 활성화될 때 Z값이 변경되지 않아 튀는 문제 발생
3. OnComplete()에서 UI를 원래 위치로 복귀시키는 과정에서 오류 발생
---
🚀 Try (다음에 개선할 점)
1. UIManager를 좀 더 범용적으로 만들기
    - 현재 OnDoTween()이 특정 UI에 종속적 → 더 유연하게 만들기
2. Tween 애니메이션을 Sequence로 리팩토링
    - UI가 순차적으로 나타나거나 사라질 수 있도록 조정
3. 캔버스의 스케일 설정을 동적으로 반영하는 방법 추가 연구
    - CanvasScaler 설정과 상관없이 항상 정중앙으로 배치되도록 연구 필요




