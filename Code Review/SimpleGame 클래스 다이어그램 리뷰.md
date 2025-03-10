# 🎮 Unity 공부 기록 (2025-03-10)

# 현재 프로젝트 다이어그램
![image](https://github.com/user-attachments/assets/ccb57b9a-48e9-4b2a-909b-787513b38f75)

## 1. 구조적으로 괜찮은 점
### ✅ 싱글톤 패턴 활용
- Singleton<T>을 기반으로 GameManager, SceneLoadManager 등을 싱글톤으로 관리하는 점이 보임.
- 전체적인 게임 관리 로직이 중앙 집중식으로 통제될 가능성이 높아 보임.

### ✅ 객체 풀링 적용
- PoolObject를 기반으로 Bullet, Unit 같은 클래스가 객체 풀링을 활용하는 것 같음.
- 메모리 사용 최적화를 고려한 점이 좋음.

### ✅ 데이터 관리 클래스 분리
- DataManager, PlayerDataManager, BossDataManager 같은 데이터 관리 클래스가 따로 존재.
- 게임 데이터를 구조적으로 관리하려는 의도가 보임.

### ✅ UI, 사운드, 스코어 등의 모듈화
- SoundManager, UIManager, ScoreUpdater 등이 개별적으로 존재하여 관심사를 분리한 점이 좋음.
- 유지보수성이 좋을 가능성이 높음.

## 2. 개선할 수 있는 점
### ⚠️ GameManager가 과도하게 중심적일 가능성
- GameManager가 Singleton<GameManager>로 되어 있고, 다양한 매니저들과 연결될 가능성이 높아 보임.
- 만약 GameManager가 지나치게 많은 역할을 한다면, 특정 기능을 별도 클래스로 분리하여 SRP(단일 책임 원칙)를 강화할 필요가 있음.
### ⚠️ UI 클래스들의 구조적 관계가 불명확
- DictionaryUI, HealthStatusUI, PlayerStatusUI, SellingUI, SettingPopup 등 여러 UI 관련 클래스가 있음.
- UI끼리의 관계(상속/구성 관계)가 명확하지 않아 보이는데, Base UI 클래스를 두고 공통 기능을 묶는 방식이 필요할 수도 있음.
### ⚠️ 데이터 클래스와 구조체의 역할 정리 필요
- Data, Layer, Mixer, Parameters, ResourcesPath, Tag 같은 구조체가 있음.
- 데이터 클래스로 관리하는 것이 더 적절한지, 구조체를 유지하는 것이 더 나은지 판단해야 함.
### ⚠️ 인터페이스 사용 빈도 낮음
IShowAndHide, IUIElement 두 개의 인터페이스만 존재.
만약 다른 클래스들이 특정 동작을 공통적으로 수행해야 한다면, 인터페이스를 적극적으로 활용하는 것이 코드 유지보수성을 높이는 데 좋을 수 있음.

## 3. 추천 개선 방향
### 🛠 GameManager의 역할 최소화
- 현재 구조에서 GameManager가 여러 매니저들과 직접 연결될 가능성이 큼.
- 게임의 핵심 루프를 돌리는 최소한의 기능만 남기고, 나머지는 개별 매니저로 위임하는 것이 좋음.
### 🛠 UI 베이스 클래스 추가
- BaseUI : MonoBehaviour 같은 기본 UI 클래스를 만들어 DictionaryUI, PlayerStatusUI 등이 이를 상속받게 하면 중복 코드 줄일 수 있음.
### 🛠 객체 풀링이 필요한 다른 클래스 추가
- 현재 Bullet, Unit만 PoolObject를 상속받고 있음.
- 만약 더 많은 동적 객체가 있다면, PoolObject를 상속받아 메모리 최적화를 고려해야 함.
### 🛠 인터페이스 활용 확대
- UI 요소들이 IShowAndHide 같은 인터페이스를 구현하도록 하면 유연성이 증가할 것 같음.

# 결론
전체적인 구조는 잘 설계되어 있음.
다만, GameManager가 너무 많은 역할을 하지 않도록 조정하고, UI 및 데이터 구조를 좀 더 정리하면 좋을 것 같음.
객체 풀링과 인터페이스 활용을 더욱 강화하면 확장성과 유지보수성이 향상될 것 같음.
