# 스프링 프로젝트의 시작점

### Application.java

```` bash
@SpringBootApplication//swagger 동작확인을 위해 넣음 (개발 시 삭제)
public class ServerApplication {

	public static void main(String[] args) {
		SpringApplication.run(ServerApplication.class, args);
	}

}
````
### SpringApplication.java

```` bash
	public ConfigurableApplicationContext run(String... args) {

        // 1. 실행 시간 측정 시작
		long startTime = System.nanoTime();

        // 2. BootStrapContext 생성
		DefaultBootstrapContext bootstrapContext = createBootstrapContext();
		ConfigurableApplicationContext context = null;

        // 3. java AWT Headless Property 설정
		configureHeadlessProperty();

        // 4. 스프링 어플리케이션 리스너 조회 및 시작 처리
		SpringApplicationRunListeners listeners = getRunListeners(args);
		listeners.starting(bootstrapContext, this.mainApplicationClass);
		try {

            // 5. Arguments 래핑 및 Environment 준비
			ApplicationArguments applicationArguments = new DefaultApplicationArguments(args);
			ConfigurableEnvironment environment = prepareEnvironment(listeners, bootstrapContext, applicationArguments);

            // 6. Ignore Bean Info 설정
			configureIgnoreBeanInfo(environment);

            // 7. 배너 출력
			Banner printedBanner = printBanner(environment);

            // 8. 어플리케이션 컨텍스트 생성
			context = createApplicationContext();
			context.setApplicationStartup(this.applicationStartup);

            // 9. 컨텍스트 준비 단계
			prepareContext(bootstrapContext, context, environment, listeners, applicationArguments, printedBanner);

            // 10. 컨텍스트 refresh 단계
			refreshContext(context);

            // 11. 컨텍스트 refresh 후처리 단계
			afterRefresh(context, applicationArguments);

            // 12. 실행시간 출력 및 리스너 시작상태로
			Duration timeTakenToStartup = Duration.ofNanos(System.nanoTime() - startTime);
			if (this.logStartupInfo) {
				new StartupInfoLogger(this.mainApplicationClass).logStarted(getApplicationLog(), timeTakenToStartup);
			}
			listeners.started(context, timeTakenToStartup);

            // 13. Runners 실행
			callRunners(context, applicationArguments);
		}
		catch (Throwable ex) {
			handleRunFailure(context, ex, listeners);
			throw new IllegalStateException(ex);
		}
		try {
			Duration timeTakenToReady = Duration.ofNanos(System.nanoTime() - startTime);
			listeners.ready(context, timeTakenToReady);
		}
		catch (Throwable ex) {
			handleRunFailure(context, ex, null);
			throw new IllegalStateException(ex);
		}
		return context;
	}
````

스프링 부트 프로젝트를 실행하면, main 메소드의 `run` 메소드가 실행된다.

run 메소드는 크게 아래와 같은 순서로 동작한다.

1. 실행 시간 측정 시작

`System.nanoTime()` 를 이용해서 실행 시작 시간을 측정한다.

2. BootStrapContext 생성

BootStrapContext는 어플리케이션 컨텍스트가 준비될 때까지 환경변수들을 관리하는 스프링의 환경 객체를 후처리하기 위한 임시 컨텍스트이다. 

DefaultBootstrapContext 객체를 생성해주고, ConfigurableApplicationContext 객체를 null로 선언해준다.

3. Java AWT Headless Property 설정

Java AWT Headless 모드는 모니터나 마우스, 키보드와 같은 디스플레이 장치가 없는 서버 환경에서 UI 클래스를 사용할 수 있도록 하는 옵션이다. 

예를 들어, 서버에서 이미지를 만들어 반환해주어야 하는 경우에 이미지 관련 클래스 등이 필요할 수 있다. 만약 Headless 모드를 주지 않으면 해당 클래스의 사용이 불가능하다고 에러를 뱉는다. Headless 모드를 True로 해주면 사용 불가능한 UI 클래스들을 특별 객체로 만들어준다. 

java.awt.Toolkit 이 특별하게 headless 전용 객체로 만들어서 디스플레이 장치가 없어도 사용할 수 있게 해준다. 

스프링부트에서는 기본적으로 headless 모드를 True로 두기 때문에 java.awt 패키지를 이용해 이미지 관련 처리를 할 수 있다.

4. 스프링 어플리케이션 리스너 조회 및 시작 처리

어플리케이션 컨텍스트를 준비할 때, 호출되어야 하는 리스너들을 찾아서 부트스트랩 컨텍스트의 리스너로써 실행하게 해준다.

예를 들어 생성 시간이 긴 객체들의 경우, 객체 생성을 위한 리스너를 만들어 등록하면 부트스트랩 컨텍스트가 어플리케이션 컨텍스트를 준비함과 동시에 객체를 생성하도록 함으로써 Lazy하게 접근 하도록 할 수 있다.

