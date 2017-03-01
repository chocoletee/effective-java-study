## 규칙 19. 인터페이스는 자료형을 정의할 때만 사용하라
- 인터페이스를 구현해 클래스를 만든다는 것은, 해당 클래스의 객체로 어떤 일을 할 수 있는지 클라이언트에게 알리는 행위이다.
- 인터페이스는 자료형 역할을 하게 된다.

### 1. 상수 인터페이스 패턴은 인터페이스를 잘못 사용한 것이다.
  ```JAVA
  public interface SangSoo{
    static final double SANGSOO_1 = 1.1;
    static final double SANGSOO_2 = 2.2;
  }
  ```
  - 클래스가 어떤 상수를 어떻게 사용하냐느 하는 것은 구현 세부사항이다.

#### 상수를 API 일부로 공개하고 싶을 때 좋은 방법
  - enum 자료형과 함께 공개하기
  - 객체 생성이 불가능한 유틸리티 클래스에 넣어서 공개하기.
  ```JAVA
  public class SangSoo{
    private SangSoo(){}
    public static final double SANGSOO_1 = 1.1;
    public static final double SANGSOO_2 = 2.2;
  }
  ```

#### 정적 import 기능으로 상수 이름 앞의 클래스명을 제거할 수 있음(jdk 1.5 이상)
  - ```import static com.SangSoo.*;```
