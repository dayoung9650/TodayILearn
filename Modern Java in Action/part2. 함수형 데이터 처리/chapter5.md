# 스트림 활용

데이터를 어떻게 처리할지는 스트림 API 가 관리하므로 편리하게 데이터 관련 작업을 할 수 있음

스트림 API 내부적으로 다양한 최적화가 이루어져 있음

## 프레디케이트 필터링

* filter 메소드는 프레디케이트를 인수로 받아서 프레디케이트와 일치하는 모든 요소를 포함하는 스트림을 반환함

```java
List<Dish> vegetrianMenu = menu.stream()
  														.filter(Dish::isVegegetarian) // 프레디케이트 필터링
  														.collect(toList());
```



## 고유 요소 필터링

* distinct 메소드 지원

```java
List<Dish> vegetrianMenu = menu.stream()
  														.filter(Dish::isVegegetarian)
  														.distinct() //고유요소 필터링
  														.collect(toList());
```



## 스트림 슬라이싱

### takeWhile, dropWhile

* 자바 9
* filter 연산은 전체 스트림을 반복하면서 각 요소에 프레디케이트를 적용한다
* 리스트가 이미 정렬되어 있다면 320 칼로리보다 작은 요리가 나왔을 때 반복 작업 중단할 수 있음

```java
//320보다 작은 칼로리 탐색
List<Dish> sliceMenu1 = sperialMenu.stream()
  																	.takeWhile(dish -> dish.getCalories() < 320)
  																	.collect(toList());

//320보다 큰 칼로리 탐색
List<Dish> sliceMenu1 = sperialMenu.stream()
  																	.dropWhile(dish -> dish.getCalories() < 320)
  																	.collect(toList());
```



### Limit

```java
List<Dish> vegetrianMenu = menu.stream()
  														.filter(Dish::isVegegetarian)
  														.limit(3) //스트림 축소
  														.collect(toList());
```



### skip

```java
List<Dish> vegetrianMenu = menu.stream()
  														.filter(Dish::isVegegetarian)
  														.skip(2) //요소 건너뛰기
  														.collect(toList());
```

## 매핑

* 특정 데이터를 선택하는 연산
* 예를 들어 SQL  테이블에서 특정 열만 선택할 수 있음
* 인수로 제공된 함수는 각 요소에 적용되며 함수를 적용한 결과가 새로운 요소로 매핑됨
* **변환, 매핑**

### map

리스트의 각 단어의 길이를 반환하는 방법

```java
List<Integer> dishNameLengths = menu.stream()
  																	.map(Dish::getName)
  																	.map(String::length)
  																	.collect(toList());
```

리스트에서 고유 문자로 이루어진 리스트 반환하고자 한다면 ??

아래 예제는 map에서 Stream<String[]> 를 반환함 (x)

```java
words.stream()
  		.map(word -> word.split()) //map으로 전달한 람다는 각 단어의 String[]을 반환함 
  		.distinct()
  		.collect(toList()); // 배열이 반환됨.. 
```

배열 스트림 대신 문자열 스트림이 필요함

```java
String[] arrayOfwords = {"aaaa", "bbbbb"}
Stream<String> streamOfwords = Arrays.stream(arrayOfWords) // array를 스트림으로 만들어주는 메소드

words.stream()
  		.map(word -> word.split("")) //각 단어를 개별 문자열로 배열로 반환
  		.map(Arrays:stream) //각 배열을 별도의 스트림으로 생성
  		.distinct()
  		.collect(toList()) //List<Stream<String>> 이 만들어지며 문제 해결 X
```



### flatMap

스트림의 각 값을 다른 스트림으로 만든 다음에 모든 스트림을 하나의 스트림으로 연결함

```java
List<String> uniqueCharacters = words.stream()
  																		.map(word -> word.split()) //각 단어를 개별 문자를 포함하는 배열로 변환
																			.flatMap(Arrays::stream) //생성된 스트림을 하나의 스트림으로 평탄화
  																		.distinct()
  																		.collect(toList());
 
```

