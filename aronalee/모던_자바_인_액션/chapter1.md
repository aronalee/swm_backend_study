# java8

자바 8에서 멀티 코어 병렬성이 강화되었다. 

# 이급 시민을 일급 시민으로

프로그래밍의 목적은 값을 바꾸는 것이다. 이러한 바꿀 수 있는 값들을 일급 시민이라고 불린다.
그럼 값을 제외한 메소드, 클래스들은 무엇인가, 이들은 이급 시민이라고 불린다. 전통적인 메소드와 클래스들은 값을 바꿀 수 없기 때문이다.
java8에서는 이러한 이급 시민들을 일급 시민으로 바꿔주는 기능들을 제공한다.

- 메소드 참조: 이 메소드를 값으로 사용해라
  - `File::isHidden`
  - 이 메소드를 참조해서 가져가서 써라
- 람다(익명 함수): 함수를 값으로 표현하는 기능
- 
# 스트림이 추가되면서

- 스트림: 한번에 한개씩 만들어지는 연속된 데이터의 모임
- 기능을 DB쿼리처럼 추상화를 시켰다.
- 스트림을 이용하면 멀티코어 아키텍처에서 병렬성을 쉽게 활용할 수 있다.