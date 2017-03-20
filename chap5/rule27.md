## 규칙 27. 가능하면 제네릭 메서드로 만들 것
### 1. 제네릭화로 클래스 뿐만 아니라 메서드도 혜택을 본다.
```JAVA
  public static Set union(Set s1, Set s2){
    Set result = new HashSet(s1);
    result.addAll(s2);
    return result;
  }
```
- 위 메서드는 컴파일이 되긴 하나, 경고 메세지가 두 개 뜬다..ㅠ(```p.174 참조```)
#### 해결방안
  - 형 안정성이 보장된 메서드를 구현하려면, 세 집합에 보관될 원소의 자료형을 나타내는 형인자를 메서드 선언에 추가하고, 그 인자를 사용해서 메서드를 구현해야 한다.
  - ```형인자를 선언하는 형인자 목록은 메서드의 수정자와 반환값 자료형 사이에 둔다```

  ```JAVA
    public static <E> Set<E> union(Set<E> s1, Set<E> s2){
      Set<E> result = new HashSet<E>(s1);
      result.addAll(s2);
      return result;
    }
  ```

### 2. 사용할 생성자마다 제네릭 정적 팩터리 메서드를 만들면 생성자에 형인자를 명시적으로 전달하지 않아도 된다.
```JAVA
  // 형인자를 변수 선언문의 좌, 우에 똑같이 적어야 하기때문에 번거로울 수 있다.
  Map<String, List<String> a = new HashMap<String, List<String>>();

  // 아래와 같이 정적 팩터리 메서드를 이용하자.
  public static <K, V> HashMap<K, V> newHashMap(){
    return new HashMap<K, V>();
  }
  // 이처럼 간결하게 쓸 수 있다.
  Map<String, List<String>> a = newHashMap();
```
