## 규칙6. 유효기간이 지난 객체 참조는 폐기하라.<br>


  ```JAVA
  // "메모리 누수(memory leak)"가 어디서 생기는지 보이나요?
  public class Stack {
       private Object[] elements;
       private int size = 0;
       private static final int DEFAULT_INITIAL_CAPACITY = 16;
       public Stack() {
           elements = new Object[DEFAULT_INITIAL_CAPACITY];
       }
       public void push(Object o) {
          ensureCapacity();
          elements[size++] = o;
       }
       public Object pop() {
          if ( size==0 ) {
             throw new EmptyStackException();
          }
          return elemtns[--size];   // size 보다 작은 elements는 참조되어 추 후 쓰레기 수집기가 알지만,
                                    // size 보다 큰 elements는 쓰레기 수집기가 모르는 듯..
       }
        private void ensureCapacity() {
           if (elemtns.length == size) {
              elemnts = Arrays.copyOf(elemnts, 2*size+1);
           }
        }
  }
  ```
- 스택이 커졌다가 줄어들면서 제거한 객체들을 GC가 처리하지 못해서 생긴다. 스택은 ___만기참조___ 를 제거하지 않기 때문
  - 만기참조란? 다시 이용되지 않을 참조
- 실수로 객체 참조를 유지하는 경우, 그 객체를 통해 참조되는 다른 객체들도 GC에서 제외된다. __-> 성능에 악영향을 줄 수 있음__

### 1 - 1. 객체 참조를 null 처리하는 것은 규범(norm)이라기보단 예외적인 조치가 되어야한다.
  - 위의 문제는 쓸 일 없는 객체 참조를 무조건 null로 만들어 해결할 수 있다.
  ```JAVA
  public Object pop() {
      if (size == 0){
        throw new EmptyStackException();
      }
      Object results = elements[--size];
      elements[size] == null;   // 만기 참조 제거
      return results;
  }
  ```
  - 만기 참조를 null로 만들면 나중에 실수로 그 참조를 사용하더라도 NullPointerException이 발생하기 때문에, 프로그램은 오동작하는 대신 바로 종료된다는 장점이 있다.
  - 만기 참조를 제거하는 가장 좋은 방법은 변수를 정의할 때 그 유효범위를 최대한 좁게 만들면 자연스럽게 해결된다 캄.(규칙 45)
    - ex) 처음으로 사용하는 곳에서 변수를 선언하라..

### 1 - 2. 자체적으로 관리하는 메모리가 있는 클래스를 만들 때는 메모리 누수가 발생하지 않도록 주의해야 한다.  
  - 스택은 자체적으로 메모리를 관리하기 때문에 메무리 누수를 유발한다.
  - 더이상 사용되지 않는 원소안에 있는 객체 참조는 반드시 null로 바꾸자.


### 2. 캐시(Cache)도 메모리 누수가 흔히 발생하는 장소다.
  - WeakHashMap을 가지고 캐시를 구현
    - -> 키에 대한 참조가 만기되는 순간 캐시 안에 보관된 키-값 쌍은 자동으로 삭제됨
    
### 3. 메모리 누수가 흔히 발견되는 또 한 곳은 리스너(listener) 등의 역호출자(callback)다.
  - 역호출자를 명시적으로 제거 하지 않을 경우, 메모리는 점유된 상태로 남음
    - -> GC가 즉시 처리할 가장 좋은 방법은 역호출자에 대한 약한 참조만 저장하는 것

### 4. 결론 : 메모리 누수가 생길 수 있음을 사전에 인지하고 방지 대책을 세우자..!
