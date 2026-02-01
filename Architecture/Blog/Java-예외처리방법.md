1. try-catch-finally 사용법

예외가 발생할 가능성이 있는 코드는 try 블록 안에 작성한다.
예외가 발생하면 catch 블록에서 이를 처리하고,
finally 블록은 예외 발생 여부와 관계없이 항상 실행된다.

try {
    int result = 10 / 0;
} catch (ArithmeticException e) {
    System.out.println("예외 발생");
} finally {
    System.out.println("항상 실행");
}

finally는 자원 해제나 마무리 작업에 주로 사용된다.

⸻

2. throw 문

throw는 개발자가 의도적으로 예외를 발생시킬 때 사용한다.
잘못된 입력이나 비정상적인 상태를 명확히 표현할 수 있다.
if (age < 0) {
    throw new IllegalArgumentException("나이는 음수가 될 수 없다");
}

3. 멀티 catch 문

하나의 try 블록에서 여러 예외를 한 번에 처리할 수 있다.
예외 처리 로직이 같을 때 유용하다.

try {
    // 코드
} catch (IOException | SQLException e) {
    e.printStackTrace();
}


⸻

4. 예외 메시지 출력

예외 객체는 발생 원인에 대한 정보를 담고 있다.
getMessage()와 printStackTrace()로 확인할 수 있다.

catch (Exception e) {
    System.out.println(e.getMessage()); // 예외 메시지
    e.printStackTrace();                 // 호출 스택 출력
}

5. 사용자 정의 예외 처리

기존 예외로 의미 표현이 부족할 경우, 직접 예외 클래스를 만들어 사용할 수 있다.

class InvalidUserException extends RuntimeException {
    public InvalidUserException(String message) {
        super(message);
    }
}

throw new InvalidUserException("유효하지 않은 사용자");

도메인에 맞는 예외를 정의하면 코드의 의도가 명확해진다.

⸻

6. 예외 처리가 중요한 이유

예외 처리는 프로그램의 비정상 종료를 방지하고 안정성을 높이기 위해 필수적이다.
오류 상황을 예측하고 적절히 대응함으로써, 시스템 전체의 신뢰성을 유지할 수 있다.
또한 예외를 통해 문제의 원인을 명확히 전달하면 디버깅과 유지보수가 쉬워진다.