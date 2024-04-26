# 📌 인상 깊었던 내용
* 아이템36: 비트 필드 대신 EnumSet을 사용하라
* 아이템37: ordinal 인덱싱 대신 EnumMap을 사용하라
  - 둘다 enum으로 선언하여 정의하고 비트필드 구분이 필요하거나 enum을 인덱싱으로 사용하고자 하는 부분에만 EnumSet, EnumMap을 쓰면 되어서 유연하게 적용이 가능해보인다!

# 📌 이해가 가지 않았던 내용

* 아이템38: 확장할 수 있는 열거 타입이 필요하면 인터페이스를 사용하라
  - 예제에서 interface Operation을 확장한 BasicOperation enum, ExtendedOperation enum 을 예시로 들었는데 BasicOperation 내부에서 확장하면 무슨 문제가 생기는 것인가?

# 📌 논의해보고 싶었던 내용
