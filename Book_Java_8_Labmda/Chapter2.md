# 2. Lambda Expression

자바 8의 가장 큰 변화 `Lambda Expression`의 도입

## 람다식 구현하기

`Swing`은 자바 GUI 라이브러리

이벤트 리스너를 등록하는 보편적인 방식을 사용한다

``` Java
button.addActionListner(new ActionListner() {
    public void actionPerformed(ActionEvent event) {
        System.out.println("button clicked");
    }
});
```

익명 내부 클래스는 코드를 데이터처럼 간단하게 넘겨주기 위해 고안됨.

하지만 쉽지 않다.

관용구 코드가 추가되는 단점이 있다.

또한 개발자의 의도가 드러나지 않는다.

자바 8에서는 개선된다.

``` Java
button.addActionListner(event -> System.out.println("button clicked"));
```

해당 타입을 정확하게 명시하진 않지만, 정상적으로 컴파일 된다.

내부적으로 코드 흐름을 통해 `event` 변수의 타입을 추론한다.

## 람다 제대로 활용하기

``` Java
Runnable noArguments = () -> System.out.println("Hello World");
// 인자가 없는 람다식으로 ()로 인자가 없음을 나타낸다

ActionListner oneArgument = event -> System.out.println("button clicked");
// 빈 괄호가 제거된 하나의 인자를 가진 람다식

Runnable multiStatement = () -> {
    System.out.print("Hello ");
    System.out.println("World");
};
// {} 둘러싸인 하나의 큰 코드 블럭으로 일반 함수와 동일한 규칙을 따른다.
// 물론 1줄짜리 람다식도 중괄호를 사용해 시작과 끝을 명시할 수 있음

BinaryOperator<Long> add = (x, y) -> x + y;
// 하나 이상의 인자를 가진 메소드를 표현한다.

BinaryOperator<Long> addExplict = (Long x, Long y) -> x + y;
// 때로는 타입을 명시할 수 있다.
```

## 값의 사용

익명 내부 클래스를 사용할 때 클래스 안의 메소드에서 메소드 밖의 변수를 사용하려면

`final` 변수를 사용해야 한다.
    
`final` 변수는 값을 재할당 할 수 없다는 의미.

``` Java
final String name = getUserName();
button.addActionListner(new ActionListner() {
    public void actionPerformed(ActionEvent event) {
        System.out.println("hi " + name);
    }
});
```

자바 8에서는 이러한 제약이 줄어 `final`이 아닌 변수를 참조하는 것이 가능.

하지만, 여전히 `final`로 여겨져 사용된다.

람다식 안에서 사용된다면 내부적으로 `final` 변수로 바꾼다.

만약에 아니라면 컴파일 에러가 발생한다.

람다식은 변수를 캡쳐하는 것이 아니라, 값을 캡쳐하는 것이라 실질적으로 `final`이 되게 한다.

``` Java
final String name = getUserName();
button.addActionListner(event -> System.out.println("hi " + name));
```

`final` 키워드가 코드 이해를 방해한다고도 하지만, 명확한 `final`이 더 이해하기 쉽게 한다.

`final` 기능을 효과적으로 사용하는 것은 개발자의 선택

변수에 여러 번 값을 할당하고, 이 변수를 람다식에 사용하면 컴파일 에러가 발생.

``` Java
String name = getUserName();
name = formatUserName(name);
button.addActionListner(event -> System.out.println("hi " + name));
// local variables refrenced from a lambda expression must be final or effectively final
```

일각에선 람다식을 클로져로 부르지만, 어떻든간에 람다식은 정적으로 분류된다.

## 함수형 인터페이스

자바의 모든 파라미터는 타입이 있다.

그렇다면 람다식은??

하나의 메소드를 가진 인터페이스를 사용해, 메소드를 표현하고 사용하는 익숙한 패턴이있다.

이를 **함수형 인터페이스**라고 한다.

