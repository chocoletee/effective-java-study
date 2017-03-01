## 규칙 20. 태그 달린 클래스 대신 클래스 계층을 활용하라
```JAVA
class Figure {
    enum Shape {
        RECTANGLE, CIRCLE
    };

    // Tag field - the shape of this figure
    final Shape shape;

    // These fields are used only if shape is RECTANGLE
    double length;
    double width;

    // This field is used only if shape is CIRCLE
    double radius;

    // Constructor for circle
    Figure(double radius) {
        shape = Shape.CIRCLE;
        this.radius = radius;
    }

    // Constructor for rectangle
    Figure(double length, double width) {
        shape = Shape.RECTANGLE;
        this.length = length;
        this.width = width;
    }

    double area() {
        switch (shape) {
        case RECTANGLE:
            return length * width;
        case CIRCLE:
            return Math.PI * (radius * radius);
        default:
            throw new AssertionError();
        }
    }
}
```
- 위와 같은 태그달린 클래스의 단점
  - 상투적 코드가 반복되는 클래스가 만들어지고, 서로 다른 기능을 위한 코드가 한 클래스에 모여있어서 가독성이 떨어짐
  - 객체가 만들어 질 때, 필요없는 기능도 함께 생성되어, 메모리 요구량도 늘어남
- 새로 추가된 도형이 있을때, switch에 추가하지 않으면 error나버림..

### 태그 기반 클래스는 클래스 계층을 얼기설기 흉내 낸 것일 뿐이다
  - 클래스 계층으로 변환하기 위해 추상클래스를 정의하자.
  - 아래와 같이 수정할 수 있다.
```JAVA
  abstract class Figure {
    abstract double area();
  }

  class Circle extends Figure {
      final double radius;

      Circle(double radius) {
          this.radius = radius;
      }

      double area() {
          return Math.PI * (radius * radius);
      }
  }

  class Rectangle extends Figure {
      final double length;
      final double width;

      Rectangle(double length, double width) {
          this.length = length;
          this.width = width;
      }

      double area() {
          return length * width;
      }
  }
 ```
 - 자료형 간의 자연스러운 계층 관계를 반영할 수 있어서 유연성이 높아지고 컴파일 시에 형 검사를 하기 용이해졌다.

### 요약
  - 태그 기반 클래스 사용은 피해야 한다.
  - 태그 필드를 두고 싶다는 생각이 들면, 계층을 통해 태그를 제거할 방법이 없는지 생각해보라.
