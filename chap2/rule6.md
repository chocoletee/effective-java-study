

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
- 실수로 객체 참조를 유지하는 경우, 그 객체를 통해 참조되는 다른 객체들도 GC에서 제외된다. 따라서 만기참조가 몇 개라도 있으면 많은 객체가 GC에서 제외될 수 있다. __-> 성능에 악영향을 줄 수 있음__

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
  - 만기 참조를 제거하는 가장 좋은 방법은 해당 참조가 보관된 변수가 유효범위(scope)를 벗어나게 두는 것이다. 변수를 정의할 때 그 유효범위를 최대한 좁게 만들면 자연스럽게 해결된다 캄.(규칙 45)
    - ex) 처음으로 사용하는 곳에서 변수를 선언하라..

### 1 - 2. 자체적으로 관리하는 메모리가 있는 클래스를 만들 때는 메모리 누수가 발생하지 않도록 주의해야 한다.  
  - 스택은 자체적으로 메모리를 관리하기 때문에 메무리 누수를 유발한다.
  - 더이상 사용되지 않는 원소안에 있는 객체 참조는 반드시 null로 바꾸자.
