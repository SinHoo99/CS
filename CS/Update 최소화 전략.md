# 🎮 Unity 공부 기록 (2025-04-03)

## ✅ 주제: Update() 최소화 전략 - 성능과 구조를 동시에 잡는 방법
---

## 🔸 왜 Update()를 최소화해야 할까?
### 🔹 개념 1: ✅ Update()는 매 프레임마다 호출됨
- 60fps → 매 초당 60번
- 100개의 오브젝트가 각각 Update() 돌면? → 6000번/초 호출됨
- 이는 CPU 부하, GC, 캐시 미스 등의 원인이 될 수 있음
### 🔹 개념 2: ✅ 불필요한 연산도 실행됨
- 조건이 항상 false인데도 연산 시도
- 쓸데없는 계산, GetComponent, Find, GameObject.CompareTag 반복 등
### 🔹 개념 3: ✅ 구조 복잡성 증가
- Update()를 여기저기 흩뿌리면 전체 흐름이 숨겨지고 디버깅 어려워짐
---

## 전략 1: 이벤트 기반 처리로 대체
❓ 예시 상황
- 플레이어가 땅에 닿았는지 매 프레임 확인?
```csharp
void Update()
{
    if (isGrounded) Jump();
}
```
✅ 해결: 이벤트 트리거 방식으로 전환
```csharp
void OnCollisionEnter2D(Collision2D col)
{
    if (col.gameObject.CompareTag("Ground"))
        Jump();
}
```
🎯 장점
- 연산은 "필요한 순간에만" 발생 → 매우 효율적
- 논리 흐름이 명확해짐 → 유지보수 용이
---
## 전략 2: 코루틴(Coroutine)을 사용한 시간 기반 처리
❓ 예시 상황
- 일정 시간마다 장애물 생성
```csharp
void Update()
{
    timer += Time.deltaTime;
    if (timer > 2f) SpawnObstacle();
}

```
✅ 해결: IEnumerator 기반 반복 처리
```csharp
IEnumerator SpawnRoutine()
{
    while (true)
    {
        SpawnObstacle();
        yield return new WaitForSeconds(2f);
    }
}
```
🎯 장점
- 시간 컨트롤 명확, 연산 횟수 제한
- yield return은 다음 프레임까지 쉬므로 CPU 부하도 적음
---
## 전략 3: FixedUpdate()를 활용한 일정 간격 처리
✅ FixedUpdate는 일정 시간 간격 (기본 0.02s)마다 호출됨

→ 매 프레임마다 하지 않아도 되는 연산에 적합

예: 카메라 자동 스크롤
```csharp
void FixedUpdate()
{
    transform.position += Vector3.up * scrollSpeed * Time.fixedDeltaTime;
}

```
🎯 장점
- 부드러운 움직임 보장
- 물리 연산과 동기화됨 → Rigidbody2D 등에 안정적
---
## 전략 4: LateUpdate() 또는 Update()에서 조건 분기
- 꼭 Update() 써야 한다면, 조건 분기와 상태 FSM을 넣어라
```csharp
void Update()
{
    if (state == GameState.Playing)
        HandlePlayerInput();
}

```
→ 모든 상황에 대해서 Update가 실행되는 게 아니라, 상태 조건을 걸어서 실제 호출은 최소화
---


## ✅ 언제 Update()를 써도 괜찮을까?

| 조건                           | 사용 가능?       |
|--------------------------------|------------------|
| 플레이어 입력 처리 (1개)       | ✅               |
| 카메라 따라다니는 1개 오브젝트 | ✅               |
| 수백 개 오브젝트의 상태 반복 검사 | ❌             |
| GUI 갱신 / FPS 표시 등         | ❌ 별도 처리 권장 |

---

## ✅ 총정리: Update() 최소화를 위한 전략 요약

| 전략                      | 핵심 개념             | 추천 상황                    |
|---------------------------|------------------------|------------------------------|
| 이벤트 기반               | 필요한 순간만 처리     | 충돌, 버튼 클릭, 상태 전이   |
| 코루틴 (Coroutine)        | 반복 타이머, 리듬 처리 | 타이밍 제어, 생성 주기       |
| FixedUpdate               | 일정 간격 실행         | 카메라, 물리, 스크롤         |
| OnBecameInvisible         | 자동 감지              | 화면 밖 오브젝트 반환        |
| 상태 머신 + 조건 분기     | 최소 호출              | if (isActive) {} 처럼 제어   |
| 비활성화 처리             | Update 자체 차단       | 불필요한 오브젝트 비활성화   |

