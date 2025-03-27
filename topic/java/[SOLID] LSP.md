### 리스코프 치환 원칙 (Liskov Substitution Principle)

상속 구조에서 부모 클래스의 인스턴스를 자식 클래스의 인스턴스로 치환할 수 있어야 한다는 원칙입니다.  
즉, 자식 클래스는 부모 클래스의 기능 확장은 가능하지만, 부모 클래스의 동작을 변경해서는 안 됩니다.

<br />

### LSP를 위반할 경우 문제점

- 다형성이 깨짐 : 부모 클래스의 인스턴스를 자식 클래스의 인스턴스로 치환했을 때, 기능이 동작하지 않을 수 있음
- 시스템 안정성 저하 : 예상치 못한 버그가 발생할 수 있음

<br />

### 리스코프 치환 원칙을 지키지 않은 코드

LSP 위반 사례로 가장 많이 사용되는 사각형, 정사각형 예제 코드입니다.

```java
class Rectangle {
    protected int width;
    protected int height;

    public void setWidth(int width) {
        this.width = width;
    }

    public void setHeight(int height) {
        this.height = height;
    }

    public int getArea() {
        return width * height;
    }
}

class Square extends Rectangle {
    @Override
    public void setWidth(int width) {
        super.width = width;
        super.height = width;
    }

    @Override
    public void setHeight(int height) {
        super.width = height;
        super.height = height;
    }
}

public class Main {
    public static void main(String[] args) {
        Rectangle rectangle = new Square();
        rectangle.setWidth(10);
        rectangle.setHeight(5);

        System.out.println("사각형의 넓이 : " + rectangle.getArea()); // 50이 아닌 25 출력
    }
}
```

문제점

- Square는 Rectangle를 대체하지 못함
- 예상치 못한 결과 발생

<br />

### 리스코프 치환 원칙을 준수한 코드

```java
interface Shape {
    int getArea();
}

class Rectangle implements Shape {
    protected int width;
    protected int height;

    public Rectangle(int width, int height) {
        this.width = width;
        this.height = height;
    }

    public int getArea() {
        return width * height;
    }
}

class Square implements Shape {
    private int side;

    public Square(int side) {
        this.side = side;
    }

    public int getArea() {
        return side * side;
    }
}

public class Main {
    public static void main(String[] args) {
        Shape rectangle = new Rectangle(10, 5);
        Shape square = new Square(5);

        System.out.println("사각형의 넓이 : " + rectangle.getArea());
        System.out.println("정사각형의 넓이 : " + square.getArea());
    }
}
```

- Square와 Rectangle이 Shape 인터페이스를 구현
- 각 클래스는 자신의 특성에 맞게 구현
- Shape 타입 사용 시, 어떤 구현체여도 안전하게 사용 가능

<br />

### 리스코프 치환 원을 적용했을 때의 장점

- 다형성이 보장됨 : 자식 클래스의 인스턴스를 부모 클래스에 대체해도 코드가 정상 동작
- 시스템 안정성 향상 : 예상치 못한 버그 방지

<br />

### 결론

리스코프 치환 원칙은 다형성을 보장하기 위한 원칙으로 이를 위반할 경우 예상치 못한 문제점이 발생하여 시스템 안정성 및 유지 보수가 어려워집니다.

<br />

#### 참고 자료

- ChatGPT 대화 내용