# 요약

## 분산 시스템을 위한 unique ID 생성 설계


### Case1: 다중 복제

- 서버마다 increment하는 배수를 정하고 해당 배수만큼 id를 증가
  - ex: 서버1은 짝수, 서버2는 홀수
- 정수를 이용함에도 서버끼리 id가 겹치지 않음
- 문제점
  1. 서버 개수의 조정이 어려움

### Case2: UUID

- UUID 알고리즘을 이용해 키를 생성
- 키의 크기: 128bit(32byte)
- 장점: 여러 서버에서 동시에  사용하여도 겹칠 가능성이 매우 적읍
  - 초당 10억개의 UUID를 100년동안 만들면 겹칠 확률이 50%로 증가 => 사실상 없다
- 장점
  - 아이디 생성이 용이
  - unique id 생성기를 서버마다 두어도 중복이 발생하지 않아 서버 조정에도 유리
- 단점
  - id의 길이가 정수에 비해 길이가 김 
  - 시간순으로 정렬이 어려움
  - 숫자+문자의 값으로 구성

### Case3: Ticket Server

- id를 만들어주는 ticket server에게 id 생성을 위임
  - ticket server라는 단일 서버에서 auto increment로 아이디를 자동 생성
- 장점
  - 구현이 용이하며 서버 확장도 쉬움
- 단점
  - SPOF문제가 발생
  - SPOF문제를 해결하기 위해 ticket server를 여러대를 준비하면 동기화 문제가 발생

### Case4: 트위터 snowflake

- ID의 구조를 다음의 값으로 구성
  - sign(1bit): 확장을 위해 유보해놓은 값
  - timestamp(41bit): 시작 시간 이후로 몇초가 경과했는지를 나타내는 timestamp
  - data ceneter unique id(5bit): 어떤 데이터 센터에서 만들어졌는가
  - sequence number(12bit): 1밀리초 내에 1씩 증가하는 unique id. 1밀리초가 지나면 sequcne number가 0으로 초기화


### unique id 생성시 고려사항

- 시간동기화: timestamp같은 시간을 사용하는 경우 여러 서버의 시간을 동일 시간으로 동기화가 필요
- 최적화: sonwflake 키 생성 알고리즘을 그대로 사용하기 보단 상황에 따라 키를 조절하여 불필요한 부분의 최적화가 필요
- HA: id를 생성기는 죽지 않는 HA를 유지해야 함