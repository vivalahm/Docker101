# Day 2 로컬 환경에서 도커를 활용

## 1일차 복습

- **도커(Docker)란**?

  - **컨테이너** 기반 **가상화** 도구
    -  리눅스 컨테이너 기술인 LXC(Linux Containers) 기반 
  - 애플리케이션을 **컨테이너**라는 단위로 격리하여 실행하고 배포하는 기술 

  

- **가상화** **(Virtualization)** **기술이란**?

  - **하나의 물리적인 컴퓨터 자원**(CPU, 메모리, 저장장치 등)을 **가상적으로 분할**하여 여러 개의 **가상 컴퓨터 환경**을 만들어 내는 기술
  -  이를 통해 물리적인 컴퓨터 자원을 더욱 **효율적**으로 사용할 수 있으며, 서버나 애플리케이션 등을 운영하는데 있어 **유연성**과 **안정성**을 제공 

- **Container** **란** ?
  - 컨테이너는 **가상화 기술** 중 하나 
  - 호스트 운영체제 위에 여러 개의 격리된 환경을 생성 
  - 각각의 컨테이너 안에서 애플리케이션을 실행 

![image](https://user-images.githubusercontent.com/48741014/230754772-4376507a-5c44-4c5a-94d3-cb4e960a9369.png)

![image](https://user-images.githubusercontent.com/48741014/230755264-3141790c-141e-4506-89b3-ed1de051e277.png)

## **Docker CLI** 

- **Download an image from a registry**

  ```bash
   docker pull [OPTIONS] NAME[:TAG|@DIGEST]
  ```

-  **List images** 

  ```bash
  docker images [OPTIONS] [REPOSITORY[:TAG]] 
  ```

- **Create and run a new container from an image** 

  ```bash
   docker run [OPTIONS] IMAGE [COMMAND] [ARG...] 
  ```

-  **Stop one or more running containers** 

  ```bash
   docker stop [OPTIONS] CONTAINER [CONTAINER...] 
  ```

-  **Fetch the logs of a container** 

  ```bash
   docker logs [OPTIONS] CONTAINER 
  ```

- **Remove one or more containers** 

  ```bash
   docker rm [OPTIONS] CONTAINER [CONTAINER...] 
  ```

-  **Remove one or more images** 

  ```bash
   docker rmi [OPTIONS] IMAGE [IMAGE...] 
  ```

  

#### Create and run a new container from an image

- 사용법

```bash
docker run [OPTIONS] IMAGE [COMMAND] [ARG... ]
```

- 예제

```bash
docker run httpd

docker run --name secondContainer httpd docker run -p 8888:80 - ~/wanted/pre-mission/docker-pro-wanted/lecture-material:/us/local/apache2/htdocshttpd
```

## Dockerfile 예제

```bash
FROM httpd: latest
COPY index.html /us/local/apache2/htdocs/index.html
EXPOSE 80
```

2. 이미지 만들기

```bash
docker build -t my-httpd .
```

3. 도커파일로 생성된 이미지로 컨테이너 실행하기

```bash
docker run -d -p 8888:80 my-httpd
```

![image](https://user-images.githubusercontent.com/48741014/230755598-154cd009-24a5-490a-a448-51327b976b7c.png)

![image-20230409141349241](/Users/choihyun-min/Library/Application Support/typora-user-images/image-20230409141349241.png)





![image](https://user-images.githubusercontent.com/48741014/230755688-c87a427e-a6d0-4bbb-b768-b372fbb32ca2.png)

![image](https://user-images.githubusercontent.com/48741014/230755745-df6198b7-755d-44db-a589-4871ef6bc4df.png)

이미지 내에는 많은 라이브러리가 있으므로 무겁다

그래서 도커파일이라는 금형 성계서만 가지고 있다가 도커이미지를 빌드해서 컨테이너에서 실행시켜 쓰는다.





### Day2: 본격적으로 로컬 환경에서 도커를 활용

### **도커 컴포즈란****?** 

- 도커 컨테이너를 일괄적으로 정의하고 제어하는 도구 
-  설정 파일을 도커 CLI로 번역하는 역할 (도커 cli를 직접 치지 않고 이 도커 컴포즈를 통해 설정 파일을 cli로 번역하는 역활)

![image](https://user-images.githubusercontent.com/48741014/230756040-1d822597-3790-49ed-a462-27c036c33bf1.png)

도커 컴포즈는 yml 형식

```yaml
version: '3.0'

services:
  mariadb10:
    image: mariadb:10
    ports:
     - "3310:3306/tcp"
    environment:
      - MYSQL_ROOT_PASSWORD=my_db_passward
      - MYSQL_USER=docker_pro
      - MYSQL_PASSWORD=docker_pro_pass
      - MYSQL_DATABASE=docker_pro
  redis:
    image: redis
    command: redis-server --port 6379
    restart: always
    ports:
      - 6379:6379
  rabbitmq:
    image: rabbitmq:3-management-alpine
    container_name: 'rabbitmq'
    ports:
        - 5672:5672
        - 15672:15672
    volumes:
        - ~/.docker-conf/rabbitmq/data/:/var/lib/rabbitmq/
        - ~/.docker-conf/rabbitmq/log/:/var/log/rabbitmq
    networks:
        - rabbitmq_go_net

networks:
  rabbitmq_go_net:
    driver: bridge

```

```bash
docker-compose -f local-infra.yml up --build
```

위 yml파일 설정하면 redis도 실행하고 마리아 자동으로 환경세팅한다.

## 도커 컴포즈 파일 구성

- version
  - 컴포즈 버저닝
- services
  - 실행하려는 컨테이너들을 정의하는 역할 
  - 이름, 이미지, 포트 매핑, 환경 변수, 볼륨 등을 포함 
  - 해당 정보를 가지고 컨테이너를 생성하고 관리 
  - **image**: 컨테이너를 생성할 때 쓰일 이미지 지정 
  - **build**: 정의된 도커파일에서 이미지를 빌드해 서비스의 컨테이너를 생성하도록 설정 
  - **environment**: 환경 변수 설정, cli에 해당하는 docker run 명령어의 --env, -e 옵션과 동일 
  - **command**: 컨테이너가 실행될 때 수행할 명령어, docker run 명령어의 마지막에 붙 는 커맨드와 동일 
  - **depends_on**: 컨테이너 간의 의존성 주입, 명시된 컨테이너가 먼저 생성되고 실행 
  - **ports**: 개방할 포트 지정, docker run 명령어의 -p와 동일 (포트 포워딩) 
  - **expose**: 링크로 연계된 컨테이너에게만 공개할 포트 설정 
  - **volumes**: 컨테이너에 볼룸을 마운트함 (-v랑 동일)
  - **restart**: 컨테이너가 종료될 때 재시작 정책 
    - no: 재시작 되지 않음 
    - always: 외부에 영향에 의해 종료 되었을 때 항상 재시작 (수동으로 끄기 전까지) 
    - on-failure: 오류가 있을 시에 재시작 
- **network**
- **volume** 
- **config** 
- **secret** 

## **도커 컴포즈 명령어** 

```bash
docker-comopse -f local-infra.yml up -d
```

* **up:** **도커 컴포즈 파일로, ** **컨테이너를 생성하기**
*  **-f:** **도커 컴포즈 파일 지정하기** 
* **-d:** **백그라운드에서 실행하기** 

## **도커 컴포즈 실습** 

#### 도커 CLI 로 여러개 컨테이너 관리하기

**#** **도커 네트워크 리스트 조회** 

```bash
docker network ls
```

- **bridge**: 도커 엔진에 의해 자동으로 생성되는 가상 네트워크. 컨테이너끼리 연결되는 기본 네트워크 
- **host**: 호스트 컴퓨터의 네트워크 인터페이스를 그대로 사용하는 네트워크 
- **none**: 네트워크를 사용하지 않는 컨테이너 

![image-20230409150046710](/Users/choihyun-min/Library/Application Support/typora-user-images/image-20230409150046710.png)

**#** **도커 네트워크 생성** 

```bash
docker network create wordpress_net
```

 **도커 네트워크를 생성하는 명령어** (네트워크의 이름을 지정)

**# mysql db container** **생성**

```bash
docker \
run \
--name app \
-v "$(pwd)/app_data:/var/www/html" \
-e "WORDPRESS_DB_HOST=db" \
-e "WORDPRESS_DB_NAME=wordpress" \
-e "WORDPRESS_DB_USER=docker_pro" \
-e "WORDPRESS_DB_PASSWORD=docker_pro_pass" \
-e "WORDPRESS_DEBUG=1" \
-p 8000:80 \
--network wordpress_net \
wordpress:latest
```

**# wordpress container** **생성** 

```bash
docker \
run \
--name app \
-v "$(pwd)/app_data:/var/www/html" \
-e "WORDPRESS_DB_HOST=db" \
-e "WORDPRESS_DB_NAME=wordpress" \
-e "WORDPRESS_DB_USER=docker_pro" \
-e "WORDPRESS_DB_PASSWORD=docker_pro_pass" \
-e "WORDPRESS_DEBUG=1" \
-p 8000:80 \
--network wordpress_net \
wordpress:latest
```

```yaml
version: "3.0"
services:
db:
image: mysql:latest
volumes:
- ./db_data:/var/lib/mysql
restart: always
environment:
MYSQL_ROOT_PASSWORD: root_pass
MYSQL_DATABASE: wordpress
MYSQL_USER: docker_pro
MYSQL_PASSWORD: docker_pro_pass
app:
depends_on:
- db
image: wordpress:latest
volumes:
- ./app_data:/var/www/html
ports:
- "8000:80"
restart: always
environment:
WORDPRESS_DB_HOST: db:3306
WORDPRESS_DB_NAME: wordpress
WORDPRESS_DB_USER: docker_pro
WORDPRESS_DB_PASSWORD: docker_pro_pass
```

```bash
docker-compose -f docker-compose.yml up --build
```

![image](https://user-images.githubusercontent.com/48741014/230757408-c08fe55a-c645-48f8-ae37-5aca358ffb8a.png)

- 포트 포워딩으로 8000번으로 입력하면 포트 80번으로 서빙 해줘서 웹서버 연결되고 3306포트로 mysql 서버로 접근

- mysql 컨테이너 내부 host volume을 db_data로 매핑