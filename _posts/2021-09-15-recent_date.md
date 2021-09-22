---
layout: post
title:  "테이블에서 최신 일자의 데이터를 가져올 때 주의할 점"
author: qqplot
categories: [ DB ]
image: assets/images/9.jpg
beforetoc: "최신 일자를 가져오는 쿼리를 짤 때에는 주의해야 한다."
toc: true
---



최신 일자를 가져오는 쿼리를 만들어보자.



우선 테이블에는 일자 컬럼이 없다.

파티션만 있을 뿐이다.

파티션은 총 3개. 년, 월, 일로 나누어져 있다.



데이터 예시 그림



```sql
SELECT MAX(날짜)
  FROM 테이블명
 WHERE 조건
;
```



```sql
SELECT *
  FROM (
   SELECT *
     FROM 테이블명
    ORDER BY 날짜 DESC
  )
LIMIT 1
;  
```

최신 일자 하나만을 가져오기 위해 전체 테이블을 정렬하는 바보 짓이다.

데이터가 엄청 크다고 하면 엄청나게 리소스를 잡아 먹을 것이다.







```sql
SELECT MAX(년_파티션), MAX(월_파티션), MAX(일_파티션)
  FROM 테이블명
;
```



아무 생각 없이 이렇게 만들었다.



```sql
SELECT 
       A.ID
     , A.Column1
     , A.Column2
  FROM <스키마>.<테이블명> AS A
 WHERE CONCAT(CAST(년_파티션 AS STRING), 
              LPAD(CAST(월_파티션 AS STRING), 2, '0'), 
              LPAD(CAST(일_파티션 AS STRING),2,'0'))
     = (SELECT MAX(CONCAT(CAST(P_BASIS_YYYY AS STRING), LPAD(CAST(P_BASIS_MM AS STRING), 2, '0'), LPAD(CAST(P_BASIS_DD AS STRING),2,'0')))

                   FROM BDPL200.L2FYT_SCOR_BLD_LTR_RULE_H )
```



```sql
SELECT 
```

