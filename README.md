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