## 검색과 매칭

### anymatch

적어도 한 요소와 일치하는지 확인

```java
if(menu.stream().anyMatch(Dish::isVegetarian)){
  System.out.println("The menu is (someWhat) vegetarian firendly!")
}
```



### allMatch

모든 요소와 일치하는지 확인

```java
boolean isVege= menu.stream().allMatch(Dish::isVegetarian)
```



### noneMatch

일치하는 요소가 없는지 확인

```java
boolean isHealthy= menu.stream().noneMatch(d -> d.getCalories() >= 3000)
```



### 쇼트셔킷

* 위 세 메소드는 스트림 쇼트서킷 기법 즉 자**바의 &&, ||,** 와 같은 연산을 사용함
* 표현식에 하나라도 거짓이라는 결과가 나오면 나머지 표현식의 결과와 상관없이 전체 결과도 거짓이 된다
* **모든 요소를 처리하지 않고도 결과를 반환할 수 있음**
* limit, anyMatch, allMatch, noneMatch

### findAny

현재 스트림에서 임의의 요소를 반환함

findAny는 아무 요소도 반환하지 않을 수 있으므로 optional 사용

```java
Optional<Dish> dish = 
  menu.stream()
  		.filter(Dish::isVegetarian)
  		.findAny(); 
```

### findFirst

가장 첫 요소 찾음

병렬 실행에서는 첫 번째 요소를 찾기 어려우므로 반환 순서가 상관없다면 병렬 스트림에서는 제약이 적은 findAny를 사용함

```java
Optional<Dish> dish = 
  menu.stream()
  		.filter(Dish::isVegetarian)
  		.findFirst(); 
```

### Optional 

* java.util.optional
* 값의 존재나 부재 여부를 표현하는 컨테이너 
* null 버그를 피하기 위함

```
Optional<Dish> dish = 
  menu.stream()
  		.filter(Dish::isVegetarian)
  		.findAny()
  		.ifPresent(dish -> System.out.println(dish.getName()));
```



## 리듀싱

* 메뉴의 모든 칼로리의 합계를 구하시오 , 메뉴에서 가장 칼로리가 높은 요리는 ? 같이 스트림 요소를 조합하여 더 복잡한 질의 표현 방법을 설명함

* Integer과 같은 결과가 나올 때까지 스트림의 모든 요서를 반복적으로 처리해야 함 -> **리듀싱 연산**

* **모든 스트림 요소를 처리해서 값으로 도출**

* 새로운 값을 이용해서 스트림의 모든 요소를 소비할 떄까지 람다를 반복 수행함

* **폴드**

* 두개의 인수를 가짐

  * 초기값 0
  * 두 요소를 조합해서 새로운 값을 만드는 BinaryOperator<t> 

* 초기값 없음

  * 초깃값을 받지 않도록 오버로드된 reduce 도 있음
  * 스트림의 아무 요소가 없는 경우를 대비해 optional로 반환함

  ```java
  Optional<Integer> sum = numbers.stream().reduce((a,b) -> a + b);
  ```

### sum

```java
int sum = numbers.stream().reduce(0, Integer::sum)
```

### max

```java
Optional<Integer> sum = numbers.stream().reduce(Integer::max);
```

### min

```java
Optional<Integer> sum = numbers.stream().reduce(Integer::min);
```

### 리스트 내 개수 세기

아래 두 문장은 동일 결과

```java
int count = menu.stream().map(d -> 1).reduce(0, (a,b) -> a + b);

int count = menu.stream().count();
```

### 리듀스 메소드의 장점과 병렬화

* 리듀스를 이용하면 내부 반복이 추상화되면서 내부 구현에서 병렬로 reduce 를 실행할 수 있게 됨
* 반복적인 합계에서는 sum 변수를 공유해야 하므로 쉽게 병렬화하기 어려움

### 스트림 연산: 상태 없음과 상태 있음

