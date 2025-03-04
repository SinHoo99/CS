# 🎮 Unity 공부 기록 (2025-03-04)

## 1. 오늘의 학습 목표 🎯
- [ ] 9-Slice에 대해 자세히 알아보기

---

## 2. 학습한 개념 📝
### 🔹 개념 1: (9-Sliced Scaling) 정리
- 9-Slice(또는 9-Sliced Scaling)는 UI 요소(버튼, 패널, 배경 등)의 크기를 변경할 때 모서리를 유지하면서 중앙 부분만 늘어나는 기법.
- 이를 통해 해상도에 따라 UI가 왜곡되지 않고 자연스럽게 확대/축소될 수 있음.

---
### 🔹 개념 2: (9-Slice의 구조)
9-Slice는 이미지를 9개의 영역으로 나누고, 각 영역을 다르게 처리해서 크기를 조정하는 방식  

![image](https://github.com/user-attachments/assets/ff1dea9e-0590-4993-b9bb-11798660452d)  
각 영역의 역할
- 모서리(4개: A, C, G, I)  
    → 크기 변경 없이 그대로 유지 (왜곡 X)  
- 가장자리(4개: B, D, F, H)  
    → 수직 또는 수평으로만 늘어남  
- 가운데(1개: E)  
    → 자유롭게 확장 (Stretch)
- 이 방식을 사용하면 버튼이나 UI 패널을 다양한 크기로 확장하더라도 모서리가 유지되고 깔끔하게 표시됨.
---

### 🔹 개념 3: (Unity에서 9-Slice 설정 방법)
1. Sprite의 Border 설정
- Project 창에서 UI에 사용할 Sprite 이미지를 선택.
- Sprite Editor 버튼을 클릭.
- Sprite Editor에서 좌우/상하 Border 값을 조정 (픽셀 단위 입력).
- 보통 모서리 부분의 크기(예: 4px~16px) 를 Border로 설정.
- Border가 너무 작으면 왜곡되고, 너무 크면 내부 영역이 줄어듦.
- Apply 버튼을 눌러 적용.

2. UI에서 9-Sliced 적용
- Canvas 안에 UI > Image 추가.
- Image 컴포넌트에서 Source Image를 9-Slice가 적용된 Sprite로 변경.
- Image Type을 Simple → Sliced로 변경.
- 필요한 경우 Fill Center 옵션 체크/해제:
- 체크하면 중앙 부분이 유지되면서 확장.
- 해제하면 중앙이 비워진 상태에서 테두리만 유지됨.
---

### 🔹 개념 4: (9-Slice 활용 시 주의할 점)
🔹 모서리(Border) 크기를 너무 작게 설정하면 이미지가 깨질 수 있음.  
🔹 Filter Mode는 Point (No Filter)로 설정 (픽셀 아트 깨짐 방지).  
🔹 UI 비율이 중요한 경우 Preserve Aspect 설정 고려.  
🔹 해상도 대응을 위해 CanvasScaler 설정 필수 (Scale With Screen Size).  

