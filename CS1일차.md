C# 및 Unity 개념 정리
1. Object Pool을 Queue로 사용하는 이유
🔹 FIFO(First-In-First-Out) 방식 유지
먼저 사용된 오브젝트가 먼저 반환됨.
오래된 오브젝트부터 재사용 가능 → 균등한 사용.
🔹 빠른 성능 (O(1) 연산)
Queue.Enqueue() / Queue.Dequeue()는 O(1) 연산.
List는 RemoveAt(0) 시 성능 저하 (O(n)).
🔹 GC(Garbage Collection) 최소화
기존 오브젝트를 재사용하여 메모리 낭비 방지.
csharp
복사
편집
private Queue<GameObject> pool = new Queue<GameObject>();

public GameObject GetObject()
{
    if (pool.Count > 0)
    {
        GameObject obj = pool.Dequeue();
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
2. 프로퍼티(Property)
🔹 기본 개념
클래스 내부의 필드(Field)에 대한 접근을 제어하는 기능.
get;으로 값을 읽고, set;으로 값을 설정 가능.
csharp
복사
편집
class Player
{
    private int _health;

    public int Health
    {
        get { return _health; }  // 값을 가져옴 (읽기)
        set { _health = value; } // 값을 설정 (쓰기)
    }
}
🔹 자동 구현 프로퍼티
csharp
복사
편집
public class Player
{
    public int Health { get; set; }  // 자동으로 필드를 생성하고 관리함
}
✅ 사용 예제
csharp
복사
편집
Player p = new Player();
p.Health = 50; // 자동으로 내부 필드에 저장됨
Console.WriteLine(p.Health); // 50 출력
🔹 읽기 전용 프로퍼티 (Read-Only Property)
csharp
복사
편집
public class Player
{
    private int _score = 100;  // 필드

    public int Score
    {
        get { return _score; }  // 값 읽기만 가능
    }
}
✅ 사용 예제
csharp
복사
편집
Player p = new Player();
Console.WriteLine(p.Score); // 100 출력
p.Score = 200; // ❌ 컴파일 오류 (set이 없기 때문)
🔹 쓰기 전용 프로퍼티 (Write-Only Property)
csharp
복사
편집
public class Player
{
    private int _secretCode; // 필드

    public int SecretCode
    {
        set { _secretCode = value; }  // 값 설정만 가능
    }
}
✅ 사용 예제
csharp
복사
편집
Player p = new Player();
p.SecretCode = 1234; // ✅ 설정 가능
Console.WriteLine(p.SecretCode); // ❌ 컴파일 오류 (읽기 불가)
🔹 private set을 활용한 프로퍼티
csharp
복사
편집
public class Player
{
    public int Level { get; private set; } = 1; // 읽기 가능, 하지만 설정은 내부에서만 가능

    public void LevelUp()
    {
        Level++; // ✅ 내부에서는 변경 가능
    }
}
✅ 사용 예제
csharp
복사
편집
Player p = new Player();
Console.WriteLine(p.Level); // 1 출력
p.Level = 5; // ❌ 컴파일 오류 (private set 때문)
p.LevelUp(); // ✅ 정상 동작
Console.WriteLine(p.Level); // 2 출력
🔹 계산된 프로퍼티 (Computed Property)
csharp
복사
편집
public class Player
{
    public int Score { get; set; }
    public int Bonus { get; set; }

    public int TotalScore => Score + Bonus; // 계산된 프로퍼티 (get만 존재)
}
✅ 사용 예제
csharp
복사
편집
Player p = new Player { Score = 90, Bonus = 10 };
Console.WriteLine(p.TotalScore); // 100 출력
3. 기존 코드 개선: 자동 구현 프로퍼티 적용
🔹 기존 코드 (불필요한 중복)
csharp
복사
편집
[SerializeField] private DataManager dataManager;
public DataManager DataManager => dataManager;
🔹 개선 코드 (자동 구현 프로퍼티)
csharp
복사
편집
[SerializeField] public DataManager DataManager { get; private set; } // ✅ 코드 간결화
필드 선언 없이 프로퍼티만 사용 → 코드가 더 깔끔해짐.
private set;을 사용해 외부에서는 읽기만 가능, 내부에서는 값 변경 가능.
4. CS0273 오류 해결 ("set 접근자의 액세스 가능성 한정자가 속성보다 제한적이어야 합니다.")
🔹 오류 발생 코드
csharp
복사
편집
[SerializeField] public DataManager DataManager { get; private set; } // ❌ 오류 발생
속성은 public인데, set이 private → C#에서 허용되지 않음.
🔹 해결 방법 1: 읽기 전용 속성으로 변경 (추천)
csharp
복사
편집
[SerializeField] public DataManager DataManager { get; } // ✅ 오류 해결
Inspector에서 값을 할당할 수 있고, 외부에서 변경 불가능.
🔹 해결 방법 2: 속성을 private으로 변경
csharp
복사
편집
[SerializeField] private DataManager DataManager { get; set; }
이렇게 하면 Inspector에서 값을 할당할 수 있지만, 외부에서 접근 불가능.
📌 결론
Object Pool은 Queue를 사용하여 FIFO 방식으로 오브젝트를 재사용하면 성능이 향상됨.
프로퍼티(Property)를 사용하면 코드가 더 깔끔하고, 캡슐화를 강화할 수 있음.
자동 구현 프로퍼티({ get; private set; })를 활용하면 불필요한 필드 선언을 줄일 수 있음.
CS0273 오류 해결을 위해 get;만 사용하여 읽기 전용 속성을 만들거나, private set;을 올바르게 설정해야 함.
