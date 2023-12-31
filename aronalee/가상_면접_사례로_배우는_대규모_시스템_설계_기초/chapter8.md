# 요약

## 단축 URL 로직

```
http://origin_url -> http://url/short_url
```

- 단축 url은 원본 url과 단축 url이 1대1로 매팡된 정보가 필요
- 가장 기본적인 구현 방법: 해시테이블로 url들을 매핑

### 단축 url 생성

1. 원본 url이 요청
2. 단축 url 생성
3. 원본 url과 단축 url을 매핑해서 저장
4. 단축 url 반환

### 단축 url 접속

1. 단축 url 접속
2. 서버에서 단축 url 매핑정보를 확인해 원본 url 조회
3. 조회 완료시 302 혹은 301 상태코드와 location 헤더를 통해 원본 url 로 리다이렉션
4. 접속 완료

### 단축 URL서비스 관점의 301과 302 상태코드의 차이(설계시 캐시 사용 여부)
  
- 301: 리소스의 위치가 영구절으로 location헤더의 값으로 변경
  - 브라우저가 캐싱하기에 한번만 요청한 후의 이후의 모든 요청은 클라이언트단에서 캐싱 처리
  - 1번만 요청하면 이후에는 브라우저 캐시를 이용하므로 클라이언트 정보 수집이 어려움

- 302: 리소스의 위치가 일시적으로 location헤더의 값으로 변경
  - 브라우저 캐싱 없이 접속 요청을 할 때마다 서버에서 반환
  - 항상 요청하기에 클라이언트의 단축 url 요청 횟수, 발생 위치등 클라이언트의 정보를 수집하기 유리함


## 단축 url 생성 전략

`url/short_url` 에서 `short_url` 을 어떻게 만들지 전략이 필요

### 1. 해싱 후 충돌 해소

- SHA, MD5 등 one way function 이라 서버가 아니면 원본 url을 알기 어려움
- 해시의 결과가 요구했던 `short_url` 보다 크면 뒤를 잘라 앞에만 사용
  - colleision이 발생할 수 있기에 충돌이 해소되기 전까지 정의된 문자열을 넣음
  - colleision이 발생하여 저장된 문자열과 새로 만들어진 문자열을 비교하기 위함
- 동작 방식에 오버헤드가 있는 방식

### 2. 인코딩

- base64, base62등 문자열 길이를 압축할 수 있는 알고리즘을 사용
- `short_url`의 길이제한, 사용 가능한 문자셋에 따라 인코딩을 결정
- 인코딩, 디코딩 알고리즘이 공개되어 있으면 원본 url로 디코딩할 수 있음


## 이외의 고려사항

1. 처리율 제한 장치: 단축 url 생성 및 접근을 서버가 감당할 수 있는 만큼만 처리하도록 제한 장치가 필요
2. db규모 확장의 고려: stateful보단 stateless로 설계하여 규모 확장을 용이하게 할 수 있또록 제작
3. db 의 다중화: 매핑 정보를 저장하는 db를 다중화하여 규모 확장성 달성
4. 분석용 데이터: 접속율, 가장 많이보는 url등 비즈니스를 위한 데이터 수집
