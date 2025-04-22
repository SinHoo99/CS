# 🎮 Unity 공부 기록 (2025-04-22)

## 1. 오늘의 학습 목표 🎯
- [ ] ScriptableObject의 기본 개념 정리
- [ ] ScriptableObject 관련 오류 원인 분석 및 해결
- [ ] 현업에서의 SO 관리 전략 정리

---

## 2. 학습한 개념 📝
### 🔹 개념 1: ScriptableObject란?
ScriptableObject는 Unity에서 데이터를 저장하기 위해 사용하는 클래스이다.
- 역할: 인스펙터에서 직접 편집 가능한 데이터 컨테이너
- 저장 방식: .asset 파일 형태로 프로젝트에 저장됨
- 장점:
    - 메모리에 상주하지 않아 경량
    - 여러 오브젝트 간 데이터 공유에 효과적
    - 코드와 데이터 분리 가능 (SRP 구조에 적합)

활용 예시
- 플레이어 설정 정보 (이동속도, 점프력 등)
- 환경 색상 정의 (TimeColorData 등)
- 적 AI 세팅 값, 난이도 곡선 등
---
### 🔹 개념 2: SO 관련 오류 분석 및 해결
오류 메시지
"No script asset for TimeColorData. Check that the definition is in a file of the same name and that it compiles properly."

원인 분석
| 항목                    | 설명                                                             |
|-------------------------|------------------------------------------------------------------|
| `fileName = "TimeColorData"` | Unity가 해당 이름과 같은 `.cs` 파일을 찾으려 함                      |
| 클래스 실제 위치         | 예: `ScriptableObjects.cs` 안에 정의됨                              |
| Unity 연결 실패         | 클래스명과 파일명이 달라 연결되지 않음                              |

해결 방법
- fileName 속성 제거
- 클래스명을 파일명과 일치시킴 (ex. TimeColorData.cs)
- 기존 .asset 삭제 → 새로 생성
- 필요 시 Library 폴더 삭제로 강제 리빌드

관련 자주 발생 문제
| 문제 유형              | 원인                                | 해결 방법                            |
|------------------------|-------------------------------------|---------------------------------------|
| Script 연결 끊김       | `fileName` 충돌, 파일명 불일치        | `fileName` 제거 또는 클래스 파일 분리 |
| .asset 생성됨, 작동 안함 | 필드 미설정 또는 `OnEnable` 미사용     | 수동 설정 또는 초기화 코드 추가       |
| 자주 연결 끊김         | `.meta` 파일 삭제 또는 스크립트 이동 | `.meta` 유지 또는 새로 연결           |

---
### 🔹 개념 3: 현업에서의 SO 관리 전략
| 전략         | 설명                                                                 |
|--------------|----------------------------------------------------------------------|
| 파일 분리     | SO마다 개별 파일로 관리 (예: `PlayerSO.cs`, `ColorSO.cs`)            |
| 묶음 관리     | 소규모일 경우 한 파일 안에 관리 가능. 단, `fileName`은 제거 필요       |
| 폴더 구조     | `Scripts/ScriptableObjects/`, `Data/` 등 정리된 구조 유지              |
| 리팩토링 시기 | SO가 4개 이상이면 분리하는 것이 유지보수에 유리                         |

팁 요약 
- CreateAssetMenu는 인스펙터에서 SO 생성 시 사용됨
- 클래스명과 파일명이 다르면 Unity 연결 불안정
- SO는 Scene과 무관하게 존재하므로 범용 데이터 관리에 유리
- SO 값은 런타임에서 변경되어도 저장되지 않음 → 저장하려면 PlayerPrefs, JSON 필요
---
## 3. C# 코드 예제 💻
```csharp
using UnityEngine;

[CreateAssetMenu(fileName = "TimeColorData", menuName = "Environment/Time Color Data")]
public class TimeColorData : ScriptableObject
{
    public Gradient dayColor;
    public Gradient nightColor;

    public Color GetColor(float timeOfDay)
    {
        return timeOfDay < 12f ? dayColor.Evaluate(timeOfDay / 12f) : nightColor.Evaluate((timeOfDay - 12f) / 12f);
    }
}
```
