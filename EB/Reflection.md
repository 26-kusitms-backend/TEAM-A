# Reflection

## 1. Reflection 이란

`구체적인 클래스 타입을 알지 못해도`, 그 클래스의 메소드, 타입, 변수들에 접근할 수 있도록 해주는 자바 API다.

리플렉션을 왜 사용할까?

자바는 정적 타입 언어이기 때문에 컴파일 시점에 타입을 결정한다. 
리플렉션이 주로 사용되는 곳은 프레임워크나 라이브러리 개발에서 많이 사용된다. 
떠올려 보자. 우리가 사용하는 프레임워크나 라이브러리가 우리가 어떤 클래스를 만들지 다 알고서 이것들을 만들어두었을까? 아니다. 그래서 어떤 클래스를 만들던지 사용할 수 있도록 동적으로 동작하게 하기 위해 리플렉션을 사용한다.

대표적인 예시로는 스프링의 DI, Proxy, ModelMapper 등이 있다.

```` bash
@Controller
@RequestMapping("/api/region")
@RequiredArgsConstructor
public class RegionController {

  private final IRegionService regionService;

  @PostMapping
  public ResponseEntity<BaseResponse> create(RegionDto.HouseRequest request) {
    Long response = regionService.create(request);
    return ResponseEntity.ok(BaseResponse.builder()
        .message("지역 생성 성공").data(response).build());
  }
}

````

위의 코드를 예시로 살펴보자. 

스프링 프레임워크를 이용해서 개발할 때, `@Controller` 어노테이션을 사용하면 인스턴스를 생성하지 않아도 스프링이 알아서 해당 클래스를 빈으로 생성해서 관리해준다.

그렇다면 스프링은 어떻게 `RegionController`의 존재를 알고 만들어주는 것일까?
또한, `IRegionService` 라는 멤버 변수를 어떻게 의존성 주입해주는 것일까?
모든 메소드의 파라미터 개수, 타입이 다른데 어떻게 이들에 해당하는 값을 바인딩해줄까?

`RegionController` 클래스를 개발한 개발자는 해당 클래스의 정보를 알지만, 스프링은 모른다.
그래서 리플렉션 기법이 필요하다. 스프링이 개발자가 작성한 클래스의 정보를 알아내기 위해서.

스프링은 `@Controller` 를 갖고 있는 클래스를 스캔한다. 그리고 해당하는 클래스의 인스턴스를 생성하고 필드를 의존성 주입한다.

어떻게 할까?

<img src="
https://s3.us-west-2.amazonaws.com/secure.notion-static.com/1ea105f1-32fa-4294-9d47-bd408f53eff3/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20221028%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20221028T005742Z&X-Amz-Expires=86400&X-Amz-Signature=79dc9a2ce9334c1ff40fa10247c5b0e34a37aef1bf3e15fbbd7405d325c472e7&X-Amz-SignedHeaders=host&response-content-disposition=filename%3D%22Untitled.png%22&x-id=GetObject">

위의 코드와 같이 사용할 수 있다.

과거 클라이언트-서버 프로그래밍 방식의 LMS 프로젝트를 떠올려보자.

클라이언트로부터 받은 요청을 서버에서 처리하기 위해 리플렉션 기법을 사용했었다.

```` bash
public void receivepro(Vector<String> receive) {
		System.out.println(receive.toString());
		System.out.println("클라이언트로부터 프로토콜 수신");
		try { // receive protocol Info
			String className = receive.get(0);
			String methodName = receive.get(1);
			String isReturn = receive.get(2);
			String param = receive.get(3);
			System.out.println(className + "/" + methodName + "/" + isReturn + "/" + param);
			if (isReturn == null) {
				if (param != null) {
					if (methodName.equals("add")) { // logout
						logoutR(className, methodName, param);
					} else if(methodName.equals("deleteAll")) {
						deleteR(className,methodName, param);
					} else if(methodName.equals("lectureEvaluation")) {
						lectureEvaluation(className, methodName, param);}
					else { // createFile && createFileS
						createFileR(className, methodName);
					}
				} else { // param == null
					if (className == null) {
						this.finalize();
					}else {
						registerR(className, methodName);
					}
				}
			} else { // isReturn != null
				if (param == null) {
					if(methodName.equals("findID")) {
						findIDR(className, methodName);
					} else if (methodName.equals("newPW")) {
						newPWR(className, methodName);
					}else if(methodName.equals("getEvaluation")) {
						getEvaluation(className, methodName);
					} else { // login
						loginR(className, methodName); }
				} else { // param != null
					 if (methodName.equals("overlap")) {
						overlapR(className, methodName, param);} 
					else if(methodName.equals("userInfo")) {userInfoR(className, methodName, param);}
					else if(methodName.equals("findPW")) {findPWR(className,methodName);}
					else if(methodName.equals("computeScore")) {computeR(className, methodName);}
					else if(methodName.equals("countLecture")) {System.out.println("-----------------");computeR(className, methodName);}
					else if(methodName.equals("getLectures")) {getLectures(className, methodName);}
					else if(methodName.equals("getImages")) {getImages(className, methodName);}
					else { // directory && table
						tableR(className, methodName, param);
					}
				}
			}
			System.out.println("data 전송 완료");
		} catch (SecurityException e) {e.printStackTrace();
		} catch (IllegalArgumentException e) {e.printStackTrace();
		} catch (Throwable e) {e.printStackTrace();		}
	}
````
> https://github.com/dldmsql/LMS_System-Client-Server-version/blob/master/2019_2%ED%95%99%EA%B8%B0%20%EA%B8%B0%EB%A7%90/src/Server/Skeleton.java

이러한 원리를 통해 스프링에서는 DI를 구현한다.

리플렉션을 왜, 어떻게 사용하는지에 대해 알아봤다. 그렇다면 리플렉션의 특징을 정리해보자.

- 과도한 사용은 지양

성능 상의 오버헤드가 있다. 이미 인스턴스를 만들었음에도 불구하고 굳이 필드와 리플렉션을 이용해서 접근하거나 사용할 경우.

- 런타임 시에만 발생하는 문제를 야기할 가능성

- 접근 지시자 무시

private 속성과 메소드에 접근가능하기에 예기치 않은 문제를 발생시킬 수 있으며 캡슐화를 저해시킨다.

https://medium.com/msolo021015/%EC%9E%90%EB%B0%94-reflection%EC%9D%B4%EB%9E%80-ee71caf7eec5

https://dublin-java.tistory.com/53