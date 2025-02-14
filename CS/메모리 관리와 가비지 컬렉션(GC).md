# 🎮 Unity 공부 기록 (2025-02-13)

## 1. 오늘의 학습 목표 🎯
- 메모리 관리와 가비지 컬렉션(GC) 최적화 상세 개념 배우기
---

## 2. 학습한 개념 📝
### 🔹 개념 1: (가비지 컬렉션(GC)란?)
* 가비지 컬렉션(Garbage Collection, GC)은 프로그램 실행 중 더 이상 참조되지 않는 객체를 자동으로 정리하여 메모리 누수를 방지하는 기술.
* C#에서는 자동 메모리 관리 시스템을 사용하여 개발자가 직접 메모리를 해제하지 않아도 되지만, GC가 작동할 때 성능 저하(프레임 드랍, GC스파이크)가 발생 할 수 있음.

### 🔹 개념 2: (Unity에서 GC의 동작 방식)
* Unity는 .NET의 ```Mark-and-Sweep``` 방식의 GC를 사용함
  1. Mark 단계 : 사용 중인 객체를 찾고 표시함.
  2. Sweep 단계 : 사용되지 않는 객체를 정리함.
* GC가 실행될 때 전체 프로그램이 잠시 멈추는 ```Stop-the-world``` 이벤트가 발생할 수 있으며, 이는 실시간 성능이 중요한 게임에서 큰 영향을 미침.

### 🔹 개념 3: (GC 최적화 전략)
1) 동적 할당 최소화
   * new 키워드를 사용하여 객체를 자주 생성하면 힙 메모리를 계속 사용하게 되며, 결국 GC를 자주 호출하게 됨.
   * 이를 줄이기 위해 재사용 가능한 객체(Object Pooling)를 활용해야함
```csharp
// (설명) 코드 예제
using UnityEngine;

public class ObjectPool : MonoBehaviour
{
    public GameObject prefab;
    private Queue<GameObject> pool = new Queue<GameObject>();

    public GameObject GetObject()
    {
        if(pool.Count > 0)
        {
            GameObjcet obj = pool.Dequeue();
            obj.SetActive(true);
            return obj;
        }
        return Instantiate(prefab);
    }
    public void ReturnObject(GameObject obj)
    {
        obj.SetActive(false);
        pool.Enqueue(obj);
    }
}
```
✅ Object Pooling 기법을 사용하면 객체 생성과 소멸을 줄여 GC 호춯을 줄일 수 있음.

2) Boxing & Unboxing 최소화
   * Boxing과 Unboxing은 값 타입 (```int```, ```float```등)을 참조 타입(```object```)으로 변환할 때 발생하며, 불필요한 메모리 할당을 유발함.

❌ 잘못된 예제:
```
object obj = 10; // Boxing 발생
int num = (int)obj; // Unboxing 발생
```
✅ 올바른 예제 (제네릭 사용):
```
void PrintValue<T>(T value)
{
    Debug.Log(value);
}
```
➡️ 제네릭(Generic) 사용을 통해 Boxing & Unboxing을 방지할 수 있음.

3) 문자열 연산 최적화
   * ```string```은 불변(Immutable) 객체 이므로, ```+```연산자를 사용할 때마다 새로운 메모리를 할당함.
❌ 잘못된 예제:
```
string message = "Hello " + "World";  // 문자열 결합 시 메모리 낭비 발생
```
✅ 올바른 예제 (제네릭 사용):
```
StringBuilder sb = new StringBuilder();
sb.Append("Hello ");
sb.Append("World");
string message = sb.ToString();
```
➡️ StringBuilder를 사용하면 GC 호출을 줄이고 성능을 최적화할 수 있음.

4)LINQ 사용 주의
* LINQ는 가독성이 좋지만, 내부적으로 많은 메모리 할당을 유발할 수 있음.
❌ 잘못된 예제:
```
var evenNumbers = numbers.Where(n => n % == 0).ToList();
```
✅ 올바른 예제 (반복문 사용):
```
Lsit<int> evenNumbers = new List<int>();
foreach (var number in numbers)
{
    if (number % 2 == 0)
        evenNumbers.Add(number);
}
```
5) Native Collection사용 (Burst, Jobs 시스템)
   * Unity의 ```NAvtiveArray<T>```,```NAvtiveList<T>```를 홣용하면 Gc 영향을 줄이고 성능을 극대화 할 수 있습니다.
  
예제 : NativeArray 활용
```
using Unity.Collections;
using UnityEngine;

public class NativeArrayExample : MonoBehaviour
{
    void Start()
    {
        NativeArray<int> numbers = new NativeArray<int>(10, Allocator.Temp);
        
        for (int i = 0; i < numbers.Length; i++)
        {
            numbers[i] = i * 2;
        }

        numbers.Dispose(); // 메모리 해제
    }
}
```
➡️```NAvtiveArray```는 관리되는 힙이 아닌 네이티브 메모리에 할당되므로 GC영향을 받지 않습니다.

---

## 3. 결론 💻
Unity에서 GC 호출을 최소화하는 것은 게임 성능 최적화의 핵심.

✅객체를 재사용(```Object Pooling```)하고, 불필요한 동적 할당을 피하기

✅Boxing & Unboxing을 피하고, 문자열 연산 시 ```StringBuilder```를 활용

✅LINQ를 신중하게 사용하고, 필요한 경우 Native Collection을 고려

