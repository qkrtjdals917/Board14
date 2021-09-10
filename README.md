# 발견 문제점
## 1. 'application.properties'  에서 초기 세팅 수정

> **원인** : DB 과 해당 계정이 다름

```java
spring.datasource.url=jdbc:mysql://localhost:3306/mydb720?useSSL=false&serverTimezone=Asia/Seoul&allowPublicKeyRetrieval=true
spring.datasource.username=myuser720
```
초기 세팅 부분에서 계정과 DB 이름 뒤에 720를 붙혀준다.

## 2. 서버 가동시 mapper 에서 WrtieDTO를 찾지 못함

> **원인** : resultType 의 경로가 실제 WrtieDTO 와 다르게 되어 있음

``` html
<select id="selectByUid" resultType="com.lec.spring.WriteDTO"> 
    ==>
<select id="selectByUid" resultType="com.lec.spring.domain.WriteDTO"> 
```
xml 파일 내부에 selectByUid 의 결과 타입의 경로가 달라 바꿔준다.


## 3. update 를 하게 되면 수정이 성공 한 뒤 view 화면에서 에러 발생

> **원인** : url 이 http://localhost:8093/board/view.do?uid= 인걸로 보아 uid 정보가 넘어가질 않음

``` java
    location.href = "view.do?uid=${uid}"; 
    ==>
    location.href = "view.do?uid=${param.uid}"; 
```
jsp에서 uid 는 DTO 같은 BEAN 으로 가져오는게 아니라 parameter 형식으로 전달이 되므로 앞에 param 를
붙혀줘야 된다.

## 4. 삭제를 할 경우 에러 발생

> **원인** : mapper 내부에 가지고 있는 쿼리문에서 uid 라는 이름의 속성을 테이블내에서 찾을 수 없음

``` xml
    DELETE FROM test_write WHERE uid = #{uid}
    ==>
	DELETE FROM test_write WHERE wr_uid = #{uid}
```
데이터테이블에는 wr_uid 란 이름이므로 uid 란 이름의 변수를 찾으라 하니 에러가 발생했다
