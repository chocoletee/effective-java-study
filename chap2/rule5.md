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
