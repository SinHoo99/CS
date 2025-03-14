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

---

# 현재 GameManager의 결합도
## 여러 개의 Manager를 직접 참조
### 1. GameManager가 UIManager, SoundManager, PoolManager, DataManager 등 너무 많은 클래스에 직접 의존하고 있음.  
- GameManager가 이 모든 객체를 관리하기 때문에 책임이 많아지고, 유지보수가 어려워짐.
### 2. GetComponentInChildren<T>() 사용
- Awake()에서 여러 개의 GetComponentInChildren<T>()을 사용하여 GameManager 내부에서 직접 참조를 초기화하고 있음.  
- 만약 Manager 클래스가 추가되거나 제거되면 GameManager의 코드를 수정해야 하는 구조이므로 유연성이 떨어짐.

```csharp
using TMPro;
using UnityEngine;
using UnityEngine.Audio;
using UnityEngine.UI;

public class GameManager : Singleton<GameManager>
{
    #region  스크립트 참조
    [Header("Managers")]
    [SerializeField] private UIManager _uiManager;
    [SerializeField] private PlayerStatusUI _playerStatusUI;
    [SerializeField] private SpawnManager _spawnManager;
    [SerializeField] private SoundManager _soundManager;
    [SerializeField] private PoolManager _poolManager;
    [SerializeField] private AlertManager _alertManager;
    [SerializeField] private ObjectPool _objectPool;
    [SerializeField] private PlayerDataManager _playerDataManager;
    [SerializeField] private BossDataManager _bossDataManager;
    [SerializeField] private DataManager _dataManager;
    [SerializeField] private SaveManager _saveManager;
    [SerializeField] private ScoreUpdater _scoreUpdater;

    [Header("Game Objects")]
    [SerializeField] private PoolObject _bulletPrefabs;


    #endregion
    private PrefabDataManager _prefabDataManager;
    public bool isQuitting = false;

    #region  Public Properties (읽기 전용)
    public UIManager UIManager => _uiManager;
    public PlayerStatusUI PlayerStatusUI => _playerStatusUI;
    public SpawnManager SpawnManager => _spawnManager;
    public PlayerDataManager PlayerDataManager => _playerDataManager;
    public ObjectPool ObjectPool => _objectPool;
    public DataManager DataManager => _dataManager;
    public SaveManager SaveManager => _saveManager;
    public PoolManager PoolManager => _poolManager;
    public BossDataManager BossDataManager => _bossDataManager;
    public SoundManager SoundManager => _soundManager;
    public AlertManager AlertManager => _alertManager;
    public ScoreUpdater ScoreUpdater => _scoreUpdater;
    #endregion
    protected override void Awake()
    {
        if (IsDuplicates()) return;
        base.Awake();
        Application.targetFrameRate = 60;
        InitializeComponents();
    }

    private void Start()
    {
        InitializeGame();
    }

    #region  게임 초기화 로직
    private void InitializeComponents()
    {
        // 1. 데이터 관련 초기화
        _dataManager = GetComponentInChildren<DataManager>();
        _saveManager = GetComponentInChildren<SaveManager>();
        _playerDataManager = GetComponentInChildren<PlayerDataManager>();
        _bossDataManager = GetComponentInChildren<BossDataManager>();
        _soundManager = GetComponentInChildren<SoundManager>();

        // 2. 오브젝트 풀링 관련 초기화
        _objectPool = GetComponentInChildren<ObjectPool>();
        _poolManager = GetComponentInChildren<PoolManager>();

        // 3. UI 관련 초기화
        _uiManager = GetComponentInChildren<UIManager>();

        // 4. 기타 매니저 초기화
        _alertManager = GetComponentInChildren<AlertManager>();
        _spawnManager = GetComponentInChildren<SpawnManager>();


        // 5. 데이터 초기화
        _dataManager.Initialize();
        _soundManager.Initialize();
    }

    private void InitializeGame()
    {
        _poolManager.AddObjectPool();
        _playerDataManager.Initialize();
        _prefabDataManager = new PrefabDataManager();
        _uiManager.InventoryManager.TriggerInventoryUpdate();
        _soundManager.SettingPopup.Initializer();
    }
    #endregion

    #region  애플리케이션 이벤트
    private void OnApplicationQuit()
    {
        isQuitting = true;

        // UI 비활성화 (오류 방지)
        if (_soundManager?.SettingPopup != null)
        {
            _soundManager.SettingPopup.gameObject.SetActive(false);
        }

        // 데이터 저장
        _playerDataManager.SavePlayerData();
        _prefabDataManager.SavePrefabData();
        _bossDataManager.SaveBossRuntimeData();
        _soundManager.SaveOptionData();
    }

    private void OnApplicationPause(bool pause)
    {
        if (pause && !isQuitting)
        {
            _playerDataManager.SavePlayerData();
            _bossDataManager.SaveBossRuntimeData();
            _soundManager.SaveOptionData();
        }
    }
    #endregion

    #region  게임 데이터 참조
    private static readonly CollectedFruitData EmptyCollectedFruitData = new CollectedFruitData { ID = FruitsID.None, Amount = 0 };

    public FruitsData GetFruitsData(FruitsID id)
    {
        return _dataManager.FruitDatas[id];
    }

    public int GetFruitAmount(FruitsID id)
    {
        return _playerDataManager.NowPlayerData.Inventory.TryGetValue(id, out var collectedData) ? collectedData.Amount : 0;
    }

    public CollectedFruitData GetCollectedFruitData(FruitsID id)
    {
        return _playerDataManager.NowPlayerData.Inventory.TryGetValue(id, out var collectedData) ? collectedData : EmptyCollectedFruitData;
    }

    public BossData GetBossData(BossID id)
    {
        return _dataManager.BossDatas.TryGetValue(id, out BossData bossData) ? bossData : null;
    }
    public int GetBossReward(BossID id)
    {
        return _dataManager.BossDatas.TryGetValue(id, out BossData bossData) ? bossData.Reward : 0;
    }

    public PoolObject GetBullet()
    {
        return _bulletPrefabs;
    }
    #endregion

    #region  사운드 관련 메서드
    public AudioMixer GetAudioMixer()
    {
        return _soundManager.AudioMixer;
    }

    public void PlayBGM(BGM target)
    {
        _soundManager.PlayBGM(target);
    }

    public void PlaySFX(SFX target)
    {
        _soundManager.PlaySFX(target);
    }
    #endregion
}

```
# 🔧 결합도를 낮춘 개선된 GameManager 구조  
## 불필요한 참조 제거 + 의존성 주입(DI) 적용  
  
