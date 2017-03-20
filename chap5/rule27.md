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

### 3. 제네릭 싱글턴 패턴
  - 때론 변경이 불가능하지만 많은 자료형에 적용 가능한 객체를 만들어야 할 때가 있다캄..
  - 제네릭은 ```삭제``` 과정을 통해 구현되므로 모든 필요한 형인자화 과정에 동일 객체를 활용할 수 있는데, 그러려면 필요한 형인자화 과정마다 같은 객체를 나눠주는 정적 팩터리 메서드를 작성해야 한다캄..
  - ```Collections.reverseOrder, Collections.emptySet``` 여기 많인 쓴다고 하니 각자 참조..!
#### 예시
- T형의 값을 받고 반환하는 함수를 나타내는 인터페이스가 있다 카자..!
```JAVA
  public interface UnaryFunction<T> {
    T apply(T arg);
  }
```
- 항등함수를 구현해보자..! 무상태 함수이므로 새 함수를 계속 만드는 것은 낭비다. 제네릭 싱글턴으로 해결해주자.
      ```JAVA
        private static UnaryFunction<Object> IDENTITY_FUNCTION =
          new UnaryFunction<Object>() {
            public Object apply(Object arg) { return arg; }
          }

        @SuppressWarnings("언췍")
        public static <T> UnaryFunction<T> identityFunction(){
          return (UnaryFunction<T>) IDENTITY_FUNCTION;
        }
      ```
    - IDENTITY_FUNCTION를 (UnaryFunction<T>)로 형변환하면 무점검 형변환 경고가 발생한다. 하지만 걱정마라.
    인자를 수정없이 반환하므로, T가 무엇이든 간에 UnaryFunction<T>인 것처럼 써도 형 안정성이 보장된다. 그러니 경고를 억제해도 된다.
    
