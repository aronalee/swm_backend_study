# 필터링

## Predicate를 이용한 필터링

- `filter*(Predicate<T>)`: Predicate를 이용한 필터링

```java
List<Dish> vegetarianMenu=menu.stream()
    .filter(Dish::isVegetarian)
    .collect(toList());
```

## distinct(): 고유 요소를 통한 필터링

- `distinct()` : 중복된 요소를 뺘고 필터링

```java
List<Dish> dishes=menu.stream()
    .filter(Dish::isVegetarian)
    .distinct()
    .collect(toList());
```

## tajeWhile, dropWhile: 스트림 슬라이싱으로 요소 제한

값이 정렬이 되어있을 때 탐색의 범위를 제한시킬 수 있다. 이를 통해 무한의 범위를 가진 스트림을 유한의 범위로 제한시킬 수 있다.

- `takewhile(Predicate<T>)`: 조건이 false가 될 때까지 요소를 반환
- `dropwhile(Predicate<T>)`: 조건이 false가 될 때까지 요소를 버림

```java
List<Dish> slicedMenu1=menu.stream()
    .takeWhile(dish->dish.getCalories()< 320)
    .collect(toList());
```

```java
List<Dish> slicedMenu2 = menu.stream()
    .dropWhile(dish -> dish.getCalories() < 320)
    .collect(toList());
```

## limit(n): 스트림 축소

- limit(n): n개의 요소를 받으면 스트림을 닫는다.

```java 
List<Dish> dishes=menu.stream()
    .filter(dish->dish.getCalories()>300)
    .limit(3)
    .collect(toList());
```

## skip(n) : 일부 요소를 제외

- skip(n): n개의 요소를 건너뛴다.

```java
List<Dish> dishes=menu.stream()
    .filter(dish->dish.getCalories()>300)
    .skip(2)
    .collect(toList());
```

# 매핑

Collection을 반복할 때 값을 변환하여 다른 객체로 매핑하는 일들이 필요하다. 스트림으로는 아래의 요소들로 매핑들을 처리할 수 있다.

## map(): 람다식을 이용한 매핑

- `map(Function<T,R>)`: T타입의 요소를 R타입으로 반환
- 변환에 가까운 매핑 방법

```java
List<String> dishNames=menu.stream()
    .map(Dish::getName)
    .collect(toList());
```

## flatMap(): 스트림의 평면화

- `flatMap(Function<T,Stream<R>>`: T타입의 요소를 받안 R타입의 스트림으로 반환
- 요소 내의 요소를 스트림에 추가
  - ex) 객체 내의 리스트 요소가 있으면 이를 스트림으로 추가
- `Arrays.Stream()` 같이 스트림으로 바꿔주는 함수를 이용

```java
List<String> words=Arrays.asList("Hello","World");
words.stream()
    .map(word->word.split(""))
    .flatMap(Arrays::stream) // h,e,l,l,o,w,o,r,l,d 로 평면화
    .distinct()
    .collect(toList());
```

# 검색과 매칭

## 매칭된 결과를 반환

- `allMatch(Predicate<T>)`: 모든 요소가 Predicate를 만족하는지 검사
- `anyMatch(Predicate<T>)`: 하나라도 Predicate를 만족하는지 검사
- `noneMatch(Predicate<T>)`: 모든 요소가 Predicate를 만족하지 않는지 검사
- `findAny()`: 스트림에서 임의의 요소를 반환. => 병렬로 스트림을 처리하는 순서를 고려하지 않음
- `findFirst()`: 스트림에서 첫 번째 요소를 반환 => 병렬로 스트림을 처리하는 순서를 고려

```java
if(menu.stream().anyMatch(Dish::isVegetarian)){
    System.out.println("vegetarian!!");
}
```

# 리듀싱(reducing)

- 리듀싱 연산: 스트림의 모든 요소를 반복리하여 값을 도출하는 최종 연산
  - 하나의 작은 조각(결과)가 나올때까지 접어가는 연산이라 폴드(fold)라고도 불리움
- 리듀스 연산은 스트림의 지원하는 연산이므로 병렬처리가 가능하다

## 연산의 종류

- `reduce(BinaryOperator<T>)`: 스트림의 요소를 하나씩 줄여가며 연산을 수행
- `reduce(T identity, BinaryOperator<T>)`: 스트림의 요소를 하나씩 줄여가며 연산을 수행. 초기값을 지정할 수 있음
- `reduce(U identity, BiFunction<U,? super T,U> accumulator, BinaryOperator<U> combiner)`: 스트림의 요소를 하나씩 줄여가며 연산을 수행. 초기값을 지정할 수 있음. 병렬로 처리할 때 사용

```java
// 람다식을 이용한 연산
int sum=numbers.stream().reduce(0,(a,b)->a+b);
// 메소드 추상화를 통한 연산
int sum=numbers.stream().reduce(0,Integer::sum);
// 초기값이 없으면 Optional객체로 반환
Optional<Integer> max=numbers.stream().reduce(Integer::max);
```

## 연산의 한계

- 스트림의 요소 수는 무한대이지만 double, int 같은 기본 자료형을 사용하기에 계산가능한 값의 한계가 존재
- sorted, distinct 이전, 이후의 요소들을 모두 알아야 계산하기에 이 연산은 요소들을 모두 버퍼에 쌓고 계산하기에 무한대의 요소일경우 계산이 불가능함

# 숫자형 스트림

- 스트림의 요소가 숫자일 경우에는 기본형 스트림을 이용하는 것이 효율적
- 박싱과 언박싱을 하지 않기에 효율적이
- 종류: `IntStream`, `DoubleStream`, `LongStream`이 있다.
- `mapToInt`, `mapToDouble`, `mapToLong`을 이용하여 스트림을 기본형 스트림으로 변환 가능

# 기본형 특화 스트림

- 일반적으로 기본형은 초기값들이 존재한다.
  - 만약 계산 결과가 초기값과 같을 경우 정확히 계산한 여부를 어떻게 판단할 것인가
  - 값이 null이라면 어떻게 처리할 것인가
  - 기본형 특화 스트림
- `OptionalInt`, `OptionalDouble`, `OptionalDouble`를 통해 최대값이 없는 상황에 어떤 값을 줄지 명시할 수 있다.


# 스트림 만들기

- `Stream.of(T...)`: 가변인수를 이용하여 스트림 생성
- `Stream.empty()`: 빈 스트림 생성
- `Stream.ofNullable(T)`: null이 아니면 스트림 생성
- `Stream.iterate(T, UnaryOperator<T>)`: 초기값과 람다식을 이용하여 스트림 생성 => 무한대의 스트림 생성 가능
  - 일반적으로는 `limit()`을 이용하여 범위를 제한
- `Stream.generate(Supplier<T>)`: Supplier를 이용하여 스트림 생성
  - Math::random 을 이용하여 무제한 랜덤 값들을 제공이 가능
  - 왜 사용하는가? => 상태가 없는 스트림을 만들 수 있다.