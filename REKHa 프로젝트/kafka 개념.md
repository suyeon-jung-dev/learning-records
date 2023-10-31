# TODO
[Naver D2 - 대용량 스트리밍 데이터 실시간 분석](https://d2.naver.com/helloworld/7731491)
- 위 글 정리
- 네이버 D2 에서 kafka 관련 글 더 찾아 읽기 (https://d2.naver.com/search?keyword=kafka)
- https://team-platform.tistory.com/11
- [소프트웨어 엔지니어가 알아야 할 로그에 대한 모든 것](https://medium.com/rate-labs/%EC%86%8C%ED%94%84%ED%8A%B8%EC%9B%A8%EC%96%B4-%EC%97%94%EC%A7%80%EB%8B%88%EC%96%B4%EA%B0%80-%EC%95%8C%EC%95%84%EC%95%BC-%ED%95%A0-%EB%A1%9C%EA%B7%B8%EC%97%90-%EB%8C%80%ED%95%9C-%EB%AA%A8%EB%93%A0-%EA%B2%83-11513af8b998)
- [데이터 수집 단계를 처음부터 구현해 보기](https://gonigoni.kr/posts/collecting-data-from-scratch/)
- [ELK + Apache Kafka 활용한 로그 모니터링](https://codezip.tistory.com/677)
- [29CM 로그 수집 시스템 소개](https://medium.com/29cm/29cm-%EB%A1%9C%EA%B7%B8-%EC%88%98%EC%A7%91-%EC%8B%9C%EC%8A%A4%ED%85%9C-%EC%86%8C%EA%B0%9C-e7955d7deec6)



# 참고자료
[RedHat의 kafka 공식 문서](https://www.redhat.com/ko/topics/integration/what-is-apache-kafka)
[실시간 데이터 수집의 강자 '카프카'](https://brunch.co.kr/@devapril/9)
[k8s에 kafka를 올리는 게 좋을까?](https://sencia.tistory.com/3)

# 공식 링크
[Apache kafka GitHub Link](https://github.com/apache/kafka)
[Apache kafka 공식 홈페이지](https://kafka.apache.org/)



![[Pasted image 20231021172017.png]]


> Apache kafka - A distributed sreaming platform

[RedHat - Apache Kafka란 무엇일까요?](https://www.redhat.com/ko/topics/integration/what-is-apache-kafka)
	- 실시간으로 기록 스트림을 publish, subscribe, store 할 수 있는 *분산형 데이터 스트리밍 플랫폼*
# 카프카 개념
- [실시간 데이터 수집의 강자 '카프카'](https://brunch.co.kr/@devapril/9)
	- 링크드인이 처음 개발
		- [[한빛 미디어 - 카프카 탄생배경]]
	- 웹사이트, 애플리케이션, 센서 등에서 수집한 [[데이터 스트림]]을 실시간으로 관리하기 위한 오픈소스 시스템
	- 대용량 실시간 로그 처리에 특화되어 설계된 메시징 시스템.
		- 메시지를 [[토픽]]으로 분류 가능 
		- 짧은 시간 안에 엄청난 양의 데이터와 다양한 유형의 데이터를 실시간으로 수집가능
	- 서비스간 의존성을 제거
		- ifland에서 Azure Service Bus 를 통해 받은 사용자의 행동 패턴을 분석해 이벤트 발생하고, 서비스간 데이터 흐름을 처리했던 것을 생각해보자.
## History
[출처: 데브원영 유튜브 채널](https://www.youtube.com/watch?v=waw0XXNX-uQ&list=PL3Re5Ri5rZmkY46j6WcJXQYRlDRZSUQ1j)
- 과거 아키텍처 에서는 서비스간의 의존성이 너무 높았다.
  ![[Pasted image 20231031205140.png]]
위 사진처럼, data의 전송라인이 복잠해짐에 따라 CI/CD 도 어려웠을 뿐만 아니라, 작은 부분이라도 데이터 규격 변경시 유지보수가 매우 어려웠다.
- 카프카는 과거의 서비스간 응집력을 약하게 만들어 줬다.
  ![[Pasted image 20231031205445.png]]
예를들면, 클릭로그나 결제로그들이 맨 앞단에서 들어오면 카프카가 대량의 모든 데이터들을 받아 필요한 곳(로그 모니터링 시스템, 로그 적재 시스템 등..)에 데이터를 분산시켜준다. 이렇게 서비스간 응집력을 낮추고 복잡했던 데이터 흐름을 간소화시켰다.


# 동작방식

![그림 1 Kafka 클러스터와 KafkaProducer, KafkaConsumer](https://d2.naver.com/content/images/2022/01/KafkaClientInternalsNetworkClient-01.png)

[실시간 데이터 수집의 강자 '카프카'](https://brunch.co.kr/@devapril/9)
1. producer가 메세지를 broker에게 전달. 
2. broker는 메세지를 구분해서 해당 토픽으로 전달
3. consumer(subscriber)가 토픽에서 메세지를 가져옴

- 고 가용성 (Fault Tolerance)
	- 서버와 서버간 데이터 전송중 서버가 다운되었을때, 데이터 손실 없이 카프카 토픽에 쌓여있던 데이터와 송수신 이력을 가지고 재전송/재수신 한다.
	- kafka의 broker 한대가 장애발생시에도, Replication Broker 를 둬서 가용성을 보장한다.
	- 이렇게 데이터 파이프라인을 구성하는데 있어서 카프카의 고가용성이 큰 역할을 한다.
- 낮은 지연과  높은 처리량으로 많은 데이터를 효과적으로 처리할 수 있다.
	- >> 파티션 갯수와 Consumer 갯수를 늘려 병렬처리해서 데이터 처리량을 늘릴 수 있다.
	- 빅데이터 처리에 능하다. (kafka를 통해 Hadop HDFS으로 데이터 적재)
	- 

출처) 네이버 d2 - 실시간 데이터 분석 플랫폼 구조![그림 1 실시간 데이터 분석 플랫폼 구성](https://d2.naver.com/content/images/2016/04/helloworld_1525-01.png)


# 적용사례
[실시간 데이터 수집의 강자 '카프카'](https://brunch.co.kr/@devapril/9)
1. 카프카로부터 온 메세지를 HDFS에 저장해 분석 및 리포팅에 사용하는 경우
2. 실시간 스트림 프로세싱에 사용하는 경우

# 쿠버네티스와 kafka > 상희님 봐주세요~!
[k8s에 kafka를 올리는 게 좋을까?](https://sencia.tistory.com/3)

[RedHat - Kafka 서비스란?](https://www.redhat.com/ko/topics/integration/what-is-a-kafka-service)
Apache Kafka 데이터 브로커는 stateful 상태. 재시작시 보존되어 한다 (기록이 손실되면 안된다.)
스케일링할때 주의깊게 오케스트레이션 해야한다. 
>그래서 여견이 안될경우 관리형 클라우드 서비스를 선택하는데, REKHa 프로젝트에서는 채택하기 어려우므로 까다롭게 스케일링 하더라도 직접 해보자.

[RedHat - Apache Kafka란 무엇일까요?](https://www.redhat.com/ko/topics/integration/what-is-apache-kafka)
## 쿠버네티스로 Apache Kafka 애플리케이션을 확장하는 방법
쿠버네티스는 Apache Kafka에 이상적인 플랫폼?
- Apache Kafka 배포, 구성, 관리, 사용을 간소화 할 수 있음
> 쿠버네티스를 사용하면 kafka 를 scale out 하기에도 좋고 배포하기에도 좋다는 것 같다. 근데 위에 k8s에 kafka를 올리는데 회의적인 글 [k8s에 kafka를 올리는 게 좋을까?](https://sencia.tistory.com/3)은 뭘까?




