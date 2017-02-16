## 규칙13. 클래스와 멤버의 접근 권한은 최소화하라

### 1. 잘 설계된 모듈의 가장 중요한 속성은 세부사항을 다른 모듈에 잘 감추느냐의 여부이다.
  - 정보은닉, 캡슐화


  #### 1-1. 정보은닉의 중요성
    - 시스템의 의존성을 낮춤(각자 모듈이 각자의 역할에 충실하기 때문)
    - 각자 개별적으로 개발, 시험, 최적화, 이해, 변경할 수 있음 -> 개발 속도 향상


### 2. 각 클래스와 멤버는 가능한 한 접근 불가능하도록 만들라.
  - 정상 동작을 보증하는 한도 내에서 가장 낮은 접근 권한을 설정하라.

  - 최상위 레벨 클래스나 인터페이스는 가능한 package-private 선언 해야한다.
    - -> 클라이언트 코드를 깨뜨릴 걱정 없이 자유로이 변경, 삭제, 대체할 수 있게 됨

  - package-private로 선언된 최상위 클래스, 인터페이스를 사용하는 사용자 클래스가 하나뿐이라면, 해당 클래스를 사용자 클래스의 private 중첩 클래스로 만들 것을 고려해 보자.
    - -> 단 하나의 클래스 만이 접근 권한을 갖게 됨

    ```JAVA
    class Lattee
    {
      private final String coffee;
      private final int shots;
      private class Lattee2{

      }
      Lattee(String coffee, int shots){
        this.coffee = coffee;
        this.shots = shots;
      };
    }
    ```
  - private > package-private > protected > public

  - 메서드의 접근 권한을 줄일 수 없는 경우가 하나 있다.
    - 상위 클래스 메서드를 제재정의 할 때는 원래 메서드의 접근 권한보다 낮은 권한을 설정할 수 없다.
    - 따라서 인터페이스를 구현하는 클래스를 만들 때는 인터페이스의 모든 메서드를 public으로 선언해야한다.

  - 테스트 때문에 접근 권한을 열어주고 싶을 때는 private 멤버를 package-private로 만드는 것 까지는 괜찮다.

### 3. 객체 필드는 절대로 public으로 선언하면 안 된다.
  - 변경 가는 public 필드를 가진 클래스는 다중 스레드에 안전하지 않다.
  - public으로 선언하면 공개 API의 일부가 되어, 삭제 혹은 수정할 수 없게 된다.
  - 어떤 상수들이 클래스로 추상화된 결과물의 핵심적 부분을 구성한다고 판단되는 경우, 해당 상수들을 public static final 필드로 선언하여 공개할 수 있다.
    - -> 이런 필드는 기본 자료형 혹은 변경 불가능 객체를 참조해야 함

#### 3-1. public static final 배열 필드를 두거나, 배열 필드를 반환하는 접근자를 정의하면 안 된다.
  - 배열 내용을 변경할 수 있게 되어, 보안 문제가 발생한다.

```JAVA
  // 보안 문제를 초래할 수 있는 코드
  public static final String[] VALUES = {};

  // 이렇게 바꾸자 - 첫번째 방식
  // 변경 불가능한 public 리스트를 하나 만들자.
  private static final String[] PR_VALUES = {};
  public static final List<String> VALUES =
    Collections.unmodifiableList(Arrays.asList(PR_VALUES));

  // 이렇게 바꾸자 - 두번째 방식
  // 배열을 복사해서 반환
  private static final String[] PR_VALUES = {};
  public static final String[] values() {
    return PR_VALUES.clone();
  }
```
  - 클라이언트가 어떤 작업을 원할지 따져보고 결정하자.

### 4. 결론
  - 접근 권한은 가능한 낮추라
  - public API 설계 후, 다른 모든 클래스, 인터페이스 ,멤버는 API 제외하라.
  - public static final 필드를 제외한 어떤 필드도 public ㄴㄴ
  - public static final 필드가 참조하는 객체는 변경 불가능 객체로 만들라.
