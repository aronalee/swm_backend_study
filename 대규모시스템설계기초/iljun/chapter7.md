auto_increment 속성?

→ No! 분산 환경에서 이 접근법은 통하지 않는다. 여러 데이터베이스 서버를 쓰는 경우에는 delay를 낮추기 어렵다.

## 1단계. 문제 이해 및 설계 범위 확정

- ID는 유일해야 한다.
- ID는 숫자로만 구성되어야 한다.
- ID는 64비트로 표현될 수 있는 값이어야 한다.
- ID는 발급 날짜에 따라 정렬 가능해야 한다.
- 초당 10,000개의 ID를 만들 수 있어야 한다.

## 2단계. 개략적 설계안 제시 및 동의 구하기

선택지 4개

- multi-master replication
- UUID(Universally Unique Identifier)
- ticket server
- twitter snowflake 접근법

************************************다중 마스터 복제************************************

데이터베이스의 auto_increment 기능을 활용한다. 다만 다음 ID를 구할 때 1만큼 증가시켜 얻는 것이 아니라, k(현재 사용 중인 데이터베이스 서버의 수)만큼 증가시킨다.

단점

- 여러 데이터 센터에 걸쳐 규모를 늘리기 어렵다.
- ID의 유일성은 보장되겠지만 그 값이 시간 흐름에 맞추어 커지도록 보장할 수는 없다.
- 서버를 추가하거나 삭제할 때도 잘 동작하도록 만들기 어렵다.

********UUID********

UUID는 컴퓨터 시스템에 저장되는 정보를 유일하게 식별하기 위한 128비트짜리 수다.

중복이 생길 가능성이 현저히 낮다.

장점

- UUID를 만드는 것은 단순하다. 동기화 이슈가 없다.
- 각 서버가 자기가 쓸 ID를 알아서 만드는 구조이므로 규모 확장도 쉽다.

단점

- ID가 128비트로 길다.
- ID를 시간순으로 정렬할 수 없다.
- ID에 숫자가 아닌 값이 포함될 수 있다.

******************티켓 서버******************

플리커는 분산 기본 키를 만들어 내기 위해 이 기술을 이용했다.

auto_increment 기능을 갖춘 데이터베이스 서버, 즉 티켓 서버를 중앙 집중형으로 하나만 사용하는 것이다.

장점

- 유일성이 보장되는 오직 숫자로만 구성된 ID를 쉽게 만들 수 있다.
- 구현하기 쉽고, 중소 규모 애플리케이션에 적합하다.

단점

- 티켓 서버가 SPOF(Single-Point-of-Failure)가 된다.

********************트위터 스노플레이크 접근법********************

- sign 비트 : 1비트를 할당한다. 음수와 양수를 구별
- timestamp : 41비트를 할당. 기원 시각(epoch) 이후로 몇 밀리초가 경과했는지를 나타내는 값
- 데이터센터 ID : 5비트를 할당한다. 32개 데이터센터를 지원할 수 있다.
- 서버 ID : 5비트를 할당한다. 데이터센터당 32개 서버를 사용할 수 있다.
- 일련번호 : 12비트를 할당한다. 각 서버에서 ID를 생성할 때마다 이 일련번호를 1만큼 증가시킨다. 1밀리초가 경과할 때마다 0으로 초기화된다.

## 3단계. 상세설계