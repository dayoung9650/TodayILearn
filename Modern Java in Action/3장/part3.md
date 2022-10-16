# 3장. 람다표현식
## 람다란 무엇인가 ? 

* 메소드를 전달할 수 있는 익명 함수를 단순화한 것
* 람다표현식에는 이름은 없지만 파라미터 리스트, 바디, 반환형식, 발생할 수 있는 예외 리스트 가질 수 있음

### 특징

* 익명
  * 보통의 메서드와 달리 이름이 없어서 익명이라고 함
  * 구현해야 할 코드에 대한 걱정거리가 줄어듦
* 함수
  * 람다는 메소드처럼 특정 클래스에 종속되지 않으므로 함수라고 부름
* 전달
  * 람다 표현식을 메소드 인수로 전달하거나 변수로 저장할 수 있음
* 간결성
  * 익명 클래스처럼 많은 자질구레한 코드를 구현할 필요가 없음

람다 적용 전

```java
Comparator<Apple> byWeight = new Comparator<Apple>(){
  puclic int compare(Apple a1, Apple a2){
    return a1.getWeight().compareTo(a2.getWeigth());
  }
}
```

람다 적용 

* 메소드의 바디를 직접 전달하는 것처럼 코드를 전달할 수 있다.

```java
//표현식 스타일
Comparator<Apple> byWeight = (Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeigth());
//블록 스타일
Comparator<Apple> byWeight = (Apple a1, Apple a2) -> {a1.getWeight().compareTo(a2.getWeigth());};
```

* 파라미터 리스트 
* 화살표: 람다의 파라미터 리스트와 바디를 구분
* 람다 바디 : 람다의 반환값에 해당하는 표현식

## 함수형 인터페이스

* 람다는 함수형 인터페이스에서 사용된다.
* 많은 디폴트 메소드가 있더라도 오직 하나의 추상 메소드가 있으면 함수형 인터페이스
* Predicate<T>, Comparator<T>, Runnable<T> 등
* 람다 표현식으로 함수형 인터페이스의 추상 메소드 구현을 직접 전달할 수 있음
* **전체 표현식을 함수형 인터페이스의 인스턴스로 취급**한다. (함수형 인터페이스를 **구현한** 클래스의 인터페이스)

## 함수 디스크립터

* 람다 표현식의 시그니처를 서술하는 메소드
* 람다 표현식은 변수에 할당하거나 함수형 인터페이스를 인수로 받는 메소드로 전달할 수 있음
* 함수형 인터페이스의 추상 메소드와 같은 시그니처를 갖음
* 한 개의 void 메소드 호출은 중괄호로 감쌀 필요가 없음

```java
public void process(Runnable r){
  r.run();
}
// 인수가 없으며 void 를 반환하는 람다 표현식
process(()-> System.out.println("This is awesome!!"));

```

> @FuntionalInterface : 메소드가 한 개인 함수형인터페이스를 가리키는 어노테이션, 매소드가 두 개 이상이면 에러 발생

## 실행 어라운드 패턴

* 실제 자원을 처리하는 코드를 설정과 정리 두 과정이 둘러싸는 형태를 갖는 코드

AS-IS 

```java
// 자바7에 새로 추가된 try-with-resources, 이를 사용하면 자원을 명시적으로 닫을 필요가 없으므로 간결한 코드 가능하다
public String processFile() throws IOException{
  try (BufferedReader br = new BufferedReader(new FileReader("data.txt"))){
    return br.readLine(); // 실제 필요한 작업을 하는 행
  }
}
```

만일 이 경우, 두줄을 읽거나 자주 사용되는 단어를 반환하려면 어떻게 해야 할까?

함수형 인터페이스와 람다를 사용하면 람다 표현식으로 함수형 인터페이스의 추상 메소드 구현을 직접 전달할 수 있으며 전달된 코드는 함수형 인터페이스의 인스턴스로 전달된 코드와 같은 방식으로 처리한다.

