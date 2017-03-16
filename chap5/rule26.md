
## 규칙 26. 가능하면 제네릭 자료형으로 만들 것
### 1. 클래스를 제네릭화 하는 방법 두 가지
#### 1-1 선언부에 형인자를 추가하는 것
```JAVA
  public class Stack<E>
    private E[] elements; // 요로코롬
    private int size = 0;
    private static final int DEFAULT_INITIAL_CAPACITY = 16;

    public static() {
      elements = new E[DEFAULT_INITIAL_CAPACITY];
    }

    public E Pop() {
      if(size ==0)
        throw new EmptyStackException();

        E result = elements[--size];

        elements[size] = null;
        return result;
    }
  ...
```
  - 위에선 하나의 오류가 나는데, ```elements = new E[DEFAULT_INITIAL_CAPACITY]``` 해당 부분이다.
    - E 같은 실체화 불가능 자료형으로는 배열을 생성할 수 없다.
    - 이 문제를 해결하기 위해 제네릭 배열을 만들수 없다는 조건을 우회하는 방법이 있다.
    ```
      - Object 배열을 만들어서 제네릭 배열 자료형으로 형 변환하는 방법이다.
        -> elements = (E[]) new Object[DEFAULT_INITIAL_CAPACITY];
      하지만 형안정성을 보장하는 방법은 아님
      - 형 안정성을 보장하기 위해서는 개발자가 직접 형변환이 프로그램의 형 안정성을 해치지 않음을 확실히 해야 한다.
    ```
    - 안정성 증명이 되었다면, 경고를 억제하되 범위는 최소한으로 줄여야 한다.
    ```JAVA
      @SuppressWarnings("unchecked")
      public stack(){
        elements = (E[]) new Object[DEFAULT_INITIAL_CAPACITY];
      }
    ```

#### 1-2 elements의 자료형을 E[]에서 Object[]로 바꾸는 것
  ```JAVA
    public E Pop() {
      if(size ==0)
        throw new EmptyStackException();

        @SuppressWarnings("unchecked") E result = (E) elements[--size];

        elements[size] = null;
        return result;
    }
  ```
  - 여기선 오류가 아닌 경고로 바뀜... ㅋ

### 2. 둘 중 뭘 쓸지는 개취
  - 보통 첫 번째를 많이 쓴다.
    - 그 이유는 배열을 사용하는 코드가 클래스 이곳저곳에 흩어져 있으면 두번째 방법은 찾아서 E로 형변환을 모두 해주어야함, 첫 번째는 필드만 형변환 하면 됨

### 3. 형진자를 제한하는 제네릭 자료형도 있다.
  ```JAVA
    class DelayQueue<E extends Delayed> implements BlockingQueue<E>;
  ```
  - 위와 같은 E를 한정적 형인자라 함

### 4. 요약
  - 제네릭 자료형은 클라이언트가 형변환을 해야만 사용할 수 있는 자료형보다 안전할 뿐 아니라 사용하기도 쉽다.
  - 새로운 자료형을 설계할 때는 형변환 없이도 사용할 수 있도록 하라.
  - 시간 있을 때마다 기존 자료형을 제네릭 자료형으로 변환하라.
