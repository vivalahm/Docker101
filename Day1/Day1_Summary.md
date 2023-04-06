# 1일차

도커 설문 결과?

올해 도커는 깃과 같은 기본 도구로 보는 편이다.

도커는 현재 개발자에게는 선택이 아닌 기본툴로서 개발자들이 사용중이다.

따라서 챌린지 강의를 도커로 선정함

질문 VM과 컨테이너의 차이는?

- ppt 21번에 나옴
- vm은 격리를 시키기 위해 게스트os를 만들고 그 os 위에 라이브러리와 앱을 실행 시킴
- vm의 게스트 os는 디바이스의 리소스를 접근을 할때 하이퍼 바이저를 통해 리소스에 접근
- 동일한 게스트 os가 만들어지는데 이때 오버헤드를 제거한게 컨테이너이다.
- 다른점은 VM은 하이퍼바이저가 필요하지만 컨테이너는 컨테이너 엔진위에서 실행된다.

베이스 이미지 위에 만든 파일을 이미지로 만들어 볼수 있다함

실습해본다함.

Q.도커는 로컬의 OS에 종속되는 것 인가?

A.도커 컨테이너는 로컬 OS위에 컨테이너 엔진을 통해 실행이 된다함.

도커 엔진을 사용하기 위해 맥이나 윈도우의 하이퍼바이저가  OS가 리눅스가 아니라도 돌리게 해줌

https://docs.docker.com/engine/reference/run/

도커 레퍼런스를 많이 읽어봐라함

[Cli명령](https://www.notion.so/Cli-a04aba0791794a20b453aadb47e50b2d)

![1](https://user-images.githubusercontent.com/48741014/230269784-c1a2ca74-c094-4da6-93a9-a636261ceaa9.png)

포트 매핑

![2](https://user-images.githubusercontent.com/48741014/230269789-26eb8858-ce1c-4d6b-8d6e-275877d60981.png)

포팅을 통해 원하는 포트로 설정가능

기본 80 포트를 앞의 8888포트로 변경한다는 뜻 만약 안쓰면 그냥 80 포트로 지정됨

즉 -p는 포트 매핑이다.

8888:80 는 즉 뒤에 80포트를 8888로 매핑함

이때 -p는 port를 의미함

```java
docker run -p 8888:80 
```

![3](https://user-images.githubusercontent.com/48741014/230269791-1924f1ac-e58c-49da-9bce-f979bda89e42.png)

도커 호스트 파일 시스템을 도커 로컬 파일 시스템으로 볼륨을 변경하는 방법은 다음과 같음

-v 명령을 써서 볼륨(저장 매체를 설정 가능) volume의 약자임

```java
docker run -p 8888:80 -v ~/httpdtest:/usr /local/apache2/htdocs httpd
```

~/httpdtest는 내 현재 위치의 로컬 파일 시스템

```java
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    docker pro wanted
</body>
</html>
```

https://github.com/drum-grammer/docker-pro-wanted/blob/main/lecture-material/first/cli.md

- cli 명령 목록

## Dockerfile 활용

1. Dockerfile 예제

```
FROM httpd:latest COPY  index.html /usr/local/apache2/htdocs/index.html EXPOSE 80
```

1. 이미지 만들기

```
docker build -t my-httpd .
```

1. 도커파일로 생성된 이미지로 컨테이너 실행하기

```
docker run -d -p 8888:80 my-httpd
```

도커 빌드를 통해 로컬에 Dockerfile을 이미지로 만들고 이를 run을 통해 이미지를 컨테이너 인스턴스로 만들수 있다.

도커 FROM에 들어가는 이미지는 도커 레지스토리에서 알아서 찾아오고 없으면 도커 허브에 사용

도커 컴포즈는 여러개의 컨테이너를 실행 시키는 방법으로 다음에는 도커 컴포즈를 쓴다함

로컬에 한서비스를 컨테이너로 띄우는거 다음 시간에 해본다함.

도커를 활용하는 클라우드 서비스가 뭐가있고 aws ecs를 통해 개인적인 백엔드 서버를 배포하는것 까지 한다함.

쿠버네티스에 대한 설명은 하지만 실습은 안함

AWS ECS 실습 한다함.

ECS 실습은 돈이 따로 드니 프리크레딧 받아라함 프리 티어 받아야함.

처음 도커 pull 이미지를 통해 도커 허브에서 받아옴

```java
docker pull httpd 
```

: 뒤에는 버전을 적는다.

docker pull httpd:latest

안 적으면 그냥 기본으로 최신 버전을 받아온다.

docker stop을 해서 컨테이너를 멈춤

사용중인 컨테이너는 삭제 불가능

docker ps -a를 통해 실행한 모든 프로세스를 조회가능

기본 호스트 파일 시스템의 index.html을 바꾸는 방법은 두가지

명령어를 통해 바꾸는 방법과

dockerfile을 통해 바꾸는 방식이 있다.

첫번째 명령어로 바꾸는 방법은

로컬에 수정한 index.html 파일 만들고

docker run -v ~/httpdtest:/usr /local/apache2/htdocs httpd

노란색은 내 로컬 파일시스템을 나타내고 뒤에 초록색은 기본 호스트 파일 시스템으로 -v를 통해 매핑 시킨다.

도커 파일을 만들때

FROM 도커 이미지를 통해서 베이스 이미지 지정

copy는 index.html을 해당파일에 넣어줘란의미

카피한 index.html을 서빙

index.html /usr/local/apache2/htdocs/index.html

노란색이 초록색 index.html 대치

expose 80은 80 포트를 열어줘라는의미고

Q. 이론 수업때 도커는 하이퍼바이저 없이 컨테이너 엔진을 사용한다고 했는데

OS가 리눅스가 아니어도 하이퍼바이저가 알아서 해준다고 했는데 그게 무슨 의미인지 이해가 안간다 질문함

A.컨테이너 엔진이 사실은 리눅스 커널 기반인데 리눅스 커널 기능이 chroot namespace cgroup으로 만들어지는데 이는 리눅스에서 활용하려고 만들어졌는데 맥이나 윈도우에서도 쓰고 싶은 경우 이경우 하이퍼바이저가 동작해서 구동해줌 알아야하는건 컨테이너는 리눅스 기반이고 리눅스 커널을 활용해서 만들어졌다.

![5](https://user-images.githubusercontent.com/48741014/230269793-655dbf66-5278-45d1-b22b-7fbdc85a7503.png)

https://velog.io/@msung99/CICD-Jenkins-Docker-를-활용한-SpringBoot-배포-자동화-구축

마지막 네번째 강의때 진행해본다함









