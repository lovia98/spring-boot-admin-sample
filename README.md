#spring-boot-admin-sample

###  소개  
스프링 부트에서 제공하는 Actuator를 보다 보기 편하게 볼수 있도록 UI를 제공해주는 application이다.
스프링 팀에서 제공해주는 core module은 아니고 Codecentric 이라는 회사에서 만들었다.

###  application 작동 process  
client-side discovery패턴의 방식과 유사하게, 모니터링 대상이 되는 서비스에서 admin 서버를 주기적으로
호출 하면서 자신의 상태를 갱신하는 개념이다.

### 느낀점  
actuator를 활용하여 서비스 상태를 실시간으로 볼 수 있다는 점이 편리하다고 느껴졌다.  
actuator 기능만 활용하기 때문에 딱 그만큼만 정보 확인이 가능하다.  
대용량 서비스에서 기대하는 로깅 검색 기능이나, 정교한 성능 분석을 해주지는 못하지만, 실시간으로 로그 trace를 확인해 볼 수 있으며,  
메일, 슬랙등과 알림을 지원해 주고 있어서 경량 서비스에서는 꽤 유용할 수 있다고 생각 들었다.  

### 참고 문서  
https://docs.spring.io/spring-boot/docs/current/gradle-plugin/reference/html/  
https://www.vojtechruzicka.com/spring-boot-admin/  
https://www.vojtechruzicka.com/spring-boot-actuator/  
