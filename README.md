# AOP 소개 - 애스펙트
### 핵심 기능과 부가 기능을 분리
누군가는 이러한 부가 기능 도입의 문제점들을 해결하기 위해 오랜기간 고민해왔다.
그 결과 부가 기능을 핵심 기능에서 분리하고 한 곳에서 관리하도록 했다. 그리고 해당 부가 기능을 어디에 적용할지 선택하는 기능도 만들었다.  
이렇게 부가 기능과 부가 기능을 어디에 적용할지 선택하는 기능을 합해서 하나의 모듈로 만들었는데 이것이 바로 애스펙트(aspect)이다.   
애스펙트는 쉽게 이야기해서 부가 기능과, 해당 부가 기능을 어디에 적용할지 정의한 것이다.  
예를 들어서 로그 출력 기능을 모든 컨트롤러에 적용해라 라는 것이 정의되어 있다.  

### AspectJ 프레임워크
AOP의 대표적인 구현으로 AspectJ 프레임워크(https://www.eclipse.org/aspectj/)가 있다. 물론 스프링도
AOP를 지원하지만 대부분 AspectJ의 문법을 차용하고, AspectJ가 제공하는 기능의 일부만 제공한다.

AspectJ 프레임워크는 스스로를 다음과 같이 설명한다.
* 자바 프로그래밍 언어에 대한 완벽한 관점 지향 확장
* 횡단 관심사의 깔끔한 모듈화
    * 오류 검사 및 처리
    * 동기화
    * 성능 최적화(캐싱)
    * 모니터링 및 로깅

## AOP 적용 방식
AOP를 사용하면 핵심 기능과 부가 기능이 코드상 완전히 분리되어서 관리된다.
그렇다면 AOP를 사용할 때 부가 기능 로직은 어떤 방식으로 실제 로직에 추가될 수 있을까?

### 크게 3가지 방법이 있다.
* 컴파일 시점: 실제 대상 코드에 애스팩트를 통한 부가 기능 호출 코드가 포함된다. AspectJ를 직접 사용해야 한다.
* 클래스 로딩 시점: 실제 대상 코드에 애스팩트를 통한 부가 기능 호출 코드가 포함된다. AspectJ를 직접 사용해야한다.
* 런타임 시점(프록시) : 실제 대상 코드는 그대로 유지된다. 대신에 프록시를 통해 부가 기능이 적용된다. 
  따라서 항상 프록시를 통해야 부가 기능을 사용할 수 있다. 스프링 AOP는 이 방식을 사용한다.  

### AOP 적용 위치
AOP는 지금까지 학습한 메서드 실행 위치 뿐만 아니라 다음과 같은 다양한 위치에 적용할 수 있다.
* 적용 가능 지점(조인 포인트): 생성자, 필드 값 접근, static 메서드 접근, 메서드 실행
    * 이렇게 AOP를 적용할 수 있는 지점을 조인 포인트(Join point)라 한다.
* AspectJ를 사용해서 컴파일 시점과 클래스 로딩 시점에 적용하는 AOP는 바이트코드를 실제 조작하기 때문에 해당 기능을 모든 지점에 다 적용할 수 있다.
* 프록시 방식을 사용하는 스프링 AOP는 메서드 실행 지점에만 AOP를 적용할 수 있다.
    * 프록시는 메서드 오버라이딩 개념으로 동작한다. 따라서 생성자나 static 메서드, 필드 값 접근에는 프록시개념이 적용될 수 없다.
    * 프록시를 사용하는 스프링 AOP의 조인 포인트는 메서드 실행으로 제한된다.
* 프록시 방식을 사용하는 스프링 AOP는 스프링 컨테이너가 관리할 수 있는 스프링 빈에만 AOP를 적용할 수 있다.

## AOP 용어 정리
* 조인 포인트(Join point)
  * 어드바이스가 적용될 수 있는 위치, 메소드 실행, 생성자 호출, 필드 값 접근, static 메서드 접근 같은 프로그램 실행 중 지점
  * 조인 포인트는 추상적인 개념이다. AOP를 적용할 수 있는 모든 지점이라 생각하면 된다.
  * 스프링 AOP는 프록시 방식을 사용하므로 조인 포인트는 항상 메소드 실행 지점으로 제한된다.
* 포인트컷(Pointcut)
  * 조인 포인트 중에서 어드바이스가 적용될 위치를 선별하는 기능
  * 주로 AspectJ 표현식을 사용해서 지정
  * 프록시를 사용하는 스프링 AOP는 메서드 실행 지점만 포인트컷으로 선별 가능
* 타켓(Target)
  * 어드바이스를 받는 객체, 포인트컷으로 결정
* 어드바이스(Advice)
  * 부가 기능
  * 특정 조인 포인트에서 Aspect에 의해 취해지는 조치
  * Around(주변), Before(전), After(후)와 같은 다양한 종류의 어드바이스가 있음
* 애스펙트(Aspect)
  * 어드바이스 + 포인트컷을 모듈화 한 것
  * @Aspect 를 생각하면 됨
  * 여러 어드바이스와 포인트 컷이 함께 존재
* 어드바이저(Advisor)
  * 하나의 어드바이스와 하나의 포인트 컷으로 구성
  * 스프링 AOP에서만 사용되는 특별한 용어
* 위빙(Weaving)
  * 포인트컷으로 결정한 타켓의 조인 포인트에 어드바이스를 적용하는 것
  * 위빙을 통해 핵심 기능 코드에 영향을 주지 않고 부가 기능을 추가 할 수 있음
  * AOP 적용을 위해 애스펙트를 객체에 연결한 상태
* 컴파일 타임(AspectJ compiler)
  * 로드 타임
  * 런타임, 스프링 AOP는 런타임, 프록시 방식
* AOP 프록시
  * AOP 기능을 구현하기 위해 만든 프록시 객체, 스프링에서 AOP 프록시는 JDK 동적 프록시 또는 CGLIB 프록시이다.

# 스프링 AOP
## 어드바이스 종류
* @Around : 메서드 호출 전후에 수행, 가장 강력한 어드바이스, 조인 포인트 실행 여부 선택, 반환 값 변환, 예외 변환 등이 가능
* @Before : 조인 포인트 실행 이전에 실행
* @AfterReturning : 조인 포인트가 정상 완료후 실행
* @AfterThrowing : 메서드가 예외를 던지는 경우 실행
* @After : 조인 포인트가 정상 또는 예외에 관계없이 실행(finally)

## 참고 정보 획득
모든 어드바이스는 org.aspectj.lang.JoinPoint 를 첫번째 파라미터에 사용할 수 있다. (생략해도 된다.)  
단 @Around 는 ProceedingJoinPoint 을 사용해야 한다.
참고로 ProceedingJoinPoint 는 org.aspectj.lang.JoinPoint 의 하위 타입이다.
### JoinPoint 인터페이스의 주요 기능
* getArgs() : 메서드 인수를 반환합니다.
* getThis() : 프록시 객체를 반환합니다.
* getTarget() : 대상 객체를 반환합니다.
* getSignature() : 조언되는 메서드에 대한 설명을 반환합니다.
* toString() : 조언되는 방법에 대한 유용한 설명을 인쇄합니다.
* ProceedingJoinPoint 인터페이스의 주요 기능
* proceed() : 다음 어드바이스나 타켓을 호출한다.

## 어드바이스 종류
#### @Before
조인 포인트 실행 전 
```java
@Before("hello.aop.order.aop.Pointcuts.orderAndService()")
public void doBefore(JoinPoint joinPoint) {
  log.info("[before] {}", joinPoint.getSignature());
}
```
@Around 와 다르게 작업 흐름을 변경할 수는 없다.  
@Around 는 ProceedingJoinPoint.proceed() 를 호출해야 다음 대상이 호출된다. 만약 호출하지 않으면 다음  
대상이 호출되지 않는다. 반면에 @Before 는 ProceedingJoinPoint.proceed() 자체를 사용하지 않는다. 메  
서드 종료시 자동으로 다음 타켓이 호출된다. 물론 예외가 발생하면 다음 코드가 호출되지는 않는다.  

### @AfterReturning
메서드 실행이 정상적으로 반환될 때 실행 
```java
@AfterReturning(value = "hello.aop.order.aop.Pointcuts.orderAndService()", returning = "result")
public void doReturn(JoinPoint joinPoint, Object result) {
  log.info("[return] {} return={}", joinPoint.getSignature(), result);
}
```
* returning 속성에 사용된 이름은 어드바이스 메서드의 매개변수 이름과 일치해야 한다.  
* returning 절에 지정된 타입의 값을 반환하는 메서드만 대상으로 실행한다. (부모 타입을 지정하면 모든 자식타입은 인정된다.)  
* @Around 와 다르게 반환되는 객체를 변경할 수는 없다. 반환 객체를 변경하려면 @Around 를 사용해야 한다. 참고로 반환 객체를 조작할 수 는 있다.

### @AfterThrowing
메서드 실행이 예외를 던져서 종료될 때 실행
```java
@AfterThrowing(value = "hello.aop.order.aop.Pointcuts.orderAndService()", throwing = "ex")
public void doThrowing(JoinPoint joinPoint, Exception ex) {
  log.info("[ex] {} message={}", joinPoint.getSignature(), ex.getMessage());
}
```
* throwing 속성에 사용된 이름은 어드바이스 메서드의 매개변수 이름과 일치해야 한다.
* throwing 절에 지정된 타입과 맞는 예외를 대상으로 실행한다. (부모 타입을 지정하면 모든 자식 타입은 인정된다.)

### @After
* 메서드 실행이 종료되면 실행된다. (finally를 생각하면 된다.)
* 정상 및 예외 반환 조건을 모두 처리한다.
* 일반적으로 리소스를 해제하는 데 사용한다.
* 
### @Around
* 메서드의 실행의 주변에서 실행된다. 메서드 실행 전후에 작업을 수행한다.
* 가장 강력한 어드바이스
  * 조인 포인트 실행 여부 선택 joinPoint.proceed() 호출 여부 선택
  * 전달 값 변환: joinPoint.proceed(args[])
  * 반환 값 변환
  * 예외 변환
  * 트랜잭션 처럼 try ~ catch~ finally 모두 들어가는 구문 처리 가능
* 어드바이스의 첫 번째 파라미터는 ProceedingJoinPoint 를 사용해야 한다.
* proceed() 를 통해 대상을 실행한다.
* proceed() 를 여러번 실행할 수도 있음(재시도)

## 포인트컷 지시자
### 포인트컷 지시자
포인트컷 표현식은 execution 같은 포인트컷 지시자(Pointcut Designator)로 시작한다. 줄여서 PCD라 한다.

* 포인트컷 지시자의 종류
  * execution : 메소드 실행 조인 포인트를 매칭한다. 스프링 AOP에서 가장 많이 사용하고, 기능도 복잡하다.
  * within : 특정 타입 내의 조인 포인트를 매칭한다.
  * args : 인자가 주어진 타입의 인스턴스인 조인 포인트
  * this : 스프링 빈 객체(스프링 AOP 프록시)를 대상으로 하는 조인 포인트
  * target : Target 객체(스프링 AOP 프록시가 가리키는 실제 대상)를 대상으로 하는 조인 포인트
  * @target : 실행 객체의 클래스에 주어진 타입의 애노테이션이 있는 조인 포인트
  * @within : 주어진 애노테이션이 있는 타입 내 조인 포인트
  * @annotation : 메서드가 주어진 애노테이션을 가지고 있는 조인 포인트를 매칭
  * @args : 전달된 실제 인수의 런타임 타입이 주어진 타입의 애노테이션을 갖는 조인 포인트
  * bean : 스프링 전용 포인트컷 지시자, 빈의 이름으로 포인트컷을 지정한다.
  * 포인트컷 지시자가 무엇을 뜻하는지, 사실 글로만 읽어보면 이해하기 쉽지 않다. 예제를 통해서 하나씩 이해해보자.
  * execution 은 가장 많이 사용하고, 나머지는 자주 사용하지 않는다. 따라서 execution 을 중점적으로 이해하자.

### execution1
execution 문법 
```
execution(modifiers-pattern? ret-type-pattern declaring-type-pattern?name-
pattern(param-pattern)
throws-pattern?)
execution(접근제어자? 반환타입 선언타입?메서드이름(파라미터) 예외?) 
```
* 메소드 실행 조인 포인트를 매칭한다.
* ?는 생략할 수 있다.
* 같은 패턴을 지정할 수 있다.

### 가장 정확한 포인트컷
먼저 MemberServiceImpl.hello(String) 메서드와 가장 정확하게 모든 내용이 매칭되는 표현식이다.
```java
@Test
void exactMatch() {
//public java.lang.String hello.aop.member.MemberServiceImpl.hello(java.lang.String)
  pointcut.setExpression("execution(public String hello.aop.member.MemberServiceImpl.hello(String))");
  assertThat(pointcut.matches(helloMethod, MemberServiceImpl.class)).isTrue();
}
```
AspectJExpressionPointcut 에 pointcut.setExpression 을 통해서 포인트컷 표현식을 적용할 수 있다.
pointcut.matches(메서드, 대상 클래스) 를 실행하면 지정한 포인트컷 표현식의 매칭 여부를 true ,false 로 반환한다.
#### 매칭 조건
* 접근제어자?: public
* 반환타입: String
* 선언타입?: hello.aop.member.MemberServiceImpl
* 메서드이름: hello
* 파라미터: (String)
* 예외?: 생략

가장 많이 생략한 포인트컷 
```java
@Test
void allMatch() {
    pointcut.setExpression("execution(* *(..))");
    assertThat(pointcut.matches(helloMethod, MemberServiceImpl.class)).isTrue();
}
```
가장 많이 생략한 포인트컷이다.
#### 매칭 조건
* 접근제어자?: 생략
* 반환타입: *
* 선언타입?: 생략
* 메서드이름: *
* 파라미터: (..)
* 예외?: 없음 
* \*은 아무 값이 들어와도 된다는 뜻이다.
* 파라미터에서 .. 은 파라미터의 타입과 파라미터 수가 상관없다는 뜻이다. ( 0..* ) 파라미터는 뒤에 자세히 정리하겠다.

### within
within 지시자는 특정 타입 내의 조인 포인트들로 매칭을 제한한다. 쉽게 이야기해서 해당 타입이 매칭되면 그 안의
메서드(조인 포인트)들이 자동으로 매칭된다.

## args
* args : 인자가 주어진 타입의 인스턴스인 조인 포인트로 매칭
* 기본 문법은 execution 의 args 부분과 같다.

### execution과 args의 차이점
* execution 은 파라미터 타입이 정확하게 매칭되어야 한다. execution 은 클래스에 선언된 정보를 기반으로 판단한다.
* args 는 부모 타입을 허용한다. args 는 실제 넘어온 파라미터 객체 인스턴스를 보고 판단한다.

## @target, @within
### 정의
* @target : 실행 객체의 클래스에 주어진 타입의 애노테이션이 있는 조인 포인트
* @within : 주어진 애노테이션이 있는 타입 내 조인 포인트
### 설명
* @target , @within 은 다음과 같이 타입에 있는 애노테이션으로 AOP 적용 여부를 판단한다.
* @target(hello.aop.member.annotation.ClassAop)
* @within(hello.aop.member.annotation.ClassAop)

## @annotation
### 정의
* @annotation : 메서드가 주어진 애노테이션을 가지고 있는 조인 포인트를 매칭
### 설명
* @annotation(hello.aop.member.annotation.MethodAop)

## @args
### 정의
* @args : 전달된 실제 인수의 런타임 타입이 주어진 타입의 애노테이션을 갖는 조인 포인트
### 설명
* 전달된 인수의 런타임 타입에 @Check 애노테이션이 있는 경우에 매칭한다.
  @args(test.Check)

## bean
### 정의
* bean : 스프링 전용 포인트컷 지시자, 빈의 이름으로 지정한다.
### 설명
* 스프링 빈의 이름으로 AOP 적용 여부를 지정한다. 이것은 스프링에서만 사용할 수 있는 특별한 지시자이다.
* bean(orderService) || bean(*Repository)
* \* 과 같은 패턴을 사용할 수 있다.

## 매개변수 전달
다음은 포인트컷 표현식을 사용해서 어드바이스에 매개변수를 전달할 수 있다.  
this, target, args,@target, @within, @annotation, @args  
다음과 같이 사용한다.  
```java
@Before("allMember() && args(arg,..)")
public void logArgs3(String arg) {
  log.info("[logArgs3] arg={}", arg);
}
```
* 포인트컷의 이름과 매개변수의 이름을 맞추어야 한다. 여기서는 arg 로 맞추었다.
* 추가로 타입이 메서드에 지정한 타입으로 제한된다. 여기서는 메서드의 타입이 String 으로 되어 있기 때문에 다음과 같이 정의되는 것으로 이해하면 된다.
  * args(arg,..) args(String,..)

## this, target
### 정의
* this : 스프링 빈 객체(스프링 AOP 프록시)를 대상으로 하는 조인 포인트
* target : Target 객체(스프링 AOP 프록시가 가리키는 실제 대상)를 대상으로 하는 조인 포인트
### 설명
* this , target 은 다음과 같이 적용 타입 하나를 정확하게 지정해야 한다.
```java
this(hello.aop.member.MemberService)
target(hello.aop.member.MemberService) 
```
* \* 같은 패턴을 사용할 수 없다.
* 부모 타입을 허용한다.

### this vs target
단순히 타입 하나를 정하면 되는데, this 와 target 은 어떤 차이가 있을까?  
스프링에서 AOP를 적용하면 실제 target 객체 대신에 프록시 객체가 스프링 빈으로 등록된다.  
* this 는 스프링 빈으로 등록되어 있는 프록시 객체를 대상으로 포인트컷을 매칭한다.
* target 은 실제 target 객체를 대상으로 포인트컷을 매칭한다.

### 프록시 생성 방식에 따른 차이
스프링은 프록시를 생성할 때 JDK 동적 프록시와 CGLIB를 선택할 수 있다. 둘의 프록시를 생성하는 방식이 다르기 때문에 차이가 발생한다.  
* JDK 동적 프록시: 인터페이스가 필수이고, 인터페이스를 구현한 프록시 객체를 생성한다.
* CGLIB: 인터페이스가 있어도 구체 클래스를 상속 받아서 프록시 객체를 생성한다.

## 스프링 AOP - 예제
지금까지 학습한 내용을 활용해서 유용한 스프링 AOP를 만들어보자.
* @Trace 애노테이션으로 로그 출력하기
* @Retry 애노테이션으로 예외 발생시 재시도 하기

## 프록시와 내부 호출
스프링은 프록시 방식의 AOP를 사용한다.
따라서 AOP를 적용하려면 항상 프록시를 통해서 대상 객체(Target)을 호출해야 한다.  
이렇게 해야 프록시에서 먼저 어드바이스를 호출하고, 이후에 대상 객체를 호출한다.  
만약 프록시를 거치지 않고 대상 객체를 직접 호출하게 되면 AOP가 적용되지 않고, 어드바이스도 호출되지 않는다.  
AOP를 적용하면 스프링은 대상 객체 대신에 프록시를 스프링 빈으로 등록한다. 따라서 스프링은 의존관계 주입시에 항  
상 프록시 객체를 주입한다. 프록시 객체가 주입되기 때문에 대상 객체를 직접 호출하는 문제는 일반적으로 발생하지 않  
는다. 하지만 대상 객체의 내부에서 메서드 호출이 발생하면 프록시를 거치지 않고 대상 객체를 직접 호출하는 문제가 발생한다.  

### 프록시와 내부 호출 - 대안1 자기 자신 주입
CallServiceV1Test 소스 참조

### 프록시와 내부 호출 - 대안2 지연 조회
CallServiceV2Test 소스 참조

### 프록시와 내부 호출 - 대안3 구조 변경
CallServiceV3Test  소스 참조

### 프록시 기술과 한계 - 타입 캐스팅
JDK 동적 프록시와 CGLIB를 사용해서 AOP 프록시를 만드는 방법에는 각각 장단점이 있다.  
JDK 동적 프록시는 인터페이스가 필수이고, 인터페이스를 기반으로 프록시를 생성한다.  
CGLIB는 구체 클래스를 기반으로 프록시를 생성한다.  
물론 인터페이스가 없고 구체 클래스만 있는 경우에는 CGLIB를 사용해야 한다. 그런데 인터페이스가 있는 경우에는  
JDK 동적 프록시나 CGLIB 둘중에 하나를 선택할 수 있다.  
스프링이 프록시를 만들때 제공하는 ProxyFactory 에 proxyTargetClass 옵션에 따라 둘중 하나를 선택해서
프록시를 만들 수 있다.  
* proxyTargetClass=false JDK 동적 프록시를 사용해서 인터페이스 기반 프록시 생성
* proxyTargetClass=true CGLIB를 사용해서 구체 클래스 기반 프록시 생성
* 참고로 옵션과 무관하게 인터페이스가 없으면 JDK 동적 프록시를 적용할 수 없으므로 CGLIB를 사용한다

### 프록시 기술과 한계 - CGLIB
스프링에서 CGLIB는 구체 클래스를 상속 받아서 AOP 프록시를 생성할 때 사용한다.  
CGLIB는 구체 클래스를 상속 받기 때문에 다음과 같은 문제가 있다.  

### CGLIB 구체 클래스 기반 프록시 문제점
* 대상 클래스에 기본 생성자 필수
* 생성자 2번 호출 문제
* final 키워드 클래스, 메서드 사용 불가

### 프록시 기술과 한계 - 스프링의 해결책
* 스프링 3.2, CGLIB를 스프링 내부에 함께 패키징
  CGLIB를 사용하려면 CGLIB 라이브러리가 별도로 필요했다. 스프링은 CGLIB 라이브러리를 스프링 내부에 함께 패
  키징해서 별도의 라이브러리 추가 없이 CGLIB를 사용할 수 있게 되었다. CGLIB spring-core-org.springframework
* CGLIB 기본 생성자 필수 문제 해결
  스프링 4.0부터 CGLIB의 기본 생성자가 필수인 문제가 해결되었다.
  objenesis 라는 특별한 라이브러리를 사용해서 기본 생성자 없이 객체 생성이 가능하다.
  참고로 이 라이브러리는 생성자 호출 없이 객체를 생성할 수 있게 해준다
* 생성자 2번 호출 문제
  스프링 4.0부터 CGLIB의 생성자 2번 호출 문제가 해결되었다.
  이것도 역시 objenesis 라는 특별한 라이브러리 덕분에 가능해졌다.
  이제 생성자가 1번만 호출된다
* 스프링 부트 2.0 - CGLIB 기본 사용
  스프링 부트 2.0 버전부터 CGLIB를 기본으로 사용하도록 했다.  
  이렇게 해서 구체 클래스 타입으로 의존관계를 주입하는 문제를 해결했다.  
  스프링 부트는 별도의 설정이 없다면 AOP를 적용할 때 기본적으로 proxyTargetClass=true 로 설정해서 사용한다.
  따라서 인터페이스가 있어도 JDK 동적 프록시를 사용하는 것이 아니라 항상 CGLIB를 사용해서 구체클래스를 기반으  
  로 프록시를 생성한다.  
  물론 스프링은 우리에게 선택권을 열어주기 때문에 다음과 깉이 설정하면 JDK 동적 프록시도 사용할 수 있다.  

application.properties
```
spring.aop.proxy-target-class=false
```