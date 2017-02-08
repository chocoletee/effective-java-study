## 규칙5. 불필요한 객체는 만들지 말라
<br>

### 기능적으로 동일한 객체는 필요할 때마다 만드는 것보다 __재사용__ 하는 편이 낫다.
- 변경 불가능 객체는 언제나 재사용 할 수 있다.
- 절대로 피해야 할 극단적 예

    ```JAVA
    String s = new String("st");
    ```

- 위의 문장은 실행할 때마다 String 객체를 만드는데, 쓸데없는 짓이다. 순환문이나 자주 호출되는 메서드 안에 있다면, 수백만개의 String 객체가 쓸데없이 만들어질 것이다.

- 아래 문장이 바람직하다. 동일한 객체를 재사용한다.
    ```JAVA
    String s = "st";
    ```

- 문제

    ```JAVA
    public static void main(String args[]) {
		String s = new String("보띠보띠");
		String s1 = "보띠보띠";
		String s2 = "보띠보띠";

		System.out.println("s == s1 : " + (s == s1));
		System.out.println("s1 == s2 : " + (s1 == s2));
		System.out.println("s == s2 : " + (s == s2));
	}

  // 결과?
    ```

#### 다른 예시(변경 가능한 객체 재사용)
- 변경 불가능 객체뿐 아니라, 변경 가능한 객체도 재사용할 수 있다.
```JAVA
public class Person {
    private final Date birthDate;

    // 다른 필드와 메서드, 생성자 생략
    
    public boolean isBabyBoomer(){
      // 생성비용이 높은 객체를 쓸데없이 생성한다
        Calendar cal = Calendar.getInstance(TimeZone.getTimeZone("GMT"));
        cal.set(1950,Calendar.JANUARY,1,0,0,0);
        Date start = cal.getTime();
        cal.set(1955,Calendar.JANUARY,1,0,0,0);
        Date end = cal.getTime();
        return birthDate.compareTo(start) >= 0 && birthDate.compareTo(end) < 0;
    }
}
```
- 아래와 같이 개선하면, Calendar, TimeZone, Date객체를 클래스 초기화시 한 번만 만든다.
```JAVA
public class Person {
    private final Date birthDate;
    private static final Date START;
    private static final Date END;
    static{
        Calendar cal = Calendar.getInstance(TimeZone.getTimeZone("GMT"));
        cal.set(1950,Calendar.JANUARY,1,0,0,0);
        START = cal.getTime();
        cal.set(1955,Calendar.JANUARY,1,0,0,0);
        END = cal.getTime();
    }
     
    public boolean isBabyBoomer(){
        return birthDate.compareTo(START) >= 0 && birthDate.compareTo(END) < 0;
    }
}
```
- 위와 같이 개선되었더라도, isBabyBoomer 메서드가 한번도 호출되지 않는다면, START, END 필드는 쓸데없이 초기화 되었다고 봐야한다. 그런 상황은 초기화 지연기법(규칙71)을 사용하면 피할 수 있다.

### 객체 표현형 대신 기본 자료형을 사용, 생각지도 못한 자동객체화가 발생하지 않도록 유의하라.
- 자동객체화 : 기본 자료형과 그 객체 표현형을 섞어 샤용할 수 있도록 해주는 것

- 아래 소스는 자동객체화로 인해 성능에 큰 차이를 준다.
```JAVA
public static void main(String[] args){
  Long sum = 0L;
  for (long i = 0; i < Integer.MAX_VALUE; i++){
    sum += i;
  }
  System.out.println(sum);
}
```
sum __(Long 객체)__에 i __(long 자료형)__가 더해질 때 마다, Long 객체가 새롭게 생성되기 때문


### 직접 관리하는 객체 풀을 만들어 객체 생성을 피하는 기법은 객체 생성 비용이 극단적으로 높지 않다면 사용하지 않는 것이 좋다.
