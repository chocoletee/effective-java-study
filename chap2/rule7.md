Rule 7. 종료자 (finalize()) 사용을 피하라
-----------------------------------------

### 종료자?

Object 클래스에는 protected 메소드인 finalize 메소드가 있는데 이 객체는 자바의 모든 클래스에서 재정의 할 수 있다.

### Java의 finalize는 C++의 destructor와 성격이 다르다.

-	C++ Destructor : 객체에 할당된 메모리를 해제하는 역할. 생성자의 정 반대
-	Java finalize : 객체가 GC에 의해 수집되기 직전에 마지막으로 호출될 메서드

### 종료자(finalizer)를 사용하면 안되는 이유.

-	언제 실행될 지 예측할 수 없다.
-	객체 메모리 반환이 늦어질 수 있다.
-	프로그램 성능이 떨어진다.

### 해결 방안

-	명시적인 종료 메서드를 정의한다.

	-	이 때, 이 객체가 유효한지를 저장하는 private 필드를 두어, 유효하지 않은 객체일 때 Exception
	-	ex) OutputStream..의 close()
	-	객체 종료를 보장하기 위해 주로 try-finally 문과 함께 쓰인다.

```JAVA
// try-finally 블록을 통해 종료 함수 실행 보장
Foo foo = new Foo(...);
try {
  //실행 작업
  ...
} finally {
  // 종료 함수 호출
  foo.terminate();
}
```

-	자바 7 이상부는 try-with-resources 지원

```JAVA
try(TestObject obj = new TestObject) {
  // 실행 작업
} catch(...) {

}
```

### 종료자가 적합한 경우

-	명시적 종료 메서드 호출을 잊을 경우 안전망 역할

	-	언제 호출 될진 모르겠지만 어쨋든 반환은 된다.
	-	단 이런 경우 logging

-	native peer 와 연결된 객체를 다룰 때. (native method : http://roughexistence.tistory.com/81\)

	-	native peer : java 객체가 native method를 통해 기능 수행을 위임하는 네이티브 객체

### 종료자 사용 시 주의 할 점

-	Chaining이 자동으로 이루어지지 않는다. 하위클래스에서 상위클래스의 종료자를 명시적으로 호출.

```JAVA
 @Override protected void finalize() throws Throwable {
   try {
     ...// 하위 클래스의 상태를 종료
   } finally {
     super.finalize();
   }
 }
```

-	이 문제를 해결하는 방법은 모든 객체마다 여벌의 객체를 만드는 것(종료 보호자).

```JAVA
 public class Foo {
   //이 객체는 Foo 객체를 종료시키는 역할만 한다.
   private final Object finalizerGuardian = new Object() {
     @Override protected void finalize() throws Throwable {   
       ...// 바깥 Foo 객체를 종료 시킴
     }
   };
 }
```
