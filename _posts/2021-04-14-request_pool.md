---
layout: post
title:  "Request Pool이란"
author: qqplot
categories: [ DB ]
image: assets/images/3.jpg
beforetoc: "이번에 새로 프로젝트를 들어가게 되었다. 저번 주에 접속 권한을 신청했고, 오늘에서야 승인이 되어 이것저것 설정했다. 새로 프로젝트에 들어갈 때마다 환경설정을 새로 해야 한다. 매번 환경설정을 할 때마다 괴롭다. 왜냐하면 잘 모르기 때문이다. 작년에 와봤던 고객사라 해봤던 설정이지만 여전히 모른다. 그때에는 그냥 그렇게 설정해야만 쿼리를 날릴 수 있으니까 하라는 대로 따라 했기 때문이다. 하지만 이제는 사소한 것 하나라도 기록하고 공부해보려고 한다. 모르면 끝까지 모른다. 이번에도 두 번째 설정이지만 여전히 왜 이렇게 설정해야 하는지는 모르고 있으니 말이다."
toc: true
---





고객사의 데이터 플랫폼이 Impala로 되어 있다. DB Connection(이하 커넥션)을 위해서 DB 서버 주소 설정하고 odbc 드라이버 등 받아서 넣으면 된다. 여기까진 일반 DB와 비슷한데 여기가 특이했던 것은 쿼리 결과를 받아오기 위해서는 request_pool을 설정해야만 가능하다. 



```sql
set request_pool = 'your pool name';
```



Impala에서 리소스 관리자는 요청에 사용할 Pool 이름을 지정하는 것이다. 이는 승인 제어 기능이 활성화된 경우에만 적용된다. 이 설정을 하지 않으면 분명 connection이 되었는 데에도 오류가 반환된다. 잉 뭐지? 





### DB Connection Pool을 아는가

Request Pool은 DB Pool과 같은 역할을 한다. 쿼리를 날리면 컴퓨터는 DB 서버에 연결을 하고 요청하고 그 결과를 가져온 다음에 연결을 끊는다. 이 연결 과정은 굉장히 리소스를 많이 잡아먹는다. Cost가 비싼 연산이라는 것이다. 그래서 DB Connection Pool이라는 것이 사용된다.



매번 새로운 접속은 서버의 퍼포먼스에 영향을 준다. DB를 제어하기 전에 사용자 지정 개수만큼 커넥션을 만들어 놓고 Pool에 넣어 놓는다. 그래서 필요할 때마다 가져다 쓰고 다시 반납한다. 그래서 Pool 이야기를 하면 큐라는 자료구조가 나온다.



![그림 1 commons-pool의 GeneriObjectPool](https://d2.naver.com/content/images/2015/10/helloworld-201508-CommonsDBCP-------1.png)



예를 들어, DB Connection Pool 매니저는 일정 커넥션을 연결하고 있다가 요청이 들어오면 커넥션을 할당한다. 그런데 사용자가 많아져서 요청이 많을 수 있다. 매니저는 가지고 있는 커넥션이 없으면 그 요청은 기다리게 한다. 기존에 요청한 클라이언트는 커넥션을 다쓰면 반납한다. 그리고 반납된 커넥션을 순번이 빠른 사용자에게 할당해준다.



### TPS(Transaction Per Seconds)

트랜잭션이 처리되는 시간은 결국 커넥션 풀의 크기에 따라 달라진다(트랜잭션을 주제로도 조만간 정리해야겠다). 아래 그림을 보면 Pool에 남아있는 커넥션이 있으면 쿼리를 수행하는 시간은 진짜 그 요청을 처리하는 데에 걸리는 시간만을 뜻한다.

![그림 3 사용자 요청의 처리 과정](https://d2.naver.com/content/images/2015/10/helloworld-201508-CommonsDBCP-------3.png)



하지만 남은 커넥션이 Pool에 없다면 대기열에서 기다리는 시간이 추가가 된다.

![그림 5 대기로 인한 성능 저하 발생](https://d2.naver.com/content/images/2015/10/helloworld-201508-CommonsDBCP-------5.png)





### 승인 제어

승인 제어는 동시 쿼리에 제한을 두어 사용량이 많은 클러스터에서 리소스 사용 급증이나 메모리 부족 상태를 방지하는 기능이다. 이 기능을 사용하면 동시 쿼리 수와 각 쿼리에서 사용하는 메모리에 대한 상한을 정할 수 있다. Pool을 넘어서는 추가 쿼리는 취소되거나 느리게 실행되어 경합을 일으키지 않는다. 대신에 이전 쿼리가 완료될 때까지 대기열에 추가되어 기다린다.



현재 실행 중인 쿼리에 대한 임계값 외에도 대기열에 있는 쿼리의 최대 개수에 제한을 두거나 대기 시간을 설정(시간이 지나면 오류 반환)을 할 수도 있다. 이러한 대기열 설정은 "기아 상태" 시나리오를 감지하고 쿼리가 무기한 대기하지 않도록 할 수 있다.



Impala는 기본적으로 지정된 메모리 제한을 초과하는 쿼리를 취소한다. 그래서 한 번에 여러 개의 대규모 쿼리를 실행하면 일부 쿼리를 다시 실행해야 할 수 있다. 이 경우 승인 제어는 클러스터의 전체 메모리가 수용 할 수 있는 만큼만 동시 쿼리를 허용함으로써 전체 워크로드의 안정성과 안정성을 향상시킨다. (오호라!)



이러한 이유로 용량이 큰 테이블을 조회할 때나 복잡한 group_by를 할 때에 가끔씩 메모리 제한 오류가 났었던 것이다! 그래서 `mem_limit`라는 옵션도 있는 것이다.



이 옵션은 Impala의 메모리 제한 선택을 무시할 수 있다.

예를 들어, 리소스 Pool이 다음과 같이 파라미터가 세팅이 되어 있다:

- Minimum Query Memory Limit = 2GB
- Maximum Query Memory Limit = 10GB

만약에 사용자가 14 GB로 MEM_LIMIT 옵션과 함께 쿼리를 제출한다면, Impala는 다음처럼 수행한다:

- 만약 `Clamp MEM_LIMIT Query Option = true` 이면, admission controller는 10GB로 MEM_LIMIT 재설정하고 admission을 시도한다.
- 만약 `Clamp MEM_LIMIT Query Option = false`이면, admission controller는 14GB로 MEM_LIMIT 설정하고 admission을 시도한다.







### 참고

- https://docs.cloudera.com/documentation/enterprise/6/6.3/topics/impala_request_pool.html
- https://docs.cloudera.com/documentation/enterprise/6/6.3/topics/impala_admission.html#admission_concurrency
- https://devbox.tistory.com/entry/JSP-%EC%BB%A4%EB%84%A5%EC%85%98-%ED%92%80-1
- https://d2.naver.com/helloworld/5102792