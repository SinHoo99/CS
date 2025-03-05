# 🎮 Unity 공부 기록 (2025-03-05)

## 1. 오늘의 학습 목표 🎯
- [ ] Viewport 크기 조정 문제 해결
- [ ] Scroll View에서 Viewport가 크기 조정되지 않는 원인 분석
- [ ] KPT 방식으로 문제 해결 과정 정리

---

## 2. 학습한 개념 📝
### 🔹 개념 1: Viewport 크기 조정이 안 되는 문제 (Problem)
🔍 문제점 (Problem)  
- Viewport의 RectTransform이 비활성화되어 직접 크기를 조정할 수 없었음.
- ScrollRect 컴포넌트가 Viewport 크기를 자동으로 관리하고 있어서 Pos X, Pos Y, Width, Height 값이 잠겨 있었음.
- Stretch(늘리기) 설정을 유지하면서도 크기를 조정하고 싶었음.

### 🔹 개념 2: 문제 해결 과정 (Try & Solution)
✅ 해결 방법 (Try)    
- Scroll View 오브젝트를 선택하고 ScrollRect 컴포넌트를 확인함.
- Viewport 필드가 ScrollRect에 연결되어 있어서, 크기 조정이 불가능한 상태였음.
- ScrollRect에서 Viewport 연결을 해제한 후, RectTransform 크기를 조정함.
- 크기 조정 후, 다시 ScrollRect의 Viewport 필드에 Viewport 오브젝트를 드래그하여 연결함.
- 결과적으로 Stretch를 유지하면서도 Viewport 크기를 조정할 수 있었음.

🎯 핵심 포인트 (Keep)  
- Viewport의 크기가 조정되지 않을 때는 ScrollRect에서 Viewport 연결을 해제 후 수정 가능
- Stretch 설정을 유지하면서도 Offset 값을 조정하여 테두리 안쪽에 맞출 수 있음.
- RectMask2D를 활용하면 뷰포트 밖의 UI가 보이지 않도록 할 수 있음.

🛠 개선해야 할 점 (Problem & Try)  
- 앞으로 UI 레이아웃을 조정할 때, 자동으로 크기를 조절하는 컴포넌트가 있는지 먼저 확인하기
- Scroll View 사용 시, Viewport 크기가 자동 조정되는 점을 인지하고, 필요하면 직접 크기 조정하기

---
### 🚀 최종 정리
| K (Keep - 잘한 점) | P (Problem - 문제점) | T (Try - 해결 과정) |
|------------------|------------------|------------------|
| `ScrollRect`가 `Viewport` 크기를 자동으로 관리하는 걸 이해함 | `Viewport` 크기를 조정할 수 없었음 (`RectTransform` 잠김) | `ScrollRect`에서 `Viewport` 연결 해제 후 크기 조정, 다시 연결 |
| `Stretch` 유지하면서도 `Offset` 값으로 조정하는 방법 학습 | `Stretch` 해제를 하면 해상도에 따라 UI가 깨질 우려 | `Stretch` 유지한 채 `Offset` 값 조정하여 해결 |
| `RectMask2D`로 뷰포트 밖 UI를 가리는 방법 활용 | UI가 테두리를 넘어서 보이는 문제 발생 가능 | `RectMask2D`를 추가하여 해결 |

