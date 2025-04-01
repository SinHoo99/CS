# 🎮 Unity 공부 기록 (2025-04-01)

## 1. 오늘의 학습 목표 🎯
- [ ] DOTween 설치 및 기본 사용법 이해
- [ ] 다양한 Tween 기능(이동, 회전, 스케일, 색상 등) 학습
- [ ] Sequence를 활용한 연출 제작

---

## 2. 학습한 개념 📝
### 🔹 개념 1:  DOTween이란?
- DOTween은 Unity에서 애니메이션을 부드럽고 효율적으로 제어할 수 있는 트위닝(Tweening) 라이브러리이다.
- Unity에서의 기본 애니메이션 시스템(Animator 또는 Lerp 등)보다 더 직관적이고 강력하며 가볍다는 장점이 있다.

---

### 🔹 개념 2: DOTween 설치 및 초기 설정
✅ 설치 방법:
1. Asset Store에서 "DOTween" 검색 후 다운로드

2. 또는 [Package Manager] > [Add package from git URL]:

```bash
https://github.com/Demigiant/dotween
```
3. 설치 후 상단 메뉴바에서 Tools > Demigiant > DOTween Utility Panel 실행 → Setup DOTween 클릭하여 설정 완료

✅ 설정 체크사항:
- DOTween은 컴파일 타임에 스크립트를 자동 생성하므로, 설치 후 반드시 Setup을 해줘야 오류가 발생하지 않음

---

### 🔹 개념 3: 기본 사용법
- DOTween을 사용하려면, 항상 using DG.Tweening; 네임스페이스를 추가해야 함.
```csahrp
using DG.Tweening;
```
- 예시: 위치 이동
```csahrp
transform.DOMove(new Vector3(5, 0, 0), 1f); // 1초 동안 (5, 0, 0)으로 이동
```
- 기본 문법 구조:
```csahrp
대상.DO동작(목표값, 지속시간).옵션();
```
---

### 🔹 개념 4: 주요 기능 요약
1. 이동 관련 (Position)
```csahrp
transform.DOMove(Vector3 position, float duration);
transform.DOMoveX(float x, float duration);
transform.DOMoveY(float y, float duration);
```
2. 회전 관련 (Rotation)
```csahrp
transform.DORotate(new Vector3(0, 180, 0), 1f); // 로컬 회전
transform.DOLocalRotate(...); // 로컬 회전
```
3. 크기 조절 (Scale)
```csahrp
transform.DOScale(new Vector3(2, 2, 2), 0.5f);
```
4. 투명도 / 색상 (UI 또는 Renderer)
```csahrp
spriteRenderer.DOColor(Color.red, 1f); 
canvasGroup.DOFade(0, 1f); // UI 페이드 아웃
```
5. Delay, Loop, Ease
```csahrp
// 1초 뒤에 실행, 3번 반복, EaseOutBounce 곡선 사용
transform.DOMoveX(5, 1f)
         .SetDelay(1f)
         .SetLoops(3, LoopType.Yoyo)
         .SetEase(Ease.OutBounce);
```
---
### 🔹 개념 5: Sequence로 여러 Tween 연결
```csahrp
Sequence seq = DOTween.Sequence();
seq.Append(transform.DOMoveX(2, 1f));
seq.Append(transform.DOScale(1.5f, 0.5f));
seq.Append(transform.DORotate(new Vector3(0, 180, 0), 1f));
```
- Append: 이전 트윈이 끝난 후 실행
- Join: 동시에 실행
- Insert: 특정 시점에 삽입
---
### 🔹 개념 6: 콜백 (Callback)
```csahrp
transform.DOMoveX(5, 1f)
         .OnStart(() => Debug.Log("시작!"))
         .OnComplete(() => Debug.Log("완료!"));
```
콜백 종류:
- OnStart()
- OnUpdate()
- OnComplete()
- OnKill()
---
### 🔹 개념 7: Tween 제어 함수
```csahrp
Tween tween = transform.DOMoveX(5, 1f);
tween.Pause();
tween.Play();
tween.Kill(); // 즉시 종료
```
또는 전역 제어:
```csahrp
DOTween.PauseAll();
DOTween.KillAll();
```
---
### 🔹 개념 8:  DOTween 활용 예시
UI 버튼 클릭 시 스케일 튕기기
```csahrp
public void OnClickButton()
{
    transform.DOScale(1.2f, 0.1f)
             .SetLoops(2, LoopType.Yoyo);
}
```
오브젝트 반복 흔들림
```csahrp
transform.DOShakePosition(1f, strength: 1f, vibrato: 10)
         .SetLoops(-1, LoopType.Restart); // 무한 반복
```
---
## 3. C# 코드 예제 💻
```csharp
// 플레이어가 특정 위치로 이동하고 회전한 뒤, 스케일을 커지게 만드는 DOTween Sequence 예제

using UnityEngine;
using DG.Tweening;

public class PlayerAnimation : MonoBehaviour
{
    void Start()
    {
        Sequence seq = DOTween.Sequence();
        seq.Append(transform.DOMove(new Vector3(5, 0, 0), 1f));
        seq.Append(transform.DORotate(new Vector3(0, 180, 0), 0.5f));
        seq.Append(transform.DOScale(Vector3.one * 2, 0.5f));
        seq.OnComplete(() => Debug.Log("애니메이션 완료!"));
    }
}
```
