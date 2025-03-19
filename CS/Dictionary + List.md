# 🎮 Unity 공부 기록 (2025-03-19)

## 1. 오늘의 학습 목표 🎯
- [ ] Dictionary와 List를 병행하여 사용해보기
- [ ] Dictionary<Tkey, TValue>와 List<T>를 함께 활용하여 기능 구현
- [ ] 자동차 ID(enum)를 기반으로 빠른 검색 및 정렬된 리스트 생성
- [ ] Unity UI에서 자동차를 순서대로 표시할 수 있도록 데이터 구조 최적화

---

## 2. 학습한 개념 📝
### 🔹 개념 1: Dictionary와 List를 함께 사용하는 이유  

Unity에서 특정 오브젝트(캐릭터, 아이템, 퀘스트 등)를 ID 기반으로 빠르게 검색하면서도, UI에서 순서대로 정렬하여 표시해야 하는 경우, Dictionary와 List를 함께 활용하는 것이 효과적이다.
그 이유는 다음과 같다.

✅ Dictionary<Tkey, TValue>의 역할
- Tkey를 기반으로 빠르게 검색 할 수 있음 (O(1) 속도).
- 키 값(Tkey)을 사용해 특정 자동차를 쉽게 찾을 수 있음.

✅ List<T>의 역할
- T를 순서대로로 정렬하여 UI에서 순차적으로 표시 가능.
- 리스트를 활용하면 이전/다음 자동차를 쉽게 선택할 수 있음.

따라서 딕셔너리는 빠른 검색, 리스트는 순차적 정렬 및 UI표시용으로 사용한다. 

### 여기서 의문
-> `SortedDictionary` 정렬도 있는데 왜 이 방법을 사용하는가?

### 1. 🚀 SortedDictionary<Tkey, TValue> 대신 Dictionary + List를 쓰는 이유
- SortedDictionary는 자동 정렬되지만, 키 값(Tkey) 기준으로만 정렬됨.
- 하지만 우리가 필요한 건 다양한 기준(속도, 가격 등)으로 정렬할 유연성!
- List<T>를 사용하면 ID뿐만 아니라 다른 속성(예: 성능, 등급) 기준으로도 정렬 가능.

### 2. ⚡Dictionary<Tkey, TValue>와 List<T>를 함께 사용할 때 OrderBy()가 필요한 이유
- 새로운 데이터가 들어올 때, 리스트에 자동으로 정렬되지 않음.
- 따라서 ID 순서대로 리스트를 유지하려면 OrderBy()를 한 번 수행해야 함.
- `OrderBy()`를 적용하면, 새로운 데이터가 추가되어도 리스트가 항상 정렬된 상태를 유지함.
- 정렬된 리스트를 유지하면 UI에서 "다음", "이전" 버튼을 통한 탐색이 쉬워짐.

### 3. 

📌 결론  
빠른 검색이 필요하면 Dictionary, 정렬이 필요하면 List + OrderBy() 조합이 가장 효율적이다!

### 🔹 개념 2:Enum을 활용한  ID 관리
Enum을 사용하면 ID를 일관되게 관리할 수 있음.  
ID를 int 또는 string 대신 enum으로 정의하면 코드 가독성이 좋아지고, 실수를 줄일 수 있다.

```csharp
// 코드 예제
public enum CarID
{
    SportCar,  // 0
    SUV,       // 1
    Truck,     // 2
    Sedan      // 3
}

```
🚗 장점
- `CarID.SUV`처럼 의미 있는 이름을 사용할 수 있어 가독성이 높음.
- 정수형 값으로 자동 매핑되므로 별도의 ID 관리가 필요 없음.
---

## 3. C# 코드 예제 💻
```csharp
using System;
using System.Collections.Generic;
using UnityEngine;

// 자동차 ID를 Enum으로 정의
public enum CarID
{
    SportCar,
    SUV,
    Truck,
    Sedan
}

// MonoBehaviour를 상속받지 않는 일반 클래스 (생성자 없이 초기화 메서드 사용)
public class Car
{
    public CarID id;
    public string name;
    public GameObject model; // 자동차 모델 (Prefab)

    public void Initialize(CarID id, string name, GameObject model = null)
    {
        this.id = id;
        this.name = name;
        this.model = model;
    }
}

// 자동차 선택 및 관리 클래스
public class CarSelection : MonoBehaviour
{
    private Dictionary<CarID, Car> carDict = new Dictionary<CarID, Car>(); // ID 기반 검색
    private List<Car> sortedCarList = new List<Car>(); // 정렬된 자동차 리스트
    private int currentIndex = 0; // 현재 선택된 자동차 인덱스

    void Start()
    {
        LoadCars();
        DisplayCurrentCar();
    }

    // 자동차 데이터를 자동으로 로드하는 함수 (CSVReader에서 데이터 가져옴)
    private void LoadCars()
    {
        List<(CarID, string)> carData = CSVReader.LoadCarData("Assets/Data/cars.csv"); // CSV 파일 로드

        foreach (var (id, name) in carData)
        {
            GameObject carObject = new GameObject(name); // GameObject 생성
            Car car = carObject.AddComponent<Car>(); // Car 컴포넌트 추가
            car.Initialize(id, name); // Initialize()로 값 설정

            carDict[id] = car; // Dictionary에 저장
            sortedCarList = sortedCarList.OrderBy(car => car.id).ToList();
        }
    }

    // 현재 선택된 자동차 표시
    private void DisplayCurrentCar()
    {
        if (sortedCarList.Count == 0) return;

        Car currentCar = sortedCarList[currentIndex];
        Debug.Log($"현재 선택된 자동차: {currentCar.name}");
    }

    // 다음 자동차 선택
    public void SelectNextCar()
    {
        if (sortedCarList.Count == 0) return;

        currentIndex = (currentIndex + 1) % sortedCarList.Count;
        DisplayCurrentCar();
    }

    // 이전 자동차 선택
    public void SelectPreviousCar()
    {
        if (sortedCarList.Count == 0) return;

        currentIndex = (currentIndex - 1 + sortedCarList.Count) % sortedCarList.Count;
        DisplayCurrentCar();
    }

    // 특정 ID의 자동차를 선택하는 함수
    public void SelectCarByID(CarID id)
    {
        if (carDict.ContainsKey(id))
        {
            currentIndex = sortedCarList.FindIndex(car => car.id == id);
            DisplayCurrentCar();
        }
        else
        {
            Debug.LogWarning("해당 ID의 자동차가 없습니다.");
        }
    }

    public void AddNewCar(CarID id, string name)
    {
        if (carDict.ContainsKey(id))
        {
            Debug.LogWarning("이미 존재하는 자동차 ID입니다.");
            return;
        }

        GameObject carObject = new GameObject(name);
        Car car = carObject.AddComponent<Car>();
        car.Initialize(id, name);

        carDict[id] = car;
        sortedCarList.Add(car);

        // 🚀 새로운 자동차 추가 후 다시 정렬
        sortedCarList = sortedCarList.OrderBy(c => c.id).ToList();
    }

}

```
