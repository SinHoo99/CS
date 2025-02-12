# 🎯 C# 프로퍼티(Property), Object Pool을 Queue로 사용하는 이유 정리

## **프로퍼티(Property)란?**
- **클래스 내부의 필드(Field)에 대한 접근을 제어하는 기능**.
- `get;`으로 값을 읽고, `set;`으로 값을 설정할 수 있음.
- **캡슐화(Encapsulation)**를 통해 필드에 직접 접근하지 않도록 보호할 수 있음.

```csharp
class Player
{
    private int _health;  // private 필드

    public int Health  // 프로퍼티
    {
        get { return _health; }  // 값을 가져옴 (읽기)
        set { _health = value; } // 값을 설정 (쓰기)
    }
}

## **Object Pool을 Queue로 사용하는 이유**
- **FIFO(First-In-First-Out) 방식 유지**  
  - 먼저 사용된 오브젝트가 먼저 반환됨.  
  - 오래된 오브젝트부터 재사용 가능 → 균등한 사용.  
- **빠른 성능 (O(1) 연산)**  
  - `Queue.Enqueue()` / `Queue.Dequeue()`는 O(1) 연산.  
  - `List`는 `RemoveAt(0)` 시 성능 저하 (O(n)).  
- **GC(Garbage Collection) 최소화**  
  - 기존 오브젝트를 재사용하여 메모리 낭비 방지.  

```csharp
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
