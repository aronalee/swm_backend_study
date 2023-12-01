
여러 DB엔진들이 존재하였으나 RDBMS는 1970년대에 나온 후로 지속적으로 사용된다.
RDBMS도 여러 엔진이 있으나 이 책에서는 MySQL중심이므로 MySQL에 대해서도 설명하겠다.

# 상용 RDB와 MySQL의 구조적 차이

## 구조적 차이

- Oracle: 여러개의 DB서버가 하나의 스토리지를 공유
  - 어느서버에 CRUD를 해도 동일한 결과를 제공
  - 모든 DB서버가 마스터
- MySQL: 하나의 DB서버가 각각 독립적인 스토리지를 점유
  - 일반적으로 마스터-슬레이브 구조를 사용
  - 마스터는 CUD, 슬레이브는 R만 제공
  - 역할에 따라 서버가 다르므로 튜닝한 쿼리의 종류에 따라 어느 서버에 쿼리를 날려야하는지 인지가 필요

## join 알고리즘 지원 기능의 차이

- Oracle: nested loop join, sort merge join, hash join
- MySQL: nested loop join, hash join


