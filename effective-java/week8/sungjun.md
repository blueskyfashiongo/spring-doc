# 📌  아이템 41: 정의하려는 것이 타입이라면 마커 인터페이스를 사용하라
* 어떤 메서드도 감고 있지 않고 특정 속성을 가짐을 표시해주는 인터페이스를 마커 인터페이스라 한다. (ex. Serializable) 

  
* 마커 인터페이스가 마커 애너테이션보다 나은 이유 두가지
  - 마커 인터페이스는 이를 구현한 클래스의 인스턴스들을 구분하는 타입으로 쓸 수 있으나, 마커 애너테이션은 타입이 아니라 불가하다.
  - 적용 대상을 더 정밀하게 지정할 수 있다? 마커 애너테이션은 모든 타입에 달 수 있지만 마커 인터페이스는 특정 인터페이스를 구현한 클래스에만 적용할 수 있다.
  
  
* 마커 애너테이션이 마커 인터페이스보다 나은 점
  - 거대한 애너테이션 시스템의 지원을 받는다는 이점, 애너테이션을 적극 활용하는 프레임워크에서 강점을 가짐. 
  
  
* 마커 애너테이션 vs 마커 인터페이스
  - 클래스와 인터페이스 외의 프로그램 요소(모듈, 패키지, 필드, 지역변수 등)에 마킹해야할 때 애너테이션을 쓸 수 밖에 없다.
  - 마커를 클래스나 인터페이스에 적용해야한다면 "이 마킹이 된 객체를 매개변수로 받는 메서드를 작성할 일이 있을까"라고 자문하고, "그렇다" 이면 마커 인터페이스를 쓰자. "없다" 면 마커 애너테이션.
  - 마커 인터페이스를 해당 메서드의 매개변수 타입으로 사용하여 컴파일타임에 오류를 잡아낼 수 있다.


# 📌  아이템 42: 익명 클래스보다는 람다를 사용하라
* 예전에는 함수 객체(추상 메서드를 하나만 담은 인터페이스)를 만드는 주요 수단은 익명 클래스 였다(낡은 기법)

```java
// 익명 클래스의 인스턴스를 함수 객체로 사용
Collections.sort(words, new Comparator<String>() {
  public int compare(String s1, String s2) {
    return Integer.compare(s1.length(), s2.length());
  }
});
```

* 자바 8부터 추상 메서드 하나짜리 인터페이스는 함수형 인터페이스로 이들의 인스턴스를 람다식을 사용해 만들 수 있게 되었다. 

```java
// 람다식을 함수 객체로 사용
Collections.sort(words,
  (s1, s2) -> Integer.compare(s1.length(), s2.length()));
```

* 람다식의 사용 예를 살펴보자

```java
public enum Operation {
    PLUS("+") {
        public double apply(double x, double y) { return x + y; }
    },
    MINUS("-") {  
        public double apply(double x, double y) { return x - y; }
    },
    TIMES("*") {
        public double apply(double x, double y) { return x * y; }
    },
    DIVIDE("/") {
        public double apply(double x, double y) { return x / y; }
    };
    
    private final String symbol;
    
    Operatrion(String symbol) { this.symbol = symbol; }
    
    @Override public String toString() { return symbol; }
    public abstract double apply(double x, double y);
}
```

```java
public enum Operation {
    PLUS("+", (x, y) -> x + y),
    MINUS("-", (x, y) -> x - y),
    TIMES("*", (x, y) -> x * y),
    DIVIDE("/", (x, y) -> x / y);
    
    private final String symbol;
    private final DoubleBinaryOperator op;
    
    Operatrion(String symbol, DoubleBinaryOperator op) { 
        this.symbol = symbol;
        this.op = op;
    }
    
    @Override public String toString() { return symbol; }
    
    public double apply(double x, double y) {
        return op.applyAsDouble(x, y);
    }
}
```

* 단점 
  - 메서드나 클래스와 달리, 람다는 이름이 없고 문서화도 못 한다. 따라서 코드 자체로 동작이 명확히 설명되지 않거나 코드 줄 수가 많아지면 람다를 쓰지 말아야 한다(적정 1줄~3줄).
  - 람다도 익명 클래스처럼 직렬화 형태가 가상머신별로 다를 수 있어 직렬화하는 일은 극히 삼가해야한다.
  - 당연히 추상 클래스나 추상 메서드가 여러 개인 인터페이스의 인스턴스를 만들 때는 사용 할 수 없다.