```java
@FunctionalInterface
public interface BufferedReaderProcessor{
  String process(BufferedReader b) throws IOException;
}

public String processFile(BufferedReaderProcessor p) throws IOException{
  try (BufferedReader br = new BufferedReader(new FileReader("data.txt"))){
    return  p.process(br); // 실제 필요한 작업을 하는 행
  }
}

public void main(){
  String online = processFile((BufferedReader br) -> br.readLine()); //한 행 처리
  String towLines = processFile((BufferedReader br) -> br.readLine() + br.readLine()); // 두 행 처리
}
```



## 함수형 인터페이스 사용

* 함수형 인터페이스의 추상 메소드는 람다 표현식의 시그니처를 묘사
* 함수형 인터페이스의 추상 메소드 시그니처를 함수 디스크립터라고 함
* 다양한 람다 표현식을 사용하려면 공통의 함수 디스크립터를 기술하는 함수형 인터헤이스 집합이 필요
* 자바 8 에서는 java.util.function 패키지로 여러 가지 새로운 함수형 인터페이스를 제공



### Predicate

* test 라는 추상메서드를 정의
* T 객체를 파라미터로 받고 boolean 을 반환하는 시그니처

```java 
@FunctionalInterface
public interface Predicate<T>{
  boolean test(T t);
}

public <T> List<T> filter(List<T> list, Predicate<T> p){
  List<T> results = new ArrayList<>();
  for(T t: list){
    if(p.test(t)){
      results.add(t);
    }
  }
  return results;
}

Predicate<String> nonEmptyStringPredicate = (String s) -> !s.isEmpty();
List<String> nonEmpty = filter(listOfStrings, nonEmptyStringPredicate);
```



### Consumer

* T 객체를 받아서 void 를 반환하는 accept 라는 추상 메소드를 정의
* 객체에 어떤 작업을 하고 싶은 경우

```java
@FunctionalInterface
public interface Consumer<T> {
  void accept(T t);
}

public <T> void forEach(List<T> list, Consumer<T> c){
  for(T t : list){
    c.accept(t);
  }
}
forEach(
	Arrays.asList(1,2,3,4,5), (Integer i) -> System.out.println(i)
);
```



### Function

* 제네릭 T 를 인수로 받아서 제네릭 형식 R 객체를 반환하는 추상 메소드 apply 를 정의함
* 입력을 출력으로 매핑하는 람다를 정의할 때 사용

```java
@FunctionalInterface
public interface Function<T, R>{
  R apply(T t);
}

public <T,R> List<R> map(List<T> list, Function<T,R> f){
  List<R> results = new ArrayList<>();
  for (T t : list){
    results.add(f.apply(t))
  }
  return results;
}

List<Integer> l = map(
	Arrays.asList("lambda", "in", "action"),
  (String s) -> s.length()
)
```



### 기본형 특화

* 제네릭 파라미터는 참조형 변수만 사용할 수 있음
* 박싱 : 자바에서는 기본형을 참조형으로 변환하는 기능을 제공함
* 언박싱 : 참조형 -> 기본형 
* 오토박싱 : 자동으로 언박싱, 박싱 이루어짐
  * 박싱한 값은 기본형을 감싸는 래퍼며 힙에 저장
  * 따라서 박싱한 값은 메모리를 더 소비하며 기본형을 가져올 때도 메모리를 탐색하는 과정이 필요
* IntPredicate, doublePredicate 등 형변환을 하지 않는 함수형 인터페이스도 존재함



## 형식 검사

* 람다로 함수형 인터페이스의 인스턴스를 만들 수 있음
* 람다 표현식 자체에는 람다가 어떤 함수형 인터페이스를 구현하는지의 정보가 포함되어 있지 않음
* 람다 형식 검사 방법
  1. 람다가 사용된 컨텍스트는 무엇인가? 
  2. 대상 형식은 무엇인가? - Predicate<Apple>
  3. Predicate<Apple> 인터페이스의 추상 메서드는 무엇인가? - boolean test(Apple apple)
  4. Apple 을 인수로 받아 boolean 을 반환하는 test 메소드
  5. 코드 형식검사 완료
