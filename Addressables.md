# 🎮 Unity 공부 기록 (2025-02-14)

## 1. 오늘의 학습 목표 🎯
-  Unity Addressables의 기본 개념 이해하기
-  Addressables을 활용한 메모리 최적화 방법 학습
-  Addressables을 사용하여 동적 애셋 로딩 구현하기

---

## 2. 학습한 개념 📝
### 🔹 개념 1: (Addressables 개요)
Addressables(어드레서블)는 Unity에서 제공하는 메모리 관리 및 애셋 로딩 시스템으로,
기존의 Resources.Load()나 AssetBundle 방식보다 더 강력한 최적화 기능을 제공함.

🔹 Addressables의 주요 특징  
✔️ 메모리 최적화 → 사용하지 않는 애셋을 자동 해제 가능  
✔️ 비동기 로딩 → 게임의 로딩 속도 개선  
✔️ 원격 애셋 지원 → 클라우드에서 애셋 다운로드 가능  
✔️ 의존성 자동 관리 → 필요한 애셋만 로드하여 용량 절감  

---


### 🔹 개념 2: (제목)
설명 및 정리 내용을 작성하세요.

---

## 3. C# 코드 예제 💻
```csharp
// (설명) 코드 예제
using UnityEngine;

public class ExampleScript : MonoBehaviour
{
    void Start()
    {
        Debug.Log("Hello, Unity!");
    }
}
