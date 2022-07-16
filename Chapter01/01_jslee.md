# 1장. 코틀린이란 무엇이며, 왜 필요한가?

# # Kotlin이란?

- Java 플랫폼에서 돌아가는 플랫폼 언어
- 간결, 실용적, Java와의 상호 운용성
    - 기존 Java 라이브러리 및 프레임워크와도 호환
    - Java와 비슷한 수준의 성능

<br>

# # Kotlin의 주요 특성

## 1. Java가 실행되는 모든 플랫폼에 적용

- 일반적으로 Backend, Frontend (Android)에 적용 가능

<br>

## 2. 정적 타입 지정 언어

- Kotlin은 **정적 타입 지정 언어**이다. (Java도 동일)

<br>

<aside>

**정적 타입 지정**
: 객체의 필드나 메서드의 타입을 컴파일러가 ***컴파일타임*** 에 검증

**동적 타입 지정**
: 메서드나 필드 접근에 대한 검증이 ***런타임*** 에 일어남
: 오류가 발생하더라도 컴파일 시점에는 확인 불가

</aside>

<br>

**정적 타입 지정의 장점**

- **성능**
    
    : 런타임에 어떤 메서드를 호출할지 알아내는 과정이 필요 없음 (이미 컴파일타임에 모든게 다 끝나서)
    
    → 메서드 호출이 빠름
    
- **신뢰성**
    
    : 컴파일러가 정확성을 검증 → 오류로 중단할 가능성 적음
    
- **유지 보수성**
    
    : 객체의 타입을 쉽게 추론 가능 → 코드 이해가 쉬움
    
- **도구 지원**
    
    : 안전한 리팩토링, 정확한 코드 완성 기능 제공
    
<br>

+) Kotlin은 **타입 추론**을 지원

- 정적 타입 지정 언어에서 직접 타입을 선언해야 하는 불편함이 사라짐
- Kotlin은 컴파일러가 문맥에 따라 변수의 타입을 자동으로 유추 가능 → 타입 생략 가능 (Java와 다른 점)
  
<br>

## 3. 함수형 프로그래밍 & 객체지향 프로그래밍

- Kotlin은 함수형 프로그래밍과 객체지향 프로그래밍 모두 가능
  
<br>

**함수형 프로그래밍 개념**

- **일급 시민인 함수 (First-class method)**
    
    : 함수를 변수처럼 사용 가능
    
    : 함수를 파라미터처럼 사용 가능
    
    : 함수를 반환값으로 사용 가능
    
- **불변성**
    
    : 생성되면 변하지 않는 불변 객체를 사용하여 작성
    
- **부수효과 없음**
    
    : 동일한 입력에는 동일한 출력을 내놓음
    
    : 다른 객체의 상태를 변경시키지 않음

<br>

**함수형 프로그래밍 장점**

- **간결성**
    
    : 명령형 코드에 비해 간결
    
- **다중 스레드 (Multi-Thread)에 안전**
    
    명령형 프로그래밍에서는 동시성 프로그래밍에서 교착상태 (Deadlock)에 걸릴 위험이 큼
    : Thread간 공유하는 데이터나 상태값이 Mutable하기 때문
    
    [http://ruaa.me/why-functional-matters/](http://ruaa.me/why-functional-matters/)
    

    
    : Thread간 공유하는 데이터가 Immutable하기 때문에 부수효과 없음
    
- **테스트 용이**
    
    : 부수효과 있는 코드는 준비 코드가 필요하지만, 순수 함수는 독립적으로 테스트 가능

<br>

# # Kotlin 응용

## 안드로이드 프로그래밍

- Layout없이 순수 Kotlin으로만 개발 가능 → ex. Kotlin Anko, Jetpack Compose
- Kotlin이 제공하는 타입 추론으로 Null을 정확하게 추적 → NPE 방지
- 기본으로 Java 8과 호환 → 호환성과 관련한 문제 X
- 성능 손해 X → 컴파일 후 생성되는 바이트 코드는 Java 바이트 코드와 동등하게 실행됨

<br>

# # Kotlin 장점

## 1. 실용성

- 이미 다른 언어에서 검증된 방법과 기능에 의존하는 언어
    
    : 러닝커브가 낮음
    
- Java에서 사용해 온 익숙한 프로그래밍 스타일 적용 가능
- IntelliJ Idea와 Kotlin 컴파일러의 개발이 함께 이루어짐
    
    : IDE의 활용성이 좋음

<br>


## 2. 간결성

- 코드에서 부가적인 요소를 많이 줄여 간결하게 만듦
    
    : ex. 게터, 세터, 생성자 파라미터
    
- Kotlin 표준 라이브러리를 통해 반복되거나 길어지는 코드를 라이브러리 함수로 대체 가능
    
    : ex. 람다식
    
<br>

## 3. 안전성

- JVM에서 실행한다는 자체가 이미 안전성을 보장
    
    : 메모리 안전성, 버퍼 오버플로 방지, 동적 메모리 할당으로 인한 이슈 방지
    
- 타입 안정성 지원
    
    : 자동 타입 추론
    
- `NullPointerException` 방지
    
    : 런타임이 아닌 컴파일타임에 확인
    
    ```kotlin
    val a: String? = null  // Nullable
    val b: String = ""     // Non-Null
    ```
    
- `ClassCastException` 방지
    
    : 객체를 다른 타입으로 캐스팅하기 전에 타입을 미리 검사하지 않으면 발생하는 Exception
    
    : 타입 검사와 캐스트가 한 연산자로 이뤄짐 - **is**
    
    ```kotlin
    if (value is String) println(value.toUpperCase())
    ```

<br>    

## 4. 상호운용성

- 기존 Java 메서드 호출, 클래스 상속, 인터페이스 구현, 자바 Annotation 적용 가능
- Kotlin 클래스나 메서드를 Java 클래스나 메서드와 혼용 가능
- 기존 Java 라이브러리를 최대한 활용 가능
    
    : Kotlin은 Java 표준 라이브러리 클래스에 의존
    
    : Kotlin 컬렉션 API의 경우 Java 라이브러리를 확장적으로 사용
    
- 다중 언어 프로젝트 지원
    
    : Java와 Kotlin 파일이 섞여있어도 컴파일 가능
    
<br>

# # Kotlin 코드 컴파일

<img src ="[kotlin-compile-process.png](https://github.com/Developer-book-club/kotlin-in-action-2207/blob/jaesung/img/kotlin-compile-process.png)" width="800">

- Kotlin은 Java와 동일하게 JVM위에 동작하기 때문에 컴파일 과정이 유사

- Kotlin 컴파일러는 .kt 파일을 .class 바이트 코드로 컴파일 함
- 컴파일된 바이트 코드는 Kotlin 런타임 라이브러리에 의존적
- Kotlin 런타임 라이브러리에는 Kotlin 표준 라이브러리와 Java API를 확장한 기능이 포함
    
    : 애플리케이션 배포 시 Kotlin 런타임 라이브러리도 함께 배포해야 함
    
    : Maven, Gradle 사용 시 애플리케이션 패키징 과정에서 Kotlin 런타임 라이브러리를 포함시킴
