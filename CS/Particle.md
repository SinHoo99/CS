# 🎇 Unity Particle System – 모든 모듈 및 속성 설명서 (세부 항목별 완전 설명)

> 이 문서는 Unity의 파티클 시스템의 모든 모듈을 포함하며, 각 모듈의 세부 설정이 어떤 역할을 하는지를 상세히 설명합니다.

---

## 🔹 Emission

### ▪️ Enabled
- Emission 모듈을 활성화할지 여부를 결정합니다.

### ▪️ Rate over Time
- 초당 생성되는 입자의 수를 지정합니다.
- Curve를 통해 시간에 따라 변화시킬 수 있습니다.

### ▪️ Rate over Distance
- 파티클 시스템이 이동한 거리당 생성되는 입자의 수를 설정합니다.

### ▪️ Bursts
- 특정 시간에 일시적으로 다량의 입자를 생성합니다.
  - **Time**: 버스트가 발생하는 시점 (초)
  - **Count**: 생성되는 입자의 수
  - **Cycle Count**: 반복 횟수
  - **Interval**: 반복 주기
  - **Probability**: 버스트가 발생할 확률

---

## 🔹 Shape

### ▪️ Shape Type
- 입자가 방출되는 영역의 형태를 설정합니다.
- 예: Cone(원뿔), Sphere(구), Box(박스), Circle(원형)

### ▪️ Angle / Radius / Length / Arc
- 선택한 형태에 따라 입자가 퍼지는 범위와 방향을 설정합니다.

### ▪️ Position / Rotation / Scale
- 생성 지점의 오프셋, 회전, 크기 설정

### ▪️ Random Direction Amount
- 입자의 방출 방향을 랜덤하게 설정할 비율 (0~1)

### ▪️ Align to Direction
- 입자의 회전이 이동 방향을 따를지 여부

---

## 🔹 Velocity over Lifetime

### ▪️ Enabled
- 이 모듈을 사용할지 여부

### ▪️ Linear X, Y, Z
- 입자의 수명 동안 각 축 방향으로의 속도를 지정합니다.
- Curve를 통해 시간에 따라 속도를 제어할 수 있습니다.

### ▪️ Separate Axes
- 축별로 다른 Curve를 설정할 수 있도록 합니다.

### ▪️ Space
- Local 또는 World 기준으로 적용할지 선택합니다.

---

## 🔹 Limit Velocity over Lifetime

### ▪️ Speed
- 입자의 최대 속도를 제한합니다.

### ▪️ Dampen
- 속도가 제한을 넘었을 때 감쇠시키는 비율을 설정합니다.

### ▪️ Separate Axes
- 각 축에 대해 개별로 제한 속도를 설정할 수 있습니다.

---

## 🔹 Inherit Velocity

### ▪️ Mode
- 부모 오브젝트의 속도를 계승할 방식 선택 (Initial/Current)

### ▪️ Multiplier
- 부모 속도 계승 비율 설정

---

## 🔹 Lifetime by Emitter Speed

### ▪️ Curve
- 시스템 속도에 따라 입자 수명을 조정하는 커브

---

## 🔹 Force over Lifetime

### ▪️ X, Y, Z
- 입자에 지속적으로 가해지는 힘을 각 축별로 설정합니다.

### ▪️ Space
- Local 또는 World 기준으로 힘을 적용할지 결정합니다.

---

## 🔹 Color over Lifetime

### ▪️ Color
- 입자의 수명 동안 색상이 어떻게 변할지를 Gradient로 설정합니다.

---

## 🔹 Color by Speed

### ▪️ Speed Range
- 속도에 따라 색상이 적용되는 범위

### ▪️ Color
- 속도에 따라 적용할 Gradient

---

## 🔹 Size over Lifetime

### ▪️ Size
- 입자의 수명에 따라 크기 변화 설정

### ▪️ Separate Axes
- 축별 크기를 따로 설정할 수 있음

---

## 🔹 Size by Speed

### ▪️ Speed Range
- 속도 범위에 따라 크기 적용 여부 결정

### ▪️ Size
- 속도에 따라 적용할 크기 값 (Curve)

---

## 🔹 Rotation over Lifetime

### ▪️ Rotation
- 입자의 회전 속도를 수명에 따라 설정

### ▪️ Separate Axes
- 각 축별 회전 가능

---

## 🔹 Rotation by Speed

### ▪️ Speed Range
- 속도에 따라 회전 적용 여부 결정

### ▪️ Rotation
- 속도에 따라 회전 속도 설정

---

## 🔹 External Forces

### ▪️ Multiplier
- Wind Zone 등의 외부 힘에 대한 반응 정도

---

## 🔹 Noise

### ▪️ Separate Axes
- 각 축별 Strength 설정 여부

### ▪️ Strength
- 흔들림 강도

### ▪️ Frequency
- 흔들림 속도

### ▪️ Scroll Speed
- 노이즈 패턴이 이동하는 속도

### ▪️ Damping
- 흔들림이 점점 약해지는 효과

### ▪️ Octaves
- 레이어 수 (복잡도 조절)

### ▪️ Octave Multiplier
- 옥타브별 강도 비율

### ▪️ Octave Scale
- 옥타브별 스케일 비율

### ▪️ Quality
- 연산 정밀도

### ▪️ Remap
- 출력값 범위 재조정

### ▪️ Remap Curve
- Remap 범위를 시간에 따라 조정

### ▪️ Position/Rotation/Size Amount
- 흔들림을 적용할 속성

---

## 🔹 Collision

### ▪️ Type
- 3D 또는 2D 충돌 선택

### ▪️ Collides With
- 충돌 대상 레이어

### ▪️ Send Collision Messages
- OnParticleCollision 이벤트 호출 여부

### ▪️ Dampen
- 충돌 후 속도 감소율

### ▪️ Bounce
- 튕겨나가는 정도

### ▪️ Lifetime Loss
- 충돌 시 수명 감소량

### ▪️ Min/Max Kill Speed
- 속도에 따라 제거 여부 설정

### ▪️ Radius Scale
- 충돌 반경 조절

### ▪️ Visualize Bounds
- 충돌 영역을 Scene 뷰에서 시각화

### ▪️ Quality / Voxel Size
- 충돌 해상도 관련 성능 설정

---

## 🔹 Triggers

- 트리거 Collider와 상호작용
- Inside / Outside / Enter / Exit 이벤트 정의 가능

---

## 🔹 Sub Emitters

- 입자 생성 시, 충돌 시, 소멸 시 다른 파티클을 추가 생성 가능
- 설정 항목: Emitter, Inherit Properties

---

## 🔹 Texture Sheet Animation

### ▪️ Tiles X, Y
- 스프라이트 시트 분할 수

### ▪️ Frame over Time
- 애니메이션 재생 속도 커브

### ▪️ Start Frame / Cycle Count / Row Mode
- 시작 프레임, 반복 횟수, Row 사용 방식 등

---

## 🔹 Lights

- 입자에 조명 효과 추가
- Ratio, Light Prefab, 범위, 밝기 등 설정

---

## 🔹 Trails

- 입자 잔상 궤적 효과 생성
- 모드, Lifetime, Width, Color, Texture Mode 등

---

## 🔹 Custom Data

- 입자에 사용자 정의 데이터를 추가하여 셰이더와 연동
- Custom1/2 Vector, Color, Curve 등 설정 가능

---

## 🔹 Renderer

- 입자 렌더링 방식 설정
- Render Mode, Material, Sorting, Mesh, Pivot 등
