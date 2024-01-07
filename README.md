

# user-service
* application.yml
```yaml
server:
  port: 9001

spring:
  application:
    name: user-service

eureka:
  client:
    register-with-eureka: #eureka 에 client 로 등록할지
    fetch-registry: #EUREKA 서버로부터 인스턴스들의 정보를 주기적으로 가져올 것인지를 설정하는 속성입니다. true 로 설정하면, 갱신된 정보를 받겠다는 설정입니다.
    service-url:
      defaultZone: #서버가 가지고 있는 위치값을 지정, 유레카라는 엔드포인트에 현재 우리가 가지고 있는 마이크로 서비스 정보를 등록하겠다.
```


## Discovery service - Client 기동
1. run discovery-service 
2. discovery dashboard 확인
![img.png](./md/image/img.png)
3. run user-service
4. discovery dashboard 확인
![img_1.png](./md/image/img_1.png)


## Multi client 기동-1(IDE)
1. edit configuration -> user-service 2 생성
![img_2.png](./md/image/img_2.png)
2. run user-service 2 
![img_3.png](./md/image/img_3.png)


## Multi client 기동-2(terminal)
1. project root directory 에서 terminal 로 아래 command 실행(run user-service 3)
```shell
./gradlew bootRun --args='--server.port=9003'
```
![img_4.png](./md/image/img_4.png)


## Multi client 기동-3(terminal)
1. project root directory 에서 terminal 로 아래 command 실행(target 폴더 삭제)
```shell
./gradlew clean
```
2. jar 바이너리 파일 생성
```shell
./gradlew bootJar
```
3. /build/libs 디렉토리에 jar 파일 생성확인
4. run user-service 4
```shell
java -jar -Dserver.port=9004 ./build/libs/user-service-0.0.1-SNAPSHOT.jar
```
![img_5.png](./md/image/img_5.png)


## Random port Multi client 기동
1. application.yml
```yaml
server:
  port: 0 # Random port 
```
2. run user-service 1, 2
3. discovery service 확인
   ![img_6.png](./md/image/img_6.png)
   * 반영이 1개만 되어있다.(기대값은 랜덤포트 2개)
     * user-service 이름 문제
     * 127.0.0.1:user-service:0
     * ip:name:port
       * 여기서 port 가 동적 할당 포트가 아닌 applicatiom.yml server.port 에 설정한 0 이라는 포트가 적용됨
       * 그러므로 여러개를 키게 되더라도 이름이 같게 될 수 밖에 없다
   * 해결책
     * application.yml 에 각각의 client 서버를 구분할 수 있도록 추가정보를 입력
4. application.yml 에 user-service instaince id 추가
```yaml
eureka:
  instance:
    instance-id: ${spring.cloud.client.hostname}:${spring.application.instance_id:${random.value}}
```
5. discovery service 확인
![img_7.png](./md/image/img_7.png)