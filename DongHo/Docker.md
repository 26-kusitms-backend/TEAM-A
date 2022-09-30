# 도커

### Docker 란?
도커(Docker)는 리눅스의 응용 프로그램들을 프로세스 격리 기술들을 사용해 컨테이너로 실행하고 관리하는 오픈 소스 프로젝트이다.

https://ko.wikipedia.org/wiki/%EB%8F%84%EC%BB%A4_(%EC%86%8C%ED%94%84%ED%8A%B8%EC%9B%A8%EC%96%B4) 



### Docker 사용해야 하는 이유

Docker를 사용하면 코드를 더 빨리 전달하고, 애플리케이션 운영을 표준화하고, 코드를 원활하게 이동하고, 리소스 사용률을 높여 비용을 절감할 수 있습니다. 
Docker를 사용하면 어디서나 안정적으로 실행할 수 있는 단일 객체를 확보하게 됩니다. 
Docker의 간단한 구문을 사용해 완벽하게 제어할 수 있습니다. 

Docker가 폭넓게 도입되었다는 것은 Docker를 사용할 수 있는 도구 및 상용 애플리케이션의 에코시스템이 강력하다는 의미입니다.

![다운로드 (2)](https://user-images.githubusercontent.com/92728780/191798167-9a75aac3-60bc-400b-9825-59046eefc3e7.png)



### Docker 사용하는 경우
Docker 컨테이너를 최신 애플리케이션 및 플랫폼을 생성하는 핵심 빌딩 블록으로 사용할 수 있습니다. 
Docker에서는 손쉽게 분산 마이크로서비스 아키텍처를 구축 및 실행하고, 표준화된 지속적 통합 및 지속적 전달 파이프라인을 통해 코드를 배포하고, 고도로 확장 가능한 데이터 처리 시스템을 구축하고, 개발자를 위한 완전관리형 플랫폼을 생성할 수 있습니다.

![image](https://user-images.githubusercontent.com/92728780/191798358-95186696-17d4-419b-b613-b2dc294ee884.png)
https://aws.amazon.com/ko/docker/



### 도커 구성 요소
![image](https://user-images.githubusercontent.com/92728780/191798445-2c2efee9-eb39-4792-a035-1c9b2ec1e91e.png)
![image](https://user-images.githubusercontent.com/92728780/191798475-8ad03841-bd50-4284-8492-7d1030d68fd1.png)
https://armin.tistory.com/entry/CS-overview-Docker-%EA%B5%AC%EC%84%B1%EC%9A%94%EC%86%8C

### 도커가 내부적으로 동작하는 원리
![image](https://user-images.githubusercontent.com/92728780/191798655-6df1b2c2-8120-49d9-8a01-23b842f8e473.png)

- 도커의 컨테이너 기술은 리눅스의 컨테이너 (LXC)를 활용한 기술로, 컴퓨터에 독립적인 컴퓨팅 공간을 만들어낸다. 이는 기존의 VM (가상머신)과 비교하여 가상환경 구조에 근본적인 차이가 있다. 아래 그림을 보면, 좌측의 VM은 환경 자체 (OS)를 가상화 해버리는 반면, 도커 컨테이너는 호스트 OS의 커널을 공유하며 단순히 하나의 격리되어 있는 프로세스로써 동작한다. 

- 도커는 컨테이너라는 가상의 '격리 환경'을 만들기 위해 리눅스의 namespace과 cgroup이라는 기능을 사용한다. (namespace와 cgroup으로 만들어진 컨테이너를 LXC라고 부른다)

   - namespace: 프로세스를 독립시켜주는 가상화 기술이다. 각 컨테이너에서 실행된 프로세스가 시스템(user, 파일, 네트워크, 호스트명, 프로세스)등에 대해 독립할 수 있게 해준다.

   - cgroups: 자원(CPU, 메모리, network bandwidth)에 대한 제어를 가능하게 해주는 리눅스 커널의 기능이다. 

 

#### namespace

1. 프로세스 별로 리소스 사용을 분리한다.
2. VM에서는 각 게스트 별로 독립적인 공간을 제공하고 충돌하지 않도록 Hardware Resource 자체를 가상화한다. 
3. 하지만 namespace의 경우, Hardware Resource 자체를 가상화하는 것이 아니라, Linux 내의 자원을 가상화한다.


#### cgroup

1. Control Groups의 약자로 프로세스들이 사용할 수 있는 컴퓨팅 자원들을 제한하고 격리시킬 수 있는 리눅스 커널의 기능이다. namespace와 더불어 도커 컨테이너에서 완벽한 격리 환경을 만드는 데에 쓰이는 중요한 기능이다. 

2. cgroup를 이용하면 다음 자원들을 제한할 수 있다

#### 메모리
#### CPU
#### Network
#### Device
#### I/O

https://anweh.tistory.com/67
