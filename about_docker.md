Docker 도커
===========================================
목차
1. Docekr 란 무엇인가?
2. Docker 이미지
3. Docker 이미지를 다른 환경에서 실행하기
4. Docker 명령어
   
## 1. Docker 란 무엇인가?
도커는 컨테이너 기술을 기반으로 동작하는 가상화 플랫폼이다.

가상화란 물리적 자원인 하드웨어를 효율적으로 활용하기 위해서 하드웨어 공간 위에 가상의 머신을 만드는 기술이고, 컨테이너란 컨테이너가 실행되고 있는 호스트 os의 기능을 그대로 사용하면서 프로세스를 격리해 독립된 환경을 만드는 기술을 뜻한다.

다시말해, 도커는 '독립된 환경을 만들어서 하드웨어를 효율적으로 활용하는 기술'이라고 할 수 있다.

### 가상화
가상화는 하나의 하드웨어를 여러개의 가상 머신으로 분할해 효율적으로 사용할 수 있는 기술이다.

분할 된 가상머신들은 각각 독립적인 환경으로 구동되는데, 베이스가 되는 기존의 환경을 Host OS, 가상 머신으로 분할된 각각의 환경을 Guest OS라고 부른다.

가상머신을 생성하기 위해선 Hypervisor, 또는 가상 머신 모니터라고 불리는 소프트웨어를 Host에 설치해 호스트와 게스트를 나누고, 각각의 게스트를 관리하며 시스템 자원을 할당한다.

이때 하이퍼바이저에 의해 생성된 게스트는 호스트나 다른 게스트와 상호 간섭하지 않고 완전히 분리된 환경에서 구동된다. 하지만 작업을 하려면 반드시 하이퍼바이저를 거쳐야 하기 때문에 속도저하가 필연적으로 따른다.

또한 가상머신은 해당 환경을 구동하는데 필요한 파일을 모두 포함하고 있기 때문에, 가상머신을 배포할 때 만들어지는 이미지의 크기가 매우 커진다는 단점이 있다.

### 컨테이너
하이퍼바이저와 다르게 컨테이너는 가상의 OS를 만드는 것이 아니라 베이스 환경의 OS를 공유하면서 필요한 '프로세스' 만을 격리하는 방식으로, 시스템 커널을 공유하기 때문에 호스트 OS의 기능을 모두 사용할 수 있다.

그렇기 때문에 컨테이너 위에서는 호스트 OS와 다른 OS를 구동할 수는 없다.
대신 격리시킨 앱과 거기에 필요한 파일, 라이브러리 등 종속 항목만을 포함시켜 배포를 위해 생성되는 이미지의 크기를 대폭 줄일 수 있는 장점이 있다.

또한 OS가 아닌 프로세스이고, 하이퍼바이저를 거칠 필요가 없기 때문에 실행속도가 상대적으로 빠르다는 장점도 지닌다.

이런 컨테이너의 특성을 지닌 가상화 플랫폼 도커는 아주 강력한 도구이다.

개발 과정에서 다른 라이브러리와 충돌하는 것을 막기 위해 격리된 환경이 필요할 때, 완성된 서비스를 배포할 때, 혹은 배포중인 서비스를 받아서 실행해볼 때 유용히 사용가능하다.

특히 배포 과정에서 도커를 사용해 필요한 파일들만 예쁘게 포장해서 이미지로 만들면 지긋지긋한 종속성 이슈에서 벗어날 수 있다. 서버 한대에만 배포한다면 종속성에 크게 휘둘리지 않지만 서버 갯수가 늘어날수록 부담이 가중되기에 도커를 사용해 같은 이미지를 실행해서 컨테이너로 만드는 방식을 선호하게 된다.

요약하면,

    도커는 프로세스를 호스트와 무관하게 동작시켜 가상머신처럼 보이는 '컨테이너형 가상화'를 지원하는 도구이다.
    가상머신의 역할을 넘어서 어느 플랫폼에서나 특정한 상태를 그대로 재현가능한 애플리케이션 컨테이너를 관리하는 도구이다.
<br>