``` Java
public interface ActionListner extends EventListner {
    public void actionPerformed(ActionEvent event);
}
```

하나의 인자를 받고 반환하지 않는 `actionPerformed()`

인터페이스 안에 정의되어 추상화를 위한 `abstract`가 필요없고, 부모를 가진다.

이런 형태가 **함수형 인터페이스**

인터페이스의 메소드가 어떤 네이밍이든 상관없이, 특징이 있다면 람다식과 조화를 이룬다.

함수형 인터페이스는 사용하려는 파라미터의 타입을 설정할 수 있고,

이는 코드의 목적을 쉽게 알고, 가독성을 높여준다.

|인터페이스 이름|인자|반환|예제|
|:---|:---|:---|:---|
|`Predicate<T>`|T|boolean|이 앨범이 출시되었는가?|
|`Consumer<T>`|T|void|값 출력하기|
|`Function<T,R>`|T|R|객체에서 값 얻기|
|`Supplier<T>`|None|T|factory 메소드|
|`UnaryOperator<T>`|T|T|논리기호 !|
|`BinaryOperator<T>`|(T,T)|T|두수의 곱|

### 타입 인터페이스

타입을 직접 명시해야 하는 경우는 언제 일까?

람다에서의 타입 추론은 자바 7에 도입된 타입 추론의 확장

컴파일러가 일반화된 인자를 추측하게 하는 `<>` 연산자

``` Java
// 명시적 선언
Map<String, Integer> oldWordCounts = new HashMap<String, Integer>();
// 다이아몬드(<>) 연산자만 사용
Map<String, Integer> diamondWordCounts = new HashMap<>();
```

컴파일러가 알아서 어떤 타입인지 찾아낸다

생성자를 메소드 호출 구문에 직접 사용한다면

컴파일러는 메소드에 선언된 일반적 타입으로 생성자의 타입을 추론한다.

``` Java
useHashMap(new HashMap<>());
...
private void useHashMap(Map<String, String> values);
```

자바 7에서 생성자를 위한 일반적 타입 선언을 하지 않는 것처럼

자바 8에서도 모든 람다식의 파라미터 타입을 명시하지 않아도 된다.

``` Java
Predicate<Integer> atLeast5 = x -> x > 5;
```

하나의 람다식이긴 하지만, 반환값이 존재한다.

`x > 5`는 람다식의 몸체로 쓰이고, 반환값은 몸체에서 계산된 값이다.

`Predicate`는 일반적인 타입을 가지는 인터페이스로, 예제에서는 `Integer`를 갖는다.

이런 `Predicate`는 오직 `Integer`만을 받는다.

``` Java
public interface Predicate<T> {
    boolean test(T t);
}
```

객체로부터 `boolean` 값을 만드는 `Predicate` 인터페이스

``` Java
BinaryOperator<Long> addLongs = (x, y) -> x + y;
```

컴파일러의 타입 추론은 정확하지만, 충분한 정보가 없다면, 올바른 타입을 이끌어내지 못한다.

``` Java
BinaryOperator add = (x, y) -> x + y;
```

위 코드는 `Operator \"dasda\" cannot be applied to java.lang.Object`라는 메세지가 발생한다.

`BinaryOperator`는 일반적 타입의 인자를 가진 함수형 인터페이스

인자는 두 개의 `x, y`뿐 아니라 반환값의 타입으로 사용된다.

하지만 아래 코드의 경우 가장 기본인 `Obejct`라고 인식하는데,

결국 해당 연산을 수행할 수 없음을 나타낸다.

## 핵심 포인트

* 람다식은 데이터처럼 넘기기 위해 사용되는 이름 없는 메소드

* 람다식은 다음과 같은 형태이다
    
    `BinaryOperator<Integer> add = (x, y) -> x + y;`

* 함수형 인터페이스는 람다식의 타입처럼 사용되는 하나의 추상 메소드를 가진 인터페이스.

