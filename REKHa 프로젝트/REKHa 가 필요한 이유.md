[관련된 좋은 아티클](https://hanbit.co.kr/media/channel/view.html?cms_code=CMS9400468504&cate_cd=001)

서비스의 규모가 커질수록 처리하는 데이터의 양이 늘어난다. 대용량 데이터를 다루는 곳, 예를 들면 대형 온라인 쇼핑몰, 검색엔진, 게임서비스 등등에서 RDB에 부하가 오고 scale up도 한계가 오기 시작했다. 그럼 나머지 답은 scale out 밖엔 답이 없다.
그러나, 설계를 처음부터 다시 하지 않는 한 RDB를 scale out 하기 쉽지 않았다.

그러다보니 scale-out을 처음부터 고려한 NoSql DB를 적용하기 시작했다.
기존의 RDB에서 NoSq DB로 완전히 갈아탄게 아니라, 기존 RDB에 최대한 부하를 줄이고 NoSQL에게 그 역할을 나누어 줬다. 쓰기,수정,삭제 (Create, Update, Delete) 작업, 즉 데이터를 조작해야하는 작업은 RDB에게 맡기고, 통계/검색/일반조회 등등 복잡하거나 간단한 조회용 데이터를 scale out이 가능하고 가벼운 NoSql DB를 사용해서 RDB의 역할을 줄였다.

RDB의 역할을 줄이는 방법중에 다른 기술은, ETL (Extrac Transfer Load) 이라는 기술로 RDB를 통째로 복제해두고 하둡 기능을 사용해서 대량의 데이터를 처리했다. 그러나 이 방법은 즉각적인 결과를 보여주는 기술이 아니기 때문에, 미리 자주 조회될 것 같은 쿼리의 인덱스들을 Redis나 Elastic Search로 데이터를 배치해둠으로써 준실시간 데이터 응답을 가능하게 했다.



