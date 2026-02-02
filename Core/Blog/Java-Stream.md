# Java Stream

Java Stream은 데이터를 저장하지 않고, 데이터를 처리하는 흐름을 표현하는 API이다.
자바 8부터 도입되었으며 컬렉션이나 배열의 요소를 선언적 방식으로 처리하기 위해 사용된다.

Stream의 가장 큰 특징은 중간 연산과 최종 연산으로 구성된다는 점이다.
filter, map 같은 중간 연산은 Stream을 반환하며, 실제 연산은 바로 실행되지 않는다.
count, forEach, collect 같은 최종 연산이 호출되는 순간에만 연산이 수행된다. 이를 **지연 실행(Lazy Evaluation)**이라 한다.

Stream은 반복문처럼 직접 제어하지 않고, 내부에서 반복을 처리한다.
이로 인해 코드가 간결해지고, 처리하려는 의도가 명확하게 드러난다. 또한 병렬 처리로 확장하기도 쉽다.

다만 Stream은 한 번 사용하면 재사용할 수 없고, 단순한 로직에서는 오히려 가독성이 떨어질 수 있다.
따라서 Stream은 데이터 가공과 집계에 적합하며, 복잡한 흐름 제어가 필요한 경우에는 반복문이 더 적절하다.

한 줄로 정리하면,
Java Stream은 컬렉션 데이터를 함수형 방식으로 처리하기 위한 파이프라인 구조의 API이다.


Stream 미사용
int count = 0;
for (int value : array) {
    if (value > 10) {
        count++;
    }
}

Stream 사용
long count = Arrays.stream(array)
                   .filter(value -> value > 10)
                   .count();
// .count() 는 long형 결과 배출


https://www.elancer.co.kr/blog/detail/255