### 📌 1️⃣ GameManager에서 직접적인 참조 제거  

```csharp
using UnityEngine;
using Zenject;

public class GameManager : Singleton<GameManager>
{
    [Inject] private UIManager _uiManager;
    [Inject] private SoundManager _soundManager;
    [Inject] private SpawnManager _spawnManager;
    [Inject] private PoolManager _poolManager;
    [Inject] private DataManager _dataManager;

    public bool isQuitting = false;

    protected override void Awake()
    {
        if (IsDuplicates()) return;
        base.Awake();
        Application.targetFrameRate = 60;
    }

    private void Start()
    {
        InitializeGame();
    }

    private void InitializeGame()
    {
        _poolManager.AddObjectPool();
        _dataManager.Initialize();
        _soundManager.Initialize();
        _uiManager.InventoryManager.TriggerInventoryUpdate();
        _soundManager.SettingPopup.Initializer();
    }
}

```

### 📌 2️⃣ Zenject에서 의존성 주입을 위한 Installer 추가  
```csharp
using UnityEngine;
using Zenject;

public class GameInstaller : MonoInstaller
{
    public override void InstallBindings()
    {
        Container.Bind<UIManager>().FromComponentInHierarchy().AsSingle();
        Container.Bind<SoundManager>().FromComponentInHierarchy().AsSingle();
        Container.Bind<SpawnManager>().FromComponentInHierarchy().AsSingle();
        Container.Bind<PoolManager>().FromComponentInHierarchy().AsSingle();
        Container.Bind<DataManager>().FromComponentInHierarchy().AsSingle();
    }
}

```
