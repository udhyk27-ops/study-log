정보처리기사 실기 문제를 풀다가 헷갈리는 문제가 하나 있었습니다.

제네릭 클래스 안에서 T t를 오버로딩된 메서드에 전달했는데, 실제 객체는 Integer임에도 Object가 호출되는 문제였습니다.

 

해설에는 “타입 소거” 때문이라고 되어 있었는데, 정확히 이해가 잘 되지 않았습니다.

그래서 문제 그대로 코드를 작성하고, 전달되는 타입을 확인하기 위해 디버깅 출력도 추가했습니다.

class Printer {
    void print(Integer a) {
        System.out.println("Integer");
    }
    void print(Object a) {
        System.out.println("Object"); // 실행됨
    }
    void print(Number a) {
        System.out.println("Number");
    }
}
class Main {
    public static void main(String[] args) {
        new Collection<>(0).print();
    }
    public static class Collection<T> {
        T value;
        public Collection(T t) {
            value = t;
            System.out.println("생성자 t : " + t.getClass().getName());
            System.out.println("생성자 value : " + value.getClass().getName());
        }
        public void print() {
            System.out.println("value1 : " + value.getClass().getName());
            new Printer().print(value);
            System.out.println("value2 : " + value.getClass().getName());
        }
    }
}

실행 결과

생성자 t : java.lang.Integer
생성자 value : java.lang.Integer
value1 : java.lang.Integer
Object
value2 : java.lang.Integer

value.getClass()는 계속 Integer를 출력하는데, 메서드는 print(Object)가 호출됩니다.

핵심은 오버로딩은 컴파일 시점에 결정된다는 점입니다.
메서드를 선택할 때는 실제 객체 타입이 아니라 변수의 선언 타입을 기준으로 판단합니다.


Collection<T> 안에서 value의 선언 타입은 T입니다.
그런데 제네릭은 컴파일 과정에서 타입 소거가 발생합니다.

 

별도의 상한을 지정하지 않은 T는 타입 소거 후 Object로 변환됩니다.

 

다른 오버로딩 메서드를 호출하려면

1. 명시적으로 캐스팅

new Printer().print((Integer) value);

 

컴파일러가 Integer 타입으로 인식하기 때문에 print(Integer)가 호출됩니다.

2. 제네릭 상한 지정

class Collection<T extends Number> {
    T value;
    void test() {
        new Printer().print(value);
    }
}

 

이 경우 타입 소거 후 T는 Number가 되므로, print(Number)를 호출하게 됩니다.

 

타입 소거 후에는 아래와 같은 형태로 컴파일됩니다.

Number value;
new Printer().print(value);

 

오버로딩은 런타임의 실제 객체 타입이 아니라 컴파일 시점의 선언 타입을 기준으로 메서드를 선택합니다.

그리고 제네릭은 타입 소거가 발생하기 때문에 상한을 지정하지 않은 T는 Object가 되고, T extends Number처럼 상한을 지정하면 Number가 됩니다.