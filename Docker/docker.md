## Docker란

- **도커 : 컨테이너의 변하지 않는 설정을 다른 컴퓨터에서도 쓰기 쉽게하기 위해 사용?
  이미지 : 라이브러리 등을 압축하여 이미지화한 것**
- 도커는 ’/usr/bin/docker’ 에 위치 , 하지만 실제 도커 엔진의 프로세스를 확인하면 ‘usr/bin/dockerd’ 파일로 실행됨
- 즉, 컨테이너나 이미지를 다루는 명령어는 /usr/bin/docker에서 실행되지만 도커 엔진의 프로세스는 /usr/bin/dockerd 파일로 실행 → docker의 명령어가 실제 도커 엔진이 아닌 클라이언트로서의 도구이기 때문
- 도커는 기본적으로 유닉스 도메인 소켓을 이용해서 통신을 한다. 소켓의 주소는 unix:///var/run/docker.socket 으로 root만 사용할 수 있다.
  ```tsx
  // 사용시 터미널에서 루트로 연결해야함
  su root
  ```

## Docker 구조

- 크게 2가지로 하나는 클라이언트로서의 도커이고, 다른 하나는 서버로서의 도커로 분류
- 컨테이너를 생성하고 실행하며 이미지를 관리하는 주체는 서버도커이고, 이는 dockerd 프로세스로서 동작
- 도커 엔진은 외부에서 API 입력을 받아 도커 엔진의 기능을 수행하는데, 도커 프로세스가 실행되어 서버로서 입력을 받을 준비가 된 상태를 **도커 데몬**이라 함
- 도커 데몬은 API 입력을 받아 도커 엔진의 기능을 수행하는데, 이 API를 사용할 수 있도록 CLI(Command Line Interface)를 제공하는 것이 도커 클라이언트
- 사용자가 docker로 시작하는 명령어를 입력하면 도커 클라이언트를 사용하는 것, 도커 클라이언트는 입력된 명령어를 로컬에 존재하는 도커 데몬에게 API로서 전달 → 이때 도커 클라이언트는 /var/run/docker.sock에 위치한 유닉스 소켓을 통해 도커 데몬의 API를 호출
- 도커 제어 과정 (터미널 or PuTTY에서 도커가 설치된 호스트에 접속해 docker 명령어 입력)
  1. 도커 명령어 ( ex) docker ps )를 입력
  2. /usr/bin/docker는 /var/run/docker.sock 유닉스 소켓을 사용해 도커 데몬에게 명령어 전달
  3. 도커 데몬은 이 명령어를 파싱하고 명령어에 해당하는 작업 수행
  4. 수행 결과를 도커 클라이언트에 반환하고 사용자에게 결과 출력

## Docker daemon

- 우분투에서는 도커가 설치되면 자동으로 서비스로 등록되므로 호스트가 재시작되더라도 자동으로 실행
- 도커 데몬은 일반적으로 아래의 명령어로 시작 및 정지 가능
  ```tsx
  service docker start // 시작
  service docker stop // 정지
  ```
- service 사용하지 않고 dockerd로 직접 도커 데몬 실행도 가능

  ```tsx
  sudo dockerd

  // 명령어 확인
  sudo dockerd --help
  ```

  - 직접 도커 데몬을 실행하면 하나의 터미널을 차지하는 포그라운드(foreground) 상태로 실행되므로 운영 및 관리 측면에서 바람직하지 않고, 실제 운영 환경에서는 직접 실행하기보다는 service, systemctl 명령어를 통해 리눅스 서비스로 관리

- 직접 도커 데몬 실행
  ```tsx
  dockerd -H tcp://0.0.0.:2375 -insecure-registry=192.168.100.99:5000 -tls=false
  ```
- 서비스로 실행
  ```tsx
  DOCKER_OPTS =
    "-H tcp://0.0.0.0:2375 --insecure-registry=192.168.100.99:5000 --tls=false";
  ```
  - 두 방법 모두 도커 데몬을 설정
  - 직접 도커 데몬을 실행하는가, 서비스로 실행하는가의 차이

## Docker , Virtual machine

- 도커는 virtual machine이 아님

```tsx
// 도커에서 해당 명령 실행할 경우
$ docker run --name ubuntu_test ubuntu

// 결과 : 아무 변화 없음 -> 컨테이너 목록 보면 종료(exit)되었다고 나타남
$ docker ps -a
d8f31b2635d9    ubuntu    "/bin/bash"   19 seconds ago  Exited (0) 17 seconds ago   ubuntu_test

```

- 우분투 image를 실행했는데 왜 아무것도 실행되지 않고 종료 될까? → Docker 컨테이너는 Virtual machine과 같이 하나의 온전한 서버를 제공하는 것이 아니라 명령을 실행하는 환경만 제공하고 명령을 실행할 뿐임
- 즉, 우분투 서버가 실행되는 것이 아니라 “/bin/bash”가 실행될 뿐 (Virtual machine과 Docker 컨테이너의 가장 큰 차이)

## Docker api 조사

- 필요기능에 따른 API **세부스펙 조사**
  - create 부분 예시
    ```tsx
    { // 해당 코드에서 AttachStdin값이 무엇을 나타내는지.. 변경하면 어떤식으로 처리 되는지 등 정리
    "Hostname": "",
    "Domainname": "",
    "User": "",
    "AttachStdin": false,
    "AttachStdout": true,
    "AttachStderr": true,
    "Tty": false,
    "OpenStdin": false,
    "StdinOnce": false,
    ```
  - 해당 API를 구현한 npm module이 있는지 조사 및 있다면 검증(test 해보기)
- Engine API에서 확인 : [https://docs.docker.com/engine/api/v1.41/](https://docs.docker.com/engine/api/v1.41/)
- 필요 기능 (Engine API)
  - 컨테이너 목록 조회 : [containers] - [list containers]
    - all : 모든 컨테이너 리턴 / 디폴트 값으로, 구동되고 있는 컨테이너만 보여짐
    - limit : 가장 최근에 생성한 컨테이너 번호 리턴
    - size : 컨테이너 크기 리턴
    - filters : 특정 필터처리 적용한 컨테이너만 리턴
      - ancestor, before, expose… 등 사용가능한 filter 참고
  - 컨테이너 생성 : [containers] - [create]
- **docker stop / 🐳 docker kill 차이**
  - docker stop은 Gracefully(우아)하게 작업을 중지. 이 뜻은, 하고 있는 작업을 마친 후에 컨테이너를 중지한다는 것
  - docker kill 또한 컨테이너를 **중지**합니다. (중지 및 삭제의 숏컷이 아닙니다! 주의하세요!) docker stop과의 차이점은 stop이 gracefully하게 컨테이너를 중지시키는 반면 kill은 어떠한 작업도 기다리지 않고 즉각 중지
- **🐳 docker create / 🐳 docker start / 🐳 docker run 차이**
  - **Docker create :** 도커 이미지에서 새로운 컨테이너를 생성합니다. 그러나 즉시 실행되지는 않습니다
  - **Docker start :** 중지된 컨테이너를 시작합니다. docker create 명령을 사용하여 컨테이너를 만든 경우 이 명령으로 시작할 수 있습니다.
  - **Docker run :** create와 start의 조합으로 새 컨테이너를 생성하고 시작합니다. docker run 명령은 로컬 시스템에서 이미지를 찾지 못하는 경우 Docker Hub에서 이미지를 가져와서 컨테이너를 생성하고 실행합니다.
