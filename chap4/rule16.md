## 규칙 16. 계승하는 대신 구성하라
### 1. 메서드 호출과 달리, 계승은 캡슐화 원칙을 위반한다.
  - 하위 클래스가 정상 동작하기 위해서는 상위 클래스의 구현에 의존할 수 밖에 없음
  ``` JAVA
  class InstrumentedSet<E> extends HashSet<E> {
  	private int addCount = 0;

  	public InstrumentedSet() {
  	}

  	@Override
  	public boolean add(E e) {
  		addCount++;
  		return super.add(e);
  	}

  	@Override
  	public boolean addAll(Collection<? extends E> c) {
  		addCount += c.size();
  		return super.addAll(c);
  		/*Iterator i = c.iterator();
  		while(i.hasNext()){
  			add((E)i.next());
  		}

  		return true;*/
  	}

  	public int getAddCount() {
  		return addCount;
  	}

  	public static void main(String[] args) {
  		InstrumentedSet<String> s = new InstrumentedSet<String>();
  		s.addAll(Arrays.asList("Snap", "Crackle", "Pop"));
  		System.out.println(s.getAddCount());
  	}
  ```
  - 하위 클래스를 망가뜨릴 수 있는 요인
    - 상위 클래스에 새로운 메서드가 추가될 수 있다는 것
    ```
    예시 :
      어떤 프로그램의 보안이 원소가 특정 술어를 만족한다는 사실에 근거해야 할 때,
      술어를 계속 만족시키려면 하위 클래스에서 원소를 추가할 수 있는 모든 메서드를 재정의해서 해결가능하다.
      하지만 상위 클래스에 메서드가 추가되면, 재정의하지 않은 새 메서드를 하위 클래스에서 호출하여, 잘못된 객체를 넣게 될지도 모른다.
    ```
