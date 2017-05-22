
## 규칙 36. Override 어노테이션은 일관되게 사용하라
### 1. Override 어노테이션을 빼먹어서 버그생김
  ```JAVA

  /**
   *
   * @author Sungho Jang
   */
  public class Bigram {
  	private final char first;
  	private final char second;

  	public Bigram(char first, char second){
  		this.first = first;
  		this.second = second;
  	}

    public boolean equals(Bigram b){
      return b.frist == first && b.second == second;
    }

  	public int hashCode() {
  		return 31 * first + second;
  	}

  	public static void main(String args[]) {
  		Set<Bigram> s = new HashSet<Bigram>();
  		for (int i = 0; i < 10; i++) {
  			for(char ch = 'a'; ch <= 'z'; ch++) {
  				s.add(new Bigram(ch, ch));
  			}
  		}

  		System.out.println(s.size());
  	}
  }
  ```
  - 기대하는 결과는 26이지만, 위 코드의 결과는 260 나온다. Object의 equals를 사용하고 있기 때문
    - 아래처럼 equals를 재정의(Override)해주어야 한다.


  ```JAVA
  @Override
  public boolean equals(Object b) {
    if(b instanceof Bigram == false){
      return false;
    }
    Bigram a = (Bigram) b;
    return a.first == first && a.second == second;
  }
  ```

  -  원하는 결과 26이 나온다.
    - 굳굳

### 2. 상위 클래스에 선언된 메서드를 재정의할 때는 반드시 선언부에 Override 어노테이션을 붙여야 한다.
  - 인터페이스를 구현할 때 모든 메서드에 반드시 오버라이드를 붙여야 하는 것은 아님(어짜피 선언된 메서드 재정의 안하면 컴파일 에러남)

### 요약
  - 상위 자료형에 선언된 메서드를 재정의하는 모든 메서드에 Override 어노테이션을 붙이도록 하면 굉장히 많은 오류를 막을 수 있다

### 번외(이거 왜이래?? @kkd)
```JAVA
public boolean equals(Bigram b){
		return b.first == first && b.second == second;
	}

@Override
	public int hashCode() {
		System.out.println("이거");
		return 31 * first + second;
	}

	public static void main(String args[]) {
		/*Set<Bigram> s = new HashSet<Bigram>();
		for (int i = 0; i < 10; i++) {
			for(char ch = 'a'; ch <= 'z'; ch++) {
				s.add(new Bigram(ch, ch));
			}
		}

		System.out.println(s.size());*/
		Set<Bigram> set = new HashSet<Bigram>();

		Bigram a = new Bigram('a', 'a');
		Bigram aa = new Bigram('a', 'a');

		set.add(a);
		set.add(aa);
		System.out.println(set.size());
		System.out.println(a);
		System.out.println(aa);

	}
```