# 📌  아이템 43: 람다보다는 메서드 참조를 사용하라

* 람다의 가장 큰 특징은 간결함인데 그보다 간결한 메서드 참조가 있다.

```java
// 람다
map.merge(key, 1, (count, incr) -> count + incr);

// 정적 메서드 sum 을 활용한 메서드 참조
map.merge(key, 1, Integer::sum);
```

* 람다로 작성할 코드를 새로운 메서드에 담은 다음, 람다 대신 그 메서드 참조를 사용하는 식이다. 메서드 참조에는 기능을 드러내는 이름을 지어줄 수 있고, 친절한 설명을 문서로 남길 수 있다.

* 메소드 참조의 유형은 다섯가지
  1. 정적 메서드를 가리키는 메서드 참조
     - ex. Integer::parseInt
     - 위의 람다 표현식 ex. str -> Integer.parseInt(str)
  2. 수신 객체를 특정하는 한정적 인스턴스 메서드 참조
     - ex. Instant.now()::isAfter
     - 위의 람다 표현식 ex. Instant then = Instant.now();  
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;   t -> then.isAfter(t)
  3. 수식 객체를 특정하지 않는 비한정적 인스턴스 메서드 참조
     - ex. String::toLowerCase
     - 위의 람다 표현식 ex. str -> str.toLowerCase
  4. 클래스 생성자를 가리키는 메서드 참조
     - ex. TreeMap<K,V>::new
     - 위의 람다 표현식 ex. () -> new TreeMap(K,V)()
  5. 배열 생성자를 가리키는 메서드 참조
     - ex. int[]::new
     - 위의 람다 표현식 ex. len -> new int[len]
  
* 메서드 참조 쪽이 짧고 명확하다면 메서드 참조를 쓰고, 그렇지 않을 때만 람다를 사용하라.

# 📌  아이템 44: 표준 함수형 인터페이스를 사용하라

* 필요한 용도에 맞는게 있다면, 직접 구현하지 말고 표준 함수형 인터페이스를 활용하라(java.util.function)

* java.util.function 패키지에는 총 43개의 인터페이스가 있지만 기본 인터페이스 6개만 기억하자
  1. UnaryOperator<T>
    - 함수 시그니처: T apply(T t)
    - ex. String::toLowerCase 
  2. BinaryOperator<T>
    - 함수 시그니처: T apply(T t1, T t2)
    - ex. BigInteger::add
  3. Predicate<T>
    - 함수 시그니처: boolean test(T t)
    - ex. Collection::isEmpty
  4. Function<T,R>
    - 함수 시그니처: R apply(T t)
    - ex. Arrays::asList
  5. Supplier<T>
    - 함수 시그니처: T get()
    - ex. Instant::now
  6. Consumer<T>
    - 함수 시그니처: void accept(T t)
    - ex. System.out::println
    
* 구조적으로 똑같은 표준 함수형 인터페이스가 있더라도 직접 코드를 작성해야만 할때가 있다
  - 첫 번째, 자주 쓰이며, 이름 자체가 용도를 명확히 설명해준다 (ex. Comparator<T> =: ToIntBiFunction<T,U>)
  - 두 번째, 반드시 따라야 하는 규약이 있다
  - 세 번째, 유용한 디폴트 메서드를 제공할 수 있다
    
* 직접 만든 함수형 인터페이스에는 항상 @FunctionalInterface 애너테이션을 사용하라
  - 첫 번째, 해당 클래스의 코드나 설명 문서를 읽을 이에게 그 인터페이스가 람다용으로 설계된 것임을 알려준다
  - 두 번째, 해당 인터페이스가 추상 메서드를 오직 하나만 가지고 있어야 컴파일 되게 해준다
  - 세 번째, 그 결과 유지보수 과정에서 누군가 실수로 메서드를 추가하지 못하게 막아준다    

* 서로 다른 함수형 인터페이스를 같은 위치의 인수로 받는 메서드들을 다중 정의하여 클라이언트에 모호함을 안겨주지 말자

# 📌  아이템 45: 스트림은 주의해서 사용하라

* 스트림이 제공하는 핵심 추상 개념 2가지
  - 첫 번째, 스트림은 데이터 원소의 유한 혹은 무한 시퀸스를 뜻한다
  - 두 번째, 스트림 파이프라인은 이 원소들로 수행하는 연산 단계를 표현하는 개념이다
    
