## 배열 대신 리스트를 써라

#### 배열과 제네릭자료형의 차이

##### 1. 배열은 공변 자료형, 제네릭은 불변 자료형

- 배열 : Sub extends Super -> Sub[]도 Super 의 하위 자료형

- 제네릭 : List<Super> List<Sub> 는 상하위 자료형이 될 수 없다.

```JAVA
// 실행 시 예외 발생 (컴파일 성공)
Object[] objectArray = new Long[1];
objectArray[0] = "I don't fit in";  // ArrayStoreException 발생

// 컴파일 되지 않음
List<Object> ol = new ArrayList<Long>();    // 자료형 불일치
ol.add("I don't fit in");
```
**컴파일 시 타입 안정성을 확보한다.**

##### 2. 배열은 실체화 되는 자료형

- 배열의 각 원소의 자료형은 실행시간에 정해진다.
- 제네릭은 삭제 과정을 통해 구현된다.
  - 자료형에 관계된 조건들은 컴파일 시점에만 사용되고 실행될 때는 삭제

#### 제네릭 배열 생성은 왜 허용되지 않을까?

 - 타입 안정성이 보장되 않기 때문!

```JAVA
List<String>[] stringLists = new List<String>[1]; // 컴파일 에러를 발생하게 해야 한다!
List<Integer> intList = Arrays.asList(42);
Object[] objects = stringLists;
objects[0] = intList;

String s = stringLists[0].get(0); // ClassCastException
```

> 실체화 불가능  :  프로그램이 실행될 때 해당 자료형을 표현하는 정보의 양이 컴파일 시점에 필요한 정보의 양보다 적은 자료형. 실행 시점에 해당 자료형의 모든 정보를 이용할 수 없는 자료형 (ex> E, List<E>, List<String>..)

#### 제네릭을 vararg 와 혼용하면 경고 메세지가 많이 출력된다.

- varargs 메서드를 호출할 때마다 varargs 인자들을 담을 배열이 생성되는데 이 배열이 실체화 가능 자료형이 아닐 때 경고!!

#### 제네릭 배열 생성 오류에 대한 가장 좋은 해결책은 E[] 대신 List[E] 를 사용하는 것이다.

```JAVA
//제네릭 없이 작성한 reduce 함수. 병행성 문제가 있다.
static Object reduce(List list, Function f, Object initVal) {
    synchronized(list) {
        Object result = initVal;
        for (Object o : list) result = f.apply(result, o);
        return result;
    }
}

interface Function {
    Object apply(Object arg1, Object arg2);
}
```

동기화 영역 안에서 불가해 메서드(alien method)를 호출하면 안된다(Rule.67) -> 락을 건 상태에서 리스트를 복사한 다음, 복사본에 작업하도록 수정해야함

```JAVA
//제네릭 없이 작성한 reduce 함수. 병행성문제 없음
static Object reduce(List list, Function f, Object initVal) {
    synchronized(list) {
        Object snapshot = list.toArray(); // 리스트 내부적으로 락을 건다.
        Object result = initVal;
        for (Object o : snapshot) result = f.apply(result, o);
        return result;
    }
}

interface Function<T> {
    T apply(T arg1, T arg2);
}
```

```JAVA
static Object reduce(List list, Function f, Object initVal) {
    synchronized(list) {
        // E[] snapshot = list.toArray(); 컴파일 오류 발생!! Required = Object[]
        E[] snapshot = (E[]) list.toArray(); // 리스트에 락을 건다.
        Object result = initVal;
        for (Object o : snapshot) result = f.apply(result, o);
        return result;
    }
}
```

이 코드도 안전한 것은 아니다! 컴파일 시점의 자료형은 E[] 인데, Integer[] 등이 될 수 있다. 실행 시간에는 Object[] 이므로 위험하다.

실체화 불가능 자료형 배열로의 형변환은 특별한 상황에서만 시도 해야 한다(Rule.26)

_결국!!! 배열 대신 리스트를 쓰자!_

```JAVA
static Object reduce(List list, Function f, Object initVal) {
    synchronized(list) {
        List<E> snapshot;
        synchronized(list) {
            snapshot = new ArrayList<E>(list); // ClassCastException 발생 늬늬
        }

        E result = initVal;

        for (E e : snapshot)
          result = f.apply(result, e);

        return result;
    }
}

interface Function<T> {
    T apply(T arg1, T arg2);
}
```

### 요약

1. 배열 : 공변 자료형이자 실체화 가능 자료형, 형 안전성 보장 늬늬
2. 제네릭 : 불변자료형이며 실체화 불가능 (형인자의 정보는 실행시간에 삭제), 형 안전성 보장

### 결론 : 배열, 제네릭 섞어쓰다가 컴파일 오류나 경고 메세지 만나면 배열을 리스트로 바꾸자!