5. Arguments 래핑 및 Environment 준비

`DefaultApplicationArguments(args);` 를 보면, main 메소드에서 String[] 형태의 인자를 스프링부트를 위한 인자인 ApplicationArguments로 래핑해준다. 그리고 이를 Environment를 준비하는 `prepareEnvironment()`에 넘겨준다.

```` bash
private ConfigurableEnvironment getOrCreateEnvironment() {
		if (this.environment != null) {
			return this.environment;
		}
		switch (this.webApplicationType) {
			case SERVLET:
				return new ApplicationServletEnvironment();
			case REACTIVE:
				return new ApplicationReactiveWebEnvironment();
			default:
				return new ApplicationEnvironment();
		}
	}
````

Environment 객체를 만들어서 어플리케이션 타입에 맞게 구현체를 생성해준다.

그 다음에 Property나 Profile 등과 같은 값을 세팅해주고 SpringApplication에 바인딩해준다.

`bindToSpringApplication(environment);`

6. IgnoreBeanInfo 설정

BeanInfo는 JSP에서 사용되는 기술인 Java Beans를 위한 빈 정보를 의미한다. 요즘은 Java Beans를 사용하지 않아서 관련 작업을 처리하지 않도록 설정해주는 부분이다.

7. 배너 출력

스프링부트 어플리케이션을 실행하면 콘솔에 출력되는 그 배너를 출력하는 코드가 여기이다. 

8. 어플리케이션 컨텍스트 생성

앞서 null로 선언한 컨텍스트를 여기서 생성한다.

```` bash
	protected ConfigurableApplicationContext createApplicationContext() {
		return this.applicationContextFactory.create(this.webApplicationType);
	}
````

코드를 보면 알 수 있듯이 어플리케이션 컨텍스트 생성을 팩토리클래스에게 위임한다.

`webApplicationType` 는 앞서 envrionment의 코드에서 봤던 것과 동일하다. 생성을 팩토리에게 위임함으로써 SpringApplication은 어떤 어플리케이션 컨텍스트 구현체가 존재하고 만들어지는지 알 필요가 없다. 다만, webApplicationType의 공통된 계층인 ConfigurableApplicationContext를 받음으로써 추상화하고 있다. 

9. 컨텍스트 준비 단계

컨텍스트가 생성된 후에 해야 하는 후처리 작업들과 빈 객체들을 등록하는 refresh 단계를 위한 전처리 작업을 담당한다.

Environment 객체를 어플리케이션 컨텍스트에 설정해준다. 그리고 이전의 작업들에서 beanNameGenerator, resourceLoader 등이 생성되었다면 싱글톤으로 등록해준다.

여기까지 오면, 어플리케이션 컨텍스트가 생성되었기 때문에 부트스트랩컨텍스트는 불필요하다. 따라서 부트스트랩 컨텍스트를 종료해준다.

그리고 래핑한 Application Arguments와 Banner 클래스를 빈으로 등록해주고 순환 잠조 여부와 빈 정보 오버라이딩 여부를 설정해준다. ( 기본적으로 순환 잠조는 불가능하며, 발생 시 에러가 발생하면서 어플리케이션이 종료된다. )

10. 컨텍스트 Refresh 단계

우리가 만든 빈 객체들을 찾아서 등록하고 웹 서버를 만들어 실행하는 핵심 작업을 담당한다. 따라서 이 단계를 지나면 모든 객체들이 싱글톤으로 인스턴스화되는데, 만약 에러가 발생한다면 등록된 모든 빈 객체들을 제거한다. 그래서 이 단계를 지나면 모든 빈 객체가 인스턴스화되었거나 어떠한 빈 객체도 존재하지 않는 상태가 된다.

11. 컨텍스트 Refresh 후처리 단계

```` bash
protected void afterRefresh(ConfigurableApplicationContext context, ApplicationArguments args) {
	}
````

별다른 작업을 하지 않는 것으로 보인다.

12. 실행 시간 출력 및 리스너 시작 상태로

어플리케이션을 시작하는 데 걸린 시간을 로그로 남기고 리스너들을 started 상태로 처리한다.

13. Runners 실행

마지막 단계로, Runner를 호출하는 단계이다. 

어플리케이션이 실행된 이후웨 초기화 작업이 필요한 경우가 있을 수 있다. 이때 초기화를 위한 방법 중 하나가 Runner를 등록하는 것이다. Runner는 2가지가 있다. 

String을 파라미터로 갖는 CommandLineRunner와 다른 타입을 파라미터로 갖는 ApplicationRunner가 있다. 

https://mangkyu.tistory.com/213