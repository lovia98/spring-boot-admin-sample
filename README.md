# spring-boot-admin-sample

###  소개  
스프링 부트에서는 application의 상태 정보를 제공해 주는 actuators 기능을 제공해 주는데,  
actuator는 json형태로 제공해주어서, 한눈에 보기에는 어려움이 있고 정보를 제공 받고 싶을 때 마다 actuator를  
호출해줘야 하는 불편함이 있다. 
spring boot admin은 이런 불편함을 보안해 주기 위해 보기 편한 UI를 제공해주는 application 서비스이다.
스프링 팀에서 제공해주는 core module은 아니고 `Codecentric` 이라는 회사에서 만들었다.

###  application 작동 process  
client-side discovery패턴의 방식과 유사하게, 모니터링 대상이 되는 서비스에서 admin 서버를 주기적으로
호출 하면서 자신의 상태를 갱신하는 개념이다.

### 느낀점  
* actuator를 활용하여 서비스 상태를 실시간으로 볼 수 있다는 점이 편리하다고 느껴졌다.  
* actuator 기능만 활용하기 때문에 딱 그만큼만 정보 확인이 가능하다.  
* 대용량 서비스에서 기대하는 로깅 검색 기능이나, 정교한 성능 분석을 해주지는 못하지만, 실시간으로 로그 trace를 확인해 볼 수 있으며,  
 메일, 슬랙등과 알림을 지원해 주고 있어서 경량 서비스에서는 꽤 쓸모있어 보인다.

### 참고 문서  
https://docs.spring.io/spring-boot/docs/current/gradle-plugin/reference/html/  
https://www.vojtechruzicka.com/spring-boot-admin/  
https://www.vojtechruzicka.com/spring-boot-actuator/  

---

## 설정 guide  

### [Admin server 설정]

1. spring-boot-admin-server dependency추가  

  **build.gradle**
  ~~~
    ext {
        set('springBootAdminVersion', '2.1.3')
    }
    
    dependencies {
        implementation 'org.springframework.boot:spring-boot-starter-web'
        implementation 'de.codecentric:spring-boot-admin-starter-server'
        testImplementation 'org.springframework.boot:spring-boot-starter-test'
    }
    
    dependencyManagement {
        imports {
            mavenBom "de.codecentric:spring-boot-admin-dependencies:${springBootAdminVersion}"
        }
    }
  ~~~

2. port 8090 (로컬에서 테스트 하는거라 기본 포트를 피하기 위해서)

  **application.yml**
  ~~~
    server:
      port: 8090
  ~~~

3. http://localhost:8090 확인
  - admin ui 화면이 펼쳐지고 admin server에 report하는 서비스가 아직 없으므로 확인할 어플리케이션이 0이라고 나온다.

![spring-boot-admin-first](https://user-images.githubusercontent.com/6405201/54019139-c8887d00-41cd-11e9-8987-e3316fbbc740.png)

---

### [client(모니터링 대상 서비스) 설정]

1. spring-boot-admin-client dependency추가  

  **build.gradle**
  ~~~
    ext {
        set('springBootAdminVersion', '2.1.3')
    }
    
    dependencies {
        implementation 'org.springframework.boot:spring-boot-starter-web'
        implementation 'de.codecentric:spring-boot-admin-starter-client'
        testImplementation 'org.springframework.boot:spring-boot-starter-test'
    }
    
    dependencyManagement {
        imports {
            mavenBom "de.codecentric:spring-boot-admin-dependencies:${springBootAdminVersion}"
        }
    }
  ~~~

2. 웹서비스 상태를 보내줄 어드민 서버 url를 셋팅해준다. 

  **application.yml**
  ~~~
    spring:
     boot:
      admin:
       client:
        url: http://localhost:8090
  ~~~

3. 여기까지 설정하면 정보 확인이 가능한 application 리스트에 추가가 되어 어드민에서 확인이 가능하다.  
   하지만, 기본적인 health 정도만 확인이 가능한데, client서비스에서 외부로 노출할 actuactor를 추가로 설정해 주어야 한다.       

![spring-boot-admin-applications](https://user-images.githubusercontent.com/6405201/54019489-ab07e300-41ce-11e9-8fa5-6fabc2b8080f.png)  

![spring-boot-admin-init](https://user-images.githubusercontent.com/6405201/54019226-04bbdd80-41ce-11e9-9a21-7dd6d732d67b.png)


4. shutdown은 비노출로 설정하고 제공받고자 하는 정보를 제공하는 actuactor를 설정해 준다.

  **application.yml**
  ~~~
    management:
      endpoints:
    web:
      exposure:
        include: health, metrics, loggers, logfile, heapdump
        exclude: shutdown
  ~~~

5. 'detail'이라는 메뉴에 추가적인 세부 항목이 생긴것이 보인다.

![spring-boot-admin-detail](https://user-images.githubusercontent.com/6405201/54019698-28cbee80-41cf-11e9-9470-ce6e20fa1a65.png)

![spring-boot-logger](https://user-images.githubusercontent.com/6405201/54019705-2b2e4880-41cf-11e9-9560-a2ea3c5423ca.png)


---

### [어드민 서버 접근 및 웹서비스 actuactor 접근 security적용 하기]