* 람다 표현식이 예외를 던질 수 있다면 추상 메소드도 같은 예외를 던질 수 있도록 throws 로 선언해야 함

### 같은 람다. 다른 함수형 인터페이스

* 대상 형식이라는 특징 때문에 같은 람다 표현식이더라도 호환되는 추상 메소드를 가진 다른 함수형 인터페이스로 사용될 수 있음
* 아래 두 할당문은 모두 유효한 코드
  * Callable<Integer> c = () -> 42;
  * PrivilegedAction<Integer> p = () -> 42;
* 할당문 콘텍스트, 메소드 호출 콘텍스트, 형변환 콘텍스트 등으로 람다 표현식의 형식을 추론할 수 있음

### 형식 추론

* 자바 컴파일러는 람다 표현식이 사용된 콘텍스트 (대상 형식)를 이용하여 람다 표현식과 관련된 함수형 인터페이스를 추론함
* 즉 대상 형식을 이용하여 함수 디스크립터를 알 수 있으므로 컴파일러는 람다의 시그니처도 추론할 수 있음
* 컴파일러는 람다 표현식의 파라미터 형식에 접근할 수 있으므로 람다 문법에서 이를 생략이 가능

```java
Comparaator<Apple> c = (Apple a1, Apple a2) -> a.getWeigth().compareTo(a2.getWeigth());
Comparaator<Apple> c = (a1, a2) -> a.getWeigth().compareTo(a2.getWeigth());
```



### 람다 캡쳐링

* 익명함수처럼 자유변수 ( 파라미터로 넘겨진 변수가 아닌 외부에서 정의된 변수) 사용 가능
* 단, static 혹은 final 변수 및 한 번만 할당된 변수만 가능함
* why?
  * 인스턴스 변수는 힙에 저장, 지역변수는 스택에 저장
  * 람다에서 지역 변수에 바로 접근하게 되면 람다가 스레드에서 실행된다면 변수를 할당한 스레드가 사라져서 변수 할당이 해제 되었는데도 람다를 실행하는 스레드에서는 해당 변수에 접근하려 할 수 있음
  * 따라서 자바 구현에서는 원래 변수에 접근을 허용하는 것이 아니라 자유 지역 변수의 복사본을 제공함
  * 복사본의 값이 바뀌지 않아야 하므로 지역 변수에는 한 번만 값을 할당해야 한다는 제약이 생긴 것





람다 및 익명 클래스는 정의된 메소드의 지역 변수 값을 바꿀 수 없다. 람다가 정의된 메소드의 지역 변수 값은 final 변수여야 한다.



## 메소드 참조

기존의 메소드 정의를 재활용해서 람다처럼 전달할 수 있다.

```java
inventory.sort(comparing(Apple::getWeigth));
```

* 특정 메소드만을 호출하는 람다의 축약형
* 가독성을 높임
* 실제로 메소드를 호출하는 것은 아니므로 ()가 필요없음



1. 정적 메소드 참조
   1. Integer::parseInt
2. 다양한 형식의 인스턴스 메소드 참조
   1. String::length
3. 기존 객체의 인스턴스 메소드 참조
   1. expensiveTransaction::getValue

List에 포함된 문자열을 대소문자 구분하지 않고 정렬하는 프로그램을 구현

```java
List<String> str = Arrays.asList("a","b","c","d","e");
str.sort((s1,s2) -> s1.compareToIgnoreCase(s2));

str.sort(String::compareToIgnoreCase);
```

* 컴파일러는 람다 표현식의 형식을 검사하던 방식과 비슷한 과정으로 메소드 참조가 주어진 함수형 인터페이스와 호환하는지 확인함
* 메소드 참조는 콘텍스트의 형식과 일치해야 함

