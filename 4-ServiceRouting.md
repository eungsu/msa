# 스프링 클라우드 게이트웨이를 이용한 서비스 라우팅
- 스프링 클라우드를 사용하면 서비스 게이트웨이를 쉽게 구축할 수 있다.
- 스프링 클라우드 게이트웨이에는 서술자(predicate)와 Filter Factories가 내장되어 있다.
- 서술자는 주어진 조건 집합을 충족하는지 확인할 수 있는 객체다.
- 필터를 사용하면 들어오고 나가는 HTTP 요청과 응답을 수정할 수 있다.
- 스프링 클라우드 게이트웨이는 넷플릭스의 유레카 서버와 통합되며, 유레카에 등록된 서비스를 자동으로 경로에 매핑할 수 있다.
- 스프링 클라우드 게이트웨이를 사용하면 애플리케이션의 구성 파일에서 수동으로 경로 매핑을 정의할 수 있다.
- 스프링 클라우드 게이트웨이를 사용하면 필터로 사용자가 정의한 비즈니스 로직을 구현할 수 있다. 스프링 클라우드 게이트웨이를 사용하여 사전 및 사후 필터를 생성할 수 있다.
- 사전 필터를 사용하여 게이트웨이를 통과하는 모든 서비스 호출에 주입할 수 있는 상관관계 ID를 생성할 수 있다.
- 사후 필터를 사용하여 서비스 클라이언트에 대한 모든 HTTP 서비스 응답에 상관관계 ID를 삽일할 수 있다.
