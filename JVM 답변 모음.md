# Java/ JVM 답변 모음

### 1번 답변:

- autoboxing 이란 컴파일러가 primitive Data를 Object Data로 자동 변환하는 것이다.

- unboxing이란 컴파일러가 Object Data를 primitive Data로 자동 변환하는 것이다.

- primitive Data
  
  - byte, short, int, long, char, float, double, boolean
  
  - 아주 가벼운 데이터를 말한다.
  
  - **스택 메모리**에 머무른다.

- Object Data
  
  - **실제 데이터는 힙메모리에 공유**하고 **레퍼런스만 스택 메모리**에 있다.

- Wrapper Class
  
  - primitvie Data를 Object Data화 시킨 Class이다.

### 6번 답변:

- 외부에서 변경이 불가하여 필드의 객체를 수정할 수 없기 때문에 테스트가 어려워진다.

- 필드 주입은 반드시 DI 프레임워크가 존재해야한다.

### 13번 답변 :

1. Lazy Initialization게으른 초기화 -> 권장 하지 않음
   
   - 생성자를 private으로 만들어 외부에서 생성을 막음
   
   - synchronized 동기화를 활용해 스레드를 안전하게 만듦 -> 성능 저하 발생

```java
class MySingleton {

    private static MySingleton instance;

    public static synchronized MySingleton getInstance() {
        if (instance == null) {
            instance = new MySingleton();
        }
        return instance;
    }

    private MySingleton() {
    }
}
```

2. Lazy Initialization + Double-checked Locking
   
   - 1번과 달리 조건문으로 인스턴스의 존재 여부를 확인한 다음 두번 째 조건문에서 synchronized를 통해 동기화를 시켜 인스턴스를 생성
   
   - 1번에 비해 성능저하 완화

```java
class MySingleton {

    private static MySingleton instance;

    public static MySingleton getInstance() {
        if (instance == null) {
            synchronized (MySingleton.class) {
                if (instance == null) {
                    instance = new MySingleton();
                }
            }
        }
        return instance;
    }

    private MySingleton() {
    }
}
```

3. **Initalization on demand holder idiom(holder에 의한 초기화) ** -> 가장 많이 사용되는 일반적인 싱글톤 클래스 사용 방법
   
   - 클래스 안에 클래스(holder)를 두어 JVM 클래스 로더 매커니즘과 클래스가 로드되는 시점을 이용한 방법
   
   - 2번 방법을 사용하지 않는 이유는 개발자가 직접 동기화 문제에 대한 코드를 작성하면서 회피하려고 하면 프로그램 구조가 그만큼 복잡해지고 비용 문제가 발생할 수 있음.
   
   - 3번 방법은 JVM의 클래스 초기화 과정에서 보장되는 `원자적 특성`을 이용해 싱글톤의 토기화 문제에 대한 책임을 JVM에 떠넘기는걸 활용함
   
   - 클래스 안에 선언한 클래스인 holder에서 선언된 인스턴스는 static이기 떄문에 클래스 로딩시점에 한번만 호출된다. 또한 final을 사용해 다시 값이 할당되지 않도록 만드는 방식을 사용한 것

```java
class MySingleton {

    private static final class InstanceHolder {

        private static final MySingleton instance = new MySingleton();
    }

    public static MySingleton getInstance() {
        return InstanceHolder.instance;
    }

    private MySingleton() {
    }
}
```
