---
layout: post
title:  "Left outer join 시 주의할 점"
categories: [ database ]
tags: [ impala ]
fullview: false
comments: true

---





### 왼쪽은 그대로 있어야 하는거 아닌가?

LEFT OUTER JOIN을 하면 기준 테이블만 신경을 쓰게 된다. INNER JOIN 만이 기준 테이블에 영향을 줄 것이라고 생각하기 때문이다. 그래서 LEFT OUTER JOIN을 할 때에는 기준 테이블의 행 수가 변하지 않는다고 생각한다. 그러나 알아서 찰떡같이 붙는 마법의 쿼리는 없다.



![img](https://t1.daumcdn.net/thumb/R1280x0.fpng/?fname=http://t1.daumcdn.net/brunch/service/user/1F9Q/image/sJmSZm4zf9SDaAeJG1uLHkONRAc.png)



LEFT OUTER JOIN은 1:1 혹은 N:1의 관계를 갖는 경우에만 행 수가 그대로이다.
반대로 1:N 관계에서 LEFT OUTER JOIN문을 사용하면 기준 테이블의 데이터가 중복된다.
즉 행 수가 뻥튀기된다.



- 원래 생각했던 LEFT OUTER JOIN

![img](https://t1.daumcdn.net/thumb/R1280x0.fjpg/?fname=http://t1.daumcdn.net/brunch/service/user/1F9Q/image/bfiI6NU7Ikr9LmKcIqMpFnYPIUo.jpg)





왼쪽 테이블(팩트)에 1개 있고, 오른쪽 테이블(디멘젼)에 2개가 있다고 하자. 오른쪽 테이블의 컬럼을 SELECT하지 않더라도 왼쪽 테이블에는 치명적인 2개의 행이 생긴다. 데이터가 두 배로 뻥튀기가 되는 것이다. 따라서 1:N 관계에서 LEFT OUTER JOIN을 통해 N에 해당하는 테이블의 컬럼으로 제한 조건을 거는 경우에는 DISTINCT 혹은 GROUP BY문으로 중복을 제거해야 한다.





- 의도치 않은 뻥튀기

![img](https://t1.daumcdn.net/thumb/R1280x0.fjpg/?fname=http://t1.daumcdn.net/brunch/service/user/1F9Q/image/V_zAByXdM8algT2yv1FO_1lB0zY.jpg)





### 쿼리 결과는 언제나 확인할 것

정보계 시스템을 구축하다 보면 별의별 테이블을 만나게 된다. 운영계 시스템의 담당자의 스타일마다 테이블 성격이 매우 달라진다. PK 에러가 나는 것이 싫어서 PK성 컬럼은 있으나 PK를 걸지 않았다거나, PK는 있으나 특별한 의미는 없이 유니크하게만 설정해놓은 테이블도 있다. 그렇기에 당연히 디멘젼 테이블에 중복 데이터가 없을 것이라고 생각하면 안 된다. 운영계 시스템이야 그 시스템의 목적만 잘 동작하고 있으면 되는 것이기에 이를 마냥 원망할 수는 없다.



하지만 수많은 쿼리를 짜다보면 데이터 검증을 할 방법이 막막하다. 특히 비즈니스에 대한 설명을 듣기 힘든 개발 환경에서는 더욱 데이터 품질을 개발자가 일일이 판별하기란 불가능한 일이다. 그래서 흔히 쓰는 방법은 두 가지이다. 행(Row) 수를 세거나(Count) 팩트 테이블의 값의 총합(Sum)이 맞는지 보는 것이다. 이 두 가지가 틀리면 당연히 쿼리를 잘못 짰다거나 테이블에 들어가 있는 데이터가 틀렸다는 것이다.




멍하니 팩트 테이블에 디멘젼을 붙이다가 보면 LEFT OUTER JOIN은 행 수에 당연히 영향을 안 줄 거라는 안일함이 생긴다. 자기 쿼리에 대한 지나친 확신은 데이터 품질을 보증하지 못한다. 언제나 테스트해보는 습관을 들이자.



**결론: LEFT OUTER JOIN이 팩트 테이블에 영향을 준다.**





### 참고

- 프로젝트에서 강한 오라클 PL/SQL 프로그래밍
- 새로 쓴 대용량 데이터베이스솔루션. 1:관계형 데이터베이스 활용 원리편



[원문 보기](https://brunch.co.kr/@qqplot/22)





