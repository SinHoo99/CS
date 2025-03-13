# 🎮 Unity 공부 기록 (2025-03-13)

 Unity에서 Zenject 적용 예제: 간단한 게임 시스템

Zenject를 활용하여 플레이어가 점수를 얻고, 게임 매니저가 이를 관리하는 구조  

📌 1️⃣ 프로젝트 구조  
Zenject를 적용하기 위해, DI를 활용한 구조를 만들어 보자.  

📂 Scripts/  
├── GameInstaller.cs ✅ (의존성 바인딩)  
├── GameManager.cs ✅ (게임 상태 관리)  
├── Player.cs ✅ (플레이어 점수 증가)  
├── ScoreService.cs ✅ (점수 관리)  

---
📌 2️⃣ GameInstaller (Zenject 초기화)  
먼저 `Zenject`의 바인딩을 담당하는 `GameInstaller`를 만든다.  
```csharp
using Zenject;
using UnityEngine;

public class GameInstaller : MonoInstaller
{
    public override void InstallBindings()
    {
        // GameManager와 ScoreService를 Singleton으로 등록
        Container.Bind<GameManager>().AsSingle();
        Container.Bind<ScoreService>().AsSingle();
        
        // Player는 씬에 있는 인스턴스를 사용
        Container.Bind<Player>().FromComponentInHierarchy().AsSingle();
    }
}

```
✅ GameManager, ScoreService는 싱글톤으로 등록하고,  
✅ Player는 씬에서 찾아서 바인딩한다.  

---
📌 3️⃣ GameManager (게임 관리)  
게임 전체 상태를 관리하는 GameManager를 만든다.  
```csharp
using Zenject;
using UnityEngine;

public class GameManager : IInitializable
{
    private readonly ScoreService _scoreService;

    [Inject]
    public GameManager(ScoreService scoreService)
    {
        _scoreService = scoreService;
    }

    public void Initialize()
    {
        Debug.Log("GameManager 초기화 완료!");
    }

    public void GameOver()
    {
        Debug.Log($"게임 종료! 최종 점수: {_scoreService.Score}");
    }
}
```
✅ IInitializable을 구현하면 Zenject가 자동으로 Initialize()를 실행해 준다.  

---
📌 4️⃣ ScoreService (점수 관리)  
점수를 관리하는 ScoreService를 만든다.  
```csharp
using UnityEngine;

public class ScoreService
{
    private int _score;

    public int Score => _score;

    public void AddScore(int value)
    {
        _score += value;
        Debug.Log($"점수 증가! 현재 점수: {_score}");
    }
}

```
✅ GameManager가 점수를 가져오고 관리할 수 있도록 ScoreService를 따로 관리한다.

---
📌 5️⃣ Player (플레이어 점수 증가)  
플레이어가 점수를 증가시킬 수 있도록 Player 스크립트를 작성한다.  
```csharp
using Zenject;
using UnityEngine;

public class Player : MonoBehaviour
{
    private ScoreService _scoreService;

    [Inject]
    public void Construct(ScoreService scoreService)
    {
        _scoreService = scoreService;
    }

    private void Update()
    {
        if (Input.GetKeyDown(KeyCode.Space))
        {
            _scoreService.AddScore(10);
        }
    }
}

```
✅ Construct() 메서드를 통해 ScoreService를 주입받는다.  
✅ 스페이스바를 누르면 점수가 증가한다.  
