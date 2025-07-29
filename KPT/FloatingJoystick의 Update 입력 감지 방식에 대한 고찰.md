#  FloatingJoystick의 Update 입력 감지 방식에 대한 고찰

##  질문
> **"조이스틱 입력을 매 프레임 `Update()`에서 비교하는 구조가 맞는 방법일까?**  
> **조건문을 걸었다고 해도 어차피 매 프레임 비교하는 건 동일한 거 아닌가?"**

---

##  결론 요약

| 질문 | 답변 |
|------|------|
| **Update에서 비교하는 게 괜찮은가?** |  Yes. Joystick은 실시간으로 입력이 미세하게 바뀌므로 `Update()` 기반 비교가 가장 실용적 |
| **매 프레임 비교는 낭비 아닌가?** |  연산 자체는 가볍지만, 조건문을 최소화하면 최적화 가능 |
| **완전한 이벤트 기반 방식은 가능한가?** |  Unity Joystick 구조상 어렵고 비효율적임 (실시간 감지가 불안정함) |

---

##  왜 `Update()`에서 감지하나?

###  이유 1. Unity 조이스틱 입력은 Continuous
- `Horizontal`, `Vertical` 값은 매 프레임 변할 수 있음
- `OnPointerMove`나 `OnDrag` 이벤트로는 미세한 입력 변화 감지가 어려움

###  이유 2. 키보드와는 다르게 "이동 중 변화"가 많음
- 손가락을 조금만 움직여도 값이 바뀜 (`0.1 → 0.11` 등)
- 이런 변화를 잡기 위해선 `Update()` 기반 비교가 안정적

---

##  성능 최적화 팁

```csharp
private void Update()
{
    Vector2 current = new Vector2(Horizontal, Vertical);

    // 최소 변화 임계값 설정 (떨림 방지)
    if ((current - _lastInput).sqrMagnitude > 0.0001f)
    {
        _lastInput = current;
        OnInputChanged?.Invoke(current);
    }
}
```
##  왜 sqrMagnitude?
- 벡터 연산 중 가장 가볍고 빠름
- 부동소수점 오차나 손가락 떨림 등을 무시하고 진짜 변화만 잡을 수 있음

##  이벤트 기반 방식이 힘든 이유

| 문제점 | 설명 |
|--------|------|
| `OnPointerMove`, `OnDrag` 없음 | `Joystick` 클래스는 기본적으로 이벤트 기반 지원이 부족함 |
| 미세 움직임 처리 불가 | Unity의 `EventSystem`으로는 `0.01`의 변화까지 감지하기 어려움 |
| 마우스/터치의 해석이 다름 | 모바일은 터치 유지 상태에서 변화가 느리기 때문에 이벤트로 잡기 어려움 |

##  결론
지금처럼 Update()에서 입력값 변화만 감지하는 구조는 가장 실용적이고 일반적인 방식이다.  
단, 조건 비교 최적화와 임계값 설정으로 불필요한 이벤트 호출을 줄이는 것이 중요하다.  