## 2. Docker 이미지
도커 이미지를 생성하려면 Dockerfile 이라는 특별한 파일을 작성해야 한다.
Dockerfile은 이미지를 빌드하는데 필요한 명령어와 설정을 정의하는 스크립트 파일이다.
다음은 간단한 도커 이미지를 생성하는 단계이다.

* Dockerfile 작성
    
    텍스트 편집기를 이용해 프로젝트 디렉토리에 Dockerfile을 생성하고 편집한다.
    다음은 간단한 도커파일 예시이다.

        # 기본 이미지 설정
        FROM ubuntu:latest

        # 작업 디렉토리 설정
        WORKDIR /app

        # 파일 복사 (로컬 파일을 컨테이너 내 /app 디렉토리로 복사)
        COPY . /app

        # 필요한 의존성 설치 또는 명령어 실행
        RUN apt-get update && \
            apt-get install -y python3 && \
            apt-get clean

        # 컨테이너가 실행될 때 실행할 명령어
        CMD ["python3", "app.py"]
    

     이 예시는 Ubuntu기반의 이미지를 사용하고, 작업 디렉토리를 /app으로 설정한 후
    디렉토리의 모든 파일을 /app으로 복사한다.
    그리고 필요한 패키지를 설치하고, 마지막으로 컨테이너가 실행될 때 실행할 명령어를 정의한다.

* 이미지 빌드

     다음으로 도커파일이 있는 디렉토리에서 다음 명령어를 실행하여 이미지를 빌드한다.
        
        docker build -t 이미지명:태그 .
        ex) docker build -t hello-world:latest .


* 생성된 이미지 확인
    
    이미지가 성공적으로 빌드되었는지 확인한다.
        
        docker images

* 이미지 실행
    빌드한 이미지를 실행하여 컨테이너를 생성한다.

        docker run 이미지명:태그
        ex) docker run hello-world:latest
<br>


## 3. Docker 이미지를 다른 환경에서 실행하기

<br>
도커 이미지를 다른 컴퓨터에서 실행하려면 이미지를 해당 컴퓨터로 전송하고,
도커를 설치한 후 해당 이미지를 로컬에서 실행해야 한다.

이미지 전송을 위해 이미지를 Docker Hub과 같은 원격 리포지토리에 업로드하거나, 이미지를 저장한 파일을 전송하는 방법 등을 이용할 수 있다.

* 이미지 저장하기
        docker save -o 이미지명.tar 이미지명:태그
        ex) docker save -o hello-world.tar hello-world:latest

* 전송
    이미지를 tar 파일로 저장한 후 해당 파일을 다른 컴퓨터로 복사하거나 전송한다.
    Docker Hub나 SCP, FTP, 혹은 USB 드라이브를 이용할 수도 있다.

* 전송(using Docker Hub)
    다음과 같은 단계로 Docker Hub를 이용해 tar파일로 만들지 않고 바로 이미지를 공유할 수 있다.
        1. Docker Hub 로그인, 계정정보 입력
        docker login

        2. 이미지 업로드
        docker push 사용자명/이미지명:태그
        ex) docker push username/hello-world:latest

        3. 다른 컴퓨터에서 이미지 다운로드 및 실행
        docker login

        docker pull 사용자명/이미지명:태그
        ex) docker pull username/hello-world:latest

        docekr run 사용자명/이미지명:태그

* 이미지 불러오기
    이미지를 전송받은 컴퓨터에서 도커 명령어를 사용하여 이미지를 불러온다.
        docker load -i 이미지명.tar

* 이미지 실행
        docker run 이미지명:태그
<br>
<br>

## 3. Docker 명령어

도커 이미지 다운로드(PULL) 및 실행
    
    docker pull 이미지명:태그
    docker run 이미지명:태그

도커 컨테이너 목록 확인
    
    docker ps

도커 이미지 목록 확인
    
    docker images

도커 컨테이너 실행 및 접속 : 이미지 기반으로 도커 컨테이너를 실행, 컨테이너 접속
    
    docker run -it 이미지명:태그 /bin/bash
    docker exec -it 컨테이너ID /bin/bash

도커 컨테이너 정지 및 삭제
    
    docker stop 컨테이너ID
    docker rm 컨테이너ID

도커 이미지 삭제
    
    docker rmi 이미지명:태그

도커 네트워크 확인
    
    docker network ls