* steam 메소드를 parallelStream으로 바꾸는 것만으로도 별다른 노력 없이 병렬성을 얻을 수 있음
* map, filter 등은 입력 스트림에서 각 요소를 받아 0 또는 결과를 출력 스트림으로 보냄
* 따라서 이들은 보통 상태가 없는, 즉 내부 상태를 갖지 않는 연산이다
* reduce, sum, max 같은 연산은 결과를 누적할 내부 상태가 필요함
* 스트림에서 처리하는 요소 수와 관계없이 내부 상태의 크기는 **한정**되어 있음
* 예를 들어 어떤 요소를 출력 스트림으로 추가하려면 **모든 요소가 버퍼에 추가되어 있어야 함**
* 따라서 데이터 스트림의 크기가 크거나 무한이라면 문제가 생길 수 있음 -> **내부 상태를 갖는 연산**



### 알파벳 순으로 정렬

```java
String traderStr =
  transactions.stream()
  						.map(transaction -> transaction.getTrader().getName())
  						.distinct()
  						.sorted()
  						.reduce("", (n1, n2) -> n1 + n2);
```



## 숫자형 스트림

* 박싱 비용을 줄이기 위함
* 스트림 API 숫자 스트림을 효율적으로 처리할 수 있도록 **기본형 특화 스트림**을 제공함

### 기본형 특화 스트림

* 박싱 비용을 피할 수 있도록 IntStream , DoubleStream 등을 제공함
* 가각의 인터페이스는 숫자 스트림의 합계를 계산하는 sum, max 등 숫자 관련 리듀싱 연산 메소드 제공
* 오직 박싱 과정에서 일어나는 효율성과 연관있음

### mapToInt

* max, min, sum 등을 지원

```java
int calories = menu.stream() // Stream<Dish> 반환
  									.mapToInt(Dish::getCalories) // IntStream 반환
  									.sum() ; // 비어있으면 0을 반환
```



### 객체 스트림으로 복원하기

```java
IntStream intStream = menu.stream().mapToInt(Dish::getCalories); //스트림을 숫자 스트림으로 변환

Stream<Integer> Stream = intStream.boxed(); //숫자스트림을 스트림으로 변환
```



### 기본값 OpionalInt

* 스트림에 요소가 없는 상황과 실제 최댓값이 0인 상황을 구분하기 어려움
* optional 특화 스트림 버전 제공
  * OptionalInt, OptionalDouble, OptionalLong

```java
OptionalInt maxCalories = menu.stream()
  														.mapToInt(Dish::getCalories)
  														.max();
int max = maxCalories.orElse(1); // 값이 없을 대 기본 최대값을 명시적으로 설정
```



### 숫자 범위

### rangeClosed

* 첫번째 인수로 시작값, 두번째 인수로 종료값을 받음
* 시작값, 종료값 포함

```java
IntStream numbers = IntStream.rangeClosed(1,100).filter(n -> n % 2 == 0);
```

### range

* 첫번째 인수로 시작값, 두번째 인수로 종료값을 받음
* 시작값, 종료값 미포함



## 스트림 만들기

* 컬렉션, 숫자 범위 외에도 다양한 방식으로 스트림을 만들 수 있음

### 값으로 스트림 만들기

* Stream.of()
* Stream.empty()

```java
Stream<String> stream = Stream.of("Mo", "Java", "in", "action");
stream.map(String::toUpperCase).forEach(System.out::println);

Stream<String> emptyStream = Stream.empty();
```



### null 이 될 수 있는 객체로 스트림 만들기

* 자바 9 추가
* Stream.ofNullable

AS-IS

```java
String homeValue = System.getProperty("home");
Stream<String> homeValueStream = homeValue == null ? Stream.empty() : Stream.of(value);
```

TO-BE

```java
Stream<String> homeValueStream = Stream.ofNullable(System.getProperty("home"));
```

null 이 될 수 있는 객체를 포함하는 스트림값을 flatMap 과 함께 사용하는 상황에서 유용

```java
Stream<String> values = Stream.of("config" , "home", "user")
  														.flatMap(key -> Stream.ofNullable(System.out.println(key)));
```

​		

### 배열로 스트림 만들기