* 스트림 파이프라인은 소스 스트림에서 시작해 종단 연산으로 끝나며, 그 사이에 하나 이상의 중간 연산이 있을 수 있다. 

* 스트림 파이프라인은 지연 평가된다. 평가는 종단 연산이 호출될 때 이뤄지며, 종단 연산에 쓰이지 않는 데이터 원소는 계산에 쓰이지 않는다. 이런 지연 평가가 무한 스트림을 다룰 수 있게 해주는 열쇠다

* 스트림 API는 메서드 연쇄를 지원하는 플루언트(fluent) API다.

* 스트림 파이프라인은 기본적으로 순차적으로 수행되며 파이프라인을 병렬로 실행시키려면 parallel 메서드를 호출해주기만 하면 되나 효과를 볼 수 있는 상황은 많지 않다.

* 스트림은 다재다능하지만 과용하면 읽기 어렵고 유지보수도 힘들어 진다.

* char용 스트림을 지원하지 않으므로 char 값들을 처리할 때는 스트림을 쓰지 말자 

* 스트림이 안성 맞춤인 경우
  - 원소들의 시퀀스를 일관되게 변환한다
  - 원소들의 시퀀스를 필터링한다
  - 원소들의 시퀀스를 하나의 연산을 사용해 결합한다(더하기, 연결하기, 최솟값 구하기 등) 
  - 원소들의 시퀀스를 컬렉션에 모은다
  - 원소들의 시퀀스에서 특정 조건을 만족하는 원소를 찾는다
    
* 스트림으로 처리 하기 어려운 대표적인 예는 한 데이터가 파이프라인의 여러 단계를 통과할 때 이 데이터의 각 단계에서의 값들에 동시에 접근하기 어려운 경우이다. 스트림 파이프라인은 일단 한 값을 다른 값에 매핑하고 나면 원래의 값은 잃는 구조이기 때문이다.
  - ex. 처음 20개의 메르센 소수를 출력하는 프로그램 (메르센 수는 2의 p승 -1 형태의 수 여기서 p 가 소수이면 해당 메르센 수도 소수일 수 있는데 이때의 수를 메르센 소수라고 한다)
  - 다음 코드는 무한 스트림을 반환하는 메서드다
    ```java
        static Stream<BigInteger> primes() {
            return Stream.iterate(TWO, BigInteger::nextProbablePrime);
        }
    ```
  - 처음 20개의 메르센 소수를 출력하는 프로그램
    ```java
    public static void main(String[] args) {
        primes().map(p -> TWO.(p.intValueExact()).subtract(ONE))
                .filter(mersenne -> mersenne.isProbablePrime(50))
                .limit(20)
                .forEach(System.out::println);
    }
    ```
  - 이제 우리가 각 메르센 소수의 앞에 지수(p)를 출력하길 원한다고 해보자. 이 값은 초기 스트림에만 나타나므로 결과를 출력하는 종단 연산에서는 접근할 수 없다. 하지만 다행히 첫 번째 중간 연산에서 수행한 매핑을 거꾸로 수행해 메르센 수의 지수를 계산해 낼 수 있다.
     ```java
    .forEach(mp -> System.out.println(mp.bitLength() + ": " + mp)); // 지수는 숫자를 이진수로 표현한 다음 몇 비트인지를 세면 나옴
    ```
    
* 스트림과 반복 중 어느 쪽을 써야 할지 바로 알기 어려운 작업도 있다
  - ex. 카드 덱을 초기화하는 작업, 카드는 숫자와 무늬를 묶은 불변 값 클래스이고 숫자와 무늬는 모두 열거 타입이라 하자. 두 집합의 원소들로 만들 수 있는 가능한 모든 조합을 계산하는 문제(데카르트 곱)
  - 데카르트 곱 계산을 반복 방식으로 구현
    ```java
    private static List<Card> newDeck() {
        List<Card> reult = new ArrayList<>();
        for (Suit suit : Suit.values()) 
            for (Rank rank : Rank.values())
                result.add(new Card(suit, rank));
        return result;
    }
    ```
  - 데카르트 곱 계산을 스트림 방식으로 구현
    ```java
    private static List<Card> newDeck() {
       return Stream.of(Suit.values())
                .flatMap(suit -> Stream.of(Rank.values())
                                       .map(rank -> new Card(suit, rank)))
                .collect(toList());
    }
    ```
  - 둘 중 어느 newDeck이 좋아보이는가? 취향차이이다.