## 생성자 참조

파라미터 1개

* 파라미터 0개에 1개 객체를 반환하는 Suppler<T> 의 get 메소드 사용하여 Apple 반환

```java
Supplier<Apple> c1 = Apple::new;
Supplier<Apple> c1 = () -> new Apple(); //위와 동일
Apple a1 = c1.get();

```

파라미터 1개 

* Function 의 apply 메소드에 무게를 인수로 호출하여 새로운 Apple 반환

```java
Function<Integer, Apple> c2 = Apple::new;
Apple a2 = c2.apply(110);
```



파라미터 2개 

* 두 인수를 갖는 BiFunction 함수형 인터페이스 사용

```java
BiFunction<Color, Integer, Apple> c3 = Apple::new;
Apple a3 = c3.appl(GREEN, 110);
```

인스턴스화하지 않고도 생성자에 접근할 수 있는 기능을 다양한 상황에 응용할 수 있음

얘를 들어 Mao으로 생성자와 문자열값을 관련시킬 수 있음

String 과 Integer가 인수로 주어졌을 때 다양한 무게를 갖는 여러 종류의 과일을 만드는 giveMeFruit 메소드 만들 수 있음

```java
static Map<String, Function<Integer, Fruit>> map = new HashMap<>();
static {
  map.put("apple", Apple::new);
  map.put("orange", Orange::new);
}

public static Fruit giveMeFuruit(String fruit, Integer weight){
  return map.get(fruit.toLowerCase())
    .apply(weight);
}
```



인수가 4개인 생성자 참조를 하고 싶다면, 생성자 참조와 일치하는 시그니처를 갖는 함수형 인터페이스가 필요함

하지만 인수가 4개인 함수형 인터페이스는 없다. 직접 만들어야 함

```java
public interface TriFunction<T, U, V, R>{
  R apply(T t, U u, V v);
}

TriFunction<Integer, Integer, Integer, Color> colorFactory = Color::new;
```



## 람다, 메소드 참조 활용하기

```java
inventory.sort(comparing(Apple::getWeight));
```



## 람다 표현식을 조합할 수 있는 유용한 메소드

* Comparator, Function, Predicate 같은 함수형 인터페이스는 람다 표현식을 조합할 수 있도록 유틸리티 메소드를 제공함
* 간단한 여러 개의 람다 표현식을 조합해서 복잡한 람다 표현식을 만들 수 있음
* 디폴트 메소드를 사용함

### Comparrator 조합

역정렬

```java
inventory.sort(comparing(Apple::getWeight).reversed());
```

Comparator 연결

만약 두번째 정렬 조건을 주고 싶다면 ? 

```java
inventory.sort(comparing(Apple::getWeight)
               .reversed()
               .thenComparing(Apple::getCountry)
              );
```

### Predicate 조합

* negate
  * 특정 프레디케이트를 반전 시킴

```java
Predicate<Apple> notRedApple = redApple.negate();
```

* And

```java
Predicate<Apple> notRedApple = redApple.and(apple -> apple.getWeigth() > 150);
```

* or

```java
Predicate<Apple> notRedApple = redApple
	.and(apple -> apple.getWeigth() > 150);
	.or(apple -> GREEN.equals(a.getColor()));
```



### Function 조합

* andThen 
  * 주어진 함수를 먼저 적용한 결과를 다른 함수의 입력으로 전달하는 함수를 반환

```java
Function<Integer, Integer> f = x -> x+ 1;
Function<Integer, Integer> g = x -> x * 2;
Function<Integer, Integer> h = f.andThen(g);
int result = h.apply(1); //4
```

* compose
  * 인수로 주어진 함수를 먼저 실행한 다음 그 결과를 외부 함수의 인수로 제공

```java
Function<Integer, Integer> f = x -> x+ 1;
Function<Integer, Integer> g = x -> x * 2;
Function<Integer, Integer> h = f.compose(g);
int result = h.apply(1); //3
```

