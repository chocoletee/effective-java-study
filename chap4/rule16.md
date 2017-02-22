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
      하지만 상위 클래스에 메서드가 추가되면, 재정의하지 않은 새 메서드를 하위 클래스에서 호출하여, 
      잘못된 객체를 넣게 될지도 모른다.
    ```
     - 재정의 대신 새 메서드로 만들어 넣으면 계승해도 괜찮을 거라 생각할 수 있다
    ```
    예시 :
      새 릴리스에 추가된 상위 클래스 메서드와 하위 클래스에 정의한 메서드가 같은 시그너처인데,
      반환값 자료형만 다르면 하위클래스는 더 이상 컴파일 되지 않을 것이다..ㅠ
    ```

### 2. 해결방안
  - 새로운 클래스에 기존 클래스 객체를 참조하는 private 필드를 하나 두는 것
    - -> __구성(composition)이라 부름...!__
    - 새로운 클래스에 포함된 메서드는 기존 클래스에 있는 메서드 가운데 필요한 것을 호출해서 결과를 반환하면 됨(__전달__) -> 전달 기법으로 구현된 메서드를 __전달 메서드__ 라 캄

      ``` JAVA
      class InstrumentedSet<E> extends ForwardingSet<E>{

        private int addCount = 0;

        public InstrumentedSet(Set<E> s) {
          super(s);
        }

        @Override
        public boolean add(E e){
          addCount++;
          System.out.println("add 호출");
          return super.add(e);
        }

        @Override
        public boolean addAll(Collection<? extends E> c){
          addCount += c.size();
          System.out.println("addAll 호출");
          return super.addAll(c);
        }

        public int getAddCount(){
          return addCount;
        }

        public static void main(String args[]){
          TreeSet<String> ts = new TreeSet<String>(Arrays.asList("최재선", "김운지"));
          Set<String> s = new InstrumentedSet<String>(ts);

          s.addAll(ts);
        }
        }

        class ForwardingSet<E> implements Set<E> {

        private final Set<E> s;

        public ForwardingSet(Set<E> s){
          this.s = s;
        }

        @Override
        public boolean addAll(Collection<? extends E> c) {
          System.out.println("부모 addAll 호출");
          return s.addAll(c);
        }

        @Override
        public boolean add(E e) {
          System.out.println("부모 add 호출");
          return s.add(e);
        }
        ```

    - InstrumentedSet을 포장 클래스라고 부름(다른 Set 개게를 포장하고 있기 때문) -> 장식자 패턴[(참고)](http://brobrobro.tistory.com/entry/Effective-JAVA-Item16-Decorator-Pattern)
    - 포장 클래스는 단점이 별로 없지만 역호출 프레임워크와 함께 사용하기엔 적절치 않음
      - 포장된 객체는 포장 객체에 대해선 모르기 때문에, 자기 자신에 대한 참조를 전달할 것임

### 3. 계승은 하위 클래스가 상위 클래스의 하위 자료형이 확실한 경우에만 바람직 함
  - IS - A 관계가 성립할 때만 계승하도록 하자.
  - 위반 사례 : Stack, Properties
  - 구성 기법이 적절한 곳에 계승을 사용하면 구현 세부사항이 쓸데없이 노출됨
  - 의마가 혼란스러운 프로그램이 만들어 질 수 있음
    - p.getProperty(key), p.get(key)

### 4. 요약
  - 계승은 캡슐화 원칙을 침해하므로 문제를 발생시킬 소지가 있다는 것이다.
  - 클래스 사이에 IS-A 관계가 있을 떄만 사용하는 것이 좋다.
  - 구성과 전달 기법을 사용하자.
