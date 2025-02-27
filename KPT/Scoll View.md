# 🎮 Unity 공부 기록 (YYYY-MM-DD) 
## 1. 오늘의 학습 목표 🎯
- [ ] Scroll View를 활용한 동적 UI 스크롤 문제 해결
- [ ] Content 크기 자동 조정 및 스크롤 정상 작동 확인
- [ ] 스크롤이 상하로만 동작하고, 자동으로 원래 위치로 돌아가는 문제 해결
- [ ] Scroll View의 주요 기능 및 설정 방법 정리
- [ ] 스크롤 오류 발생 시 해결 방법 이해

---


## 2. 학습한 개념 📝
### 🔹 개념 1: ( Unity Scroll View에서 UI가 정상적으로 스크롤되도록 설정하기)
📌 문제 상황
1. 스크롤이 상하뿐만 아니라 좌우로도 움직이는 문제 발생
2. 스크롤을 내리면 자동으로 원래 위치로 돌아가는 문제
3. 스크롤이 작동하지 않거나, 추가한 아이템이 보이지 않는 문제 발생

✅ 해결 방법
1. Scroll View 설정 수정
   - ``Scroll Rect``에서 ``Horizontal`` 비활성화 (가로 스크롤 방지)
   - ``Movement Type``을 ``Clamped``로 변경 (Elastic 제거)
2. Content 크기가 자동으로 늘어나도록 설정
   - ``Content`` 오브젝트에 ``Content Size Fitter`` 추가
   - ``Vertical Fit`` → ``Preferred Size`` 설정
3. Scroll View가 정상적으로 작동하지 않는 주요 원인 및 해결법
   
   | 문제 상황 | 해결 방법 |
   | ---------| -------- |
   | 스크롤이 좌우로 움직인다 | Scroll Rect에서 ``Horizontal`` 체크 해제 |
   | 스크롤을 내려도 자동으로 원래 위치로 돌아간다 | ``Movement Type``을 ``Elastic → Clamped``로 변경  |
   | 스크롤이 안 된다 | ``Content`` 크기가 ``Viewport``보다 작은지 확인 & ``Content Size Fitter`` 설정  |
---

### 🔹 개념 2: (Scroll View 기본 구조 및 기능)
1. Scroll View 기본 구조 및 기능
   - Scroll View는 Unity에서 스크롤 가능한 UI를 구현하는 기본적인 방법으로, 다음과 같은 주요 컴포넌트로 구성됨.

   | 컴포넌트 | 설명 |
   | ---------| -------- |
   | Scroll Rect |스크롤 동작을 제어하는 핵심 컴포넌트 |
   | Viewport | 스크롤 가능한 영역을 제한하는 마스크 역할  |
   | Content | 실제 UI 아이템이 배치되는 영역  |
   | Scrollbar (Optional) | 스크롤바를 추가하여 사용자가 스크롤 가능하도록 설정 |
✅ Scroll View를 사용하면 스크롤이 필요한 UI 콘텐츠를 쉽게 관리할 수 있음.

2. Scroll View의 주요 기능 및 설정

    | 기능 | 설명 |  설정 방법 |
   | ---------| -------- |  ------ |
   | 세로/가로 스크롤 설정 |스크롤이 수직 또는 수평으로 움직이도록 설정 |  ``Scroll Rect`` → ``Horizontal``, ``Vertical`` 체크 설정 |
   | 스크롤 제한 (Clamped) | 스크롤이 끝을 넘어서지 않도록 설정  |  ``Scroll Rect → Movement Type: Clamped`` |
   | 탄성 효과 (Elastic) | 스크롤이 끝에서 튕기는 효과 적용 |  ``Scroll Rect → Movement Type: Elastic`` |
   | 무한 스크롤 (Unrestricted) | 스크롤을 제한 없이 가능하도록 설정 |  ``Scroll Rect → Movement Type: Unrestricted`` |
   | 컨텐츠 크기 자동 조정 | 아이템 추가 시 Content 크기를 자동으로 늘리기 |  ``Content Size Fitter → Vertical Fit: Preferred Size`` |
   | 아이템 자동 정렬 | 아이템이 일정한 간격으로 정렬되도록 설정 |  ``Vertical Layout Group`` 적용 |
   | 스크롤바 추가 | 수직/수평 스크롤바 추가 가능 |  ``Scrollbar``을 ``Scroll Rect``에 연결 |
✅ 이 기능들을 적절히 조합하면 원하는 스크롤 UI를 구현할 수 있음.

3. 개념 3: Scroll View 사용 시 발생할 수 있는 문제 & 해결 방법
   
    | 문제 상황 | 원인 |  해결 방법 |
   | ---------| -------- |  ------ |
   | 스크롤이 좌우로 움직인다 |`Scroll Rect`에서 `Horizontal`이 활성화됨 |  ``Horizontal`` 체크 해제 |
   | 스크롤을 내리면 자동으로 원래 위치로 돌아감 | `Movement Type`이 `Elastic`으로 설정됨  |  `Clamped` 로 변경 |
   | 스크롤이 작동하지 않음 | Content 크기가 Viewport보다 작음 |  ``Content Size Fitter`` 설정 `(Preferred Size)`|
   | 새로운 아이템이 추가될 때 자동으로 스크롤이 내려가지 않음 | `Scroll Rect`가 갱신되지 않음 | `Canvas.ForceUpdateCanvases()`; 실행|
   | 스크롤바(Handle)가 안 보임 | `Handle Rect가 할당되지 않음` |  `Scrollbar`에서 `Handle Rect` 확인 |
    | 스크롤이 너무 빠르거나 느림 | `Scroll Sensitivity` 값이 적절하지 않음 |  `Scroll Rect → Scroll Sensitivity` 조정 |
---
## 3. 🎯 최종 정리  
✔️ Scroll View는 Scroll Rect, Viewport, Content, Scrollbar로 구성됨  
✔️ Content Size Fitter와 Vertical Layout Group을 조합하면 동적 UI도 쉽게 스크롤 가능  
✔️ 스크롤이 예상대로 동작하지 않을 경우, Scroll Rect 및 Content 크기를 확인  
✔️ 스크롤 속도, 제한 설정, 자동 스크롤 등 다양한 옵션을 활용하여 최적의 UI 구현 가능  

![Scroll view](https://github.com/user-attachments/assets/47db67b0-890c-4b47-8519-069a7cf8cd85)
- 완성
