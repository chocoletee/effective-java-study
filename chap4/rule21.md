## 규칙 21. 전략을 표현하고 싶을 때는 함수 객체를 사용하라
  - 함수 포인터, 대리자, 람다처럼 특정 함수를 호출할 수 있는 능력을 저장하고 전달할 수 있도록 하는 것들이 있다.
  - 이런 친구들은 보통 함수의 인자로 함수를 전달 하기위해 사용
    ```JAVA
    void qsort (void *base, size_t nel, size_t width, int (*compare)(const void *, const void *))
    ```
    - 파라미터 :
      - 정렬 할 배열
      - 배열의 총 엘리먼트 개수
      - 배열 한 개의 크기
      - ```비교를 수행할 함수의 포인터```
  - 위의 C언어의 qsort함수는 ```전략 패턴```의 한 사례임

### 1. JAVA는 포인터가 없지만, 객체 참조를 통해 비슷한 효과를 달성할 수 있다.
  ```JAVA
  class SLC {
    public int compare(String s1, String s2){
      return s1.length() - s2.length();
    }
  }
  ```
  - ```SLC```객체는 문자열을 비교하는 데 사용될 수 있는 실행 가능 전략이다.
  - ```무상태 클래스```이다.
  - ```싱글턴 패턴```으로 쓸데없는 객체 생성을 방지할 수 있다.
    ```JAVA
    class SLC {
      private SLC(){}
      public static final StringLegnthComparator INSTANCE = new SLC();
      public int compare(String s1, String s2){
        return s1.length() - s2.length();
      }
    }
    ```

### 2. 실행가능 전략 클래스에 어울리는 전략 인터페이스를 정의할 필요가 있다.
  - SLC 객체를 메서드에 전달하기 위해서는 인자의 자료형이 맞아야 한다. 그리고 SLC를 인자로 받는 메서드에서는 다른 전략을 전달할 수 없다. 따라서 Comparator 인터페이스를 정의한 SLC가 해당 인터페이스를 구현하도록 변경해야한다.

```JAVA
  class SLC implements Comparator<String> {
    // 앞 코드와 동일
  }
  // 그냥 짜본 예시..
  // Comparator<T> 는 전략 인터페이스..?
  // StringLengthComaparator, StringLengthReverse는 실행 가능 전략 클래스..?
  class StringLengthComaparator implements Comparator<String> {
  	private StringLengthComaparator(){}
  	public static final StringLengthComaparator INSTANCE = new StringLengthComaparator();

  	@Override
  	public int compare(String o1, String o2) {
  		return 1;
  	}
  }

  class StringLengthReverse implements Comparator<String>{
  	private StringLengthReverse(){}
  	public static final StringLengthReverse INSTANCE = new StringLengthReverse();

  	@Override
  	public int compare(String o1, String o2) {
  		return -1;
  	}
  }


  public class MyJava3 implements Serializable, Cloneable {
  	private Comparator<String> cp;
  	private void useComparator(){
  		System.out.println(cp.compare("a", "b"));
  	}
  	private void setCp(Comparator<String> cp){
  		this.cp = cp;
  	}

  	public static void main(String args[]) {
  		MyJava3 a = new MyJava3();
  		a.setCp(StringLengthComaparator.INSTANCE);
  		a.useComparator();

  		a.setCp(StringLengthReverse.INSTANCE);
  		a.useComparator();
  	}
```
  - 전략 인터페이스를 통해 원하는 실행 가능 전략 클래스를 사용할 수 있다는건가..?

### 3. 실행 가능 전략 클래스는 익명 클래스로 정의하는 경우도 많다.
```JAVA
  Arrays.sort(stringArray, new Comparator<String>(){
    public int compare(String a, String b){
      return a.length() - b.length();
    }
  })
```
  -  sort가 호출될 때마다 새 객체가 만들어지므로, 여러번 수행 되는 클래스라면 함수 객체를 ```private static final``` 필드에 저장하고 재사용하는 것을 고려해 보라.

### 4. 전략 인터페이스가 자료형인 ```public static``` 필드를 갖는 ```호스트 클래스```를 정의하는 것도 방법이다.
  - 전략 인터페이스는 실행 가능 전략 객체들의 자료형 구실을 하기 때문에, 실가전클은 굳이 ```public```으로 공개할 필요가 없다.
  - 실가전클은 호스트 클래스의 ```private``` 중첩 클래스로 정의하면 된다.
  ```JAVA
  public int compareToIgnoreCase(String var1) {
    return CASE_INSENSITIVE_ORDER.compare(this, var1);
  }
  ```

### 요약
  - 함수 객체의 주된 용도는 전략 패턴을 구현하는 것
  - 전략 인터페이스를 선언하고, 실행 가능 전략 클래스가 전부 해당 인터페이스를 구현하도록 해야함
  - 실행 가능 전략이 한 번만 사용되는 경우에는 보통 그 전략을 익명 클래스 객체로 구현
  - 반복적으로 사용된다면 ```private static``` 멤버 클래스로 전략을 표현한 다음, 전략 인터페이스가 자료형인 ```public static final``` 필드를 통해 외부에 공개하는 것이 바람직함
