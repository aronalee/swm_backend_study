# 동작 파라미터란

- 함수를 매개변수로 본다는 함수형 프로그래밍에서 발단
- 함수라는 동작을 파라미터로 넘겨 함수를 수행시켜 외부의 함수라는 입력을 동적으로 주어 하나의 메소드로 여러개의 기능을 수행
    - 사과 리스트를 필터링을 한다면 메소드를 작성할 때 구현은 리스트를 순회하며 필터에 걸러지는 리스트만 제공한다. 이때 필터의 역할이 파라미터로 전달하는 함수이다.
- 동적 파라미터를 이용하면 하나의 메소드가 다양한 기능을 수행하여 DRY(Don’t repeat Yourself)원칙을 준수할 수 있다.
    - 리스트의 `[필터]`에 걸러지는 요소를 수행한다.
    - 모든 리스트에 `[조건]` 에 맞는 목록에 `[행동]`을 수행할 수 있다.

## Predicate: 이 함수의 결과는 참과 거짓만 나온다

```jsx
public interface Predicate<T>{
	boolean test(T t);
}
```

Predicate는 수학에서 사용하는 용어로 함수의 수행 결과가 1혹은 0만 나오는 함수를 의미한다. java에서도 여기서 이름을 따와 동일한 역할을 하는 인터페이스가 있다. 이 인터페이스가 `Predicate<T>` 이다.

# 동작 파라미터를 이용한 코드 관리

동적 파라미터를 이용하면 변하는 요구사항에 쉽게 대응할 수 있다. 이유는 동적 파라미터는 ‘행동’을 추상화한다. 즉 전략패턴을 이용한 동작을 추상화하여 한개의 파라미터가 다양한 동작을 할 수 있다.

## 1. 추상화를 통한 동작 파라미터

```java
enum class COLOR={RED,GREEN}

public interface ApplePredicate{
	boolean test(Apple  apple);
}

public class GreenApplePredicate{
	public boolean test(Apple apple){
		return apple.color==COLOR.GREEN
	}
}
 
// Main.java
ApplePredicate predicate=new GreenApplePredicate()
List<Apple> greenApples=filterApple(apples, predicate);

```

Predicate로 추상화를 하여 동작을 만든다. `ApplePredicate` 의 구현체를 여러개를 만들어 서로 다른 종류의 필터를 만드는 전략 패턴으로 여러개의 동작을 하나의 코드로 할 수 있다.

## 2. 익명클래스를 이용한 코드 간소화

```java
enum class COLOR={RED,GREEN}

public interface ApplePredicate{
	boolean test(Apple  apple);
}

 
// Main.java
List<Apple> greenApples=filterApple(apples, new ApplePredicate(){
	public boolean test(Apple apple){
		return apple.color==COLOR.GREEN
	}
});
```

사과 리스트에서 녹색 사과를 필터링하는 곳은 오직 메소드 1개만 사용한다고 가정한다. 이러면 위의 코드는 한번만 사용할 함수를 위해 인터페이스, 구현체, 객체까지 만들었다. 이는 보일러 플레이트코드이므로 만들어지는데 불편한다.

익명클래스를 이용하면 이 문제를 해결할 수 있다. 익명클래스는 오직 한번만 사용되는 객체를 만드는 방법이다. 이를 이용하면 인터페이스의 구현체를 작성할 별도의 클래스와 이를 이용해 만드는 객체를 생성하는 코드가 생략된다.

## 3. 람다식을 이용한 코드 간소화

```java
enum class COLOR={RED,GREEN}

public interface ApplePredicate{
	boolean test(Apple  apple);
}

 
// Main.java
List<Apple> greenApples=filterApple(apples, (Apple apple) ->apple.color==COLOR.GREEN);
```

익명 클래스로 구현체의 작성 부담을 줄여도 코드가 길다. 비슷한 코드가 계속 나오면 `new ApplePredicate()` 라는 익명클래스 정보가 반복적으로 나와 보일러 플레이트 코드가 나오게 된다.

또한 익명클래스는 말 그래도 클래스이므로 클래스 내에 필드를 선언할 수 있다. 이는 코드를 읽는 개발자 입장에서는 불편함을 초래할 수 있다.

java8에서는 람다식을 도입애 이를 해결하였다. 람다식을 통해 클래스, 객체 생성까지 생략하고 오직 Predicate할 내용만 들어가 코드의 길이를 줄여 가독성을 높여 코드의 간결함을 얻었다.

## 4. 제네릭을 통한 추상화

필터기능을 사과뿐만이 아니라 바나나, 수박에도 적용할려고 한다. 바나나, 수박이라는 목록에서 조건에 맞는 것들을 걸러내는 것은 기존의 사과필터와 동일하다. 즉 객체만 다를 뿐 동작은 동일하다. 이 내용을 여러 객체에 만들면 보일러 플레이트 코드가 만들어진다.

이를 제네릭을 통해 추상화를 할 수 있다.

```java
enum class COLOR={RED,GREEN}

public interface Predicate<T>{
	boolean test(T t);
}

 
// Main.java
List<Apple> greenApples=filterApple(apples, (Apple apple) ->apple.color==COLOR.GREEN);
```

제네릭까지 써서 여러 객체에서 사용할 수 있는 유연성까지 얻었다.

# 동작 파라미터를 사용하는 예시

## 1. Comparator<T>

java의 Collection객체는 정렬기능을 제공한다. 이때 정렬하는 기준을 제공해야 하는데 이 정렬 기준이 `Comparator<T>` 이다. 정렬이라는 기능은 모두 같지만 정렬 기준이라는 전략을 추상화하였다.

## 2. Runnable

Runnable은 java에서 병렬 프로그래밍을 위해 Thread를 실행시키는 전략으로 Runnable인터페이스를 통해 어떤 코드를 Thread에서 실행시켜야 하는지를 알려준다. Thread로 실행한다는 내용은 그대로고 Thread내부에서 돌아갈 동작을 추상화를 시켰다.

## 3. Callable<T>

java 5부터 지원하는 ExecutorService인터페이스와 관련된 내용으로서 Ruunable의 업그레이드 버전이다. Thread pool를 이용해 동작할 코드를 제공해준다. 이도 마찬가지로 Runnable과 동일하다.

## 4. GUI 프로그래밍

GUI는 사용자 인터페이스가 많다. 키보드, 마우스 등의 여러개의 입력이 들어온다. 이 동작이 들어오면 어떻게 수행할지를 처리하는 것도 동작 파라미터를 이용한 방법이다. 어떤 이벤트가 들어온다면 이를 알려주고 함수가 실행할 조건이 아니면 넘기는 방식으로 구현할 수 있다.