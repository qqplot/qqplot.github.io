---
layout: post
title:  "[LeetCode] 문자열 조작 (1)"
categories: [ python ]
tags: [ python, leetcode ]
fullview: false
comments: true
---

*연습, 연습, 그리고 연습 - LeetCode 100문 풀기*


---

### LeetCode 819

문자열을 전처리하고 List의 요소에 대한 개수를 파악하는 문제다.

<br/>

#### 나의 풀이


```python
from typing import List
from collections import Counter
import re

class Solution:
    def mostCommonWord(self, paragraph: str, banned: List[str]) -> str:
        
        p = paragraph.lower()
        p = re.sub("[!?';,.]", ' ', p)
        p = re.sub("[  ]", ' ', p)
        p_counter = Counter(p.split())

        if len(banned) > 0:
            for word in banned:
                del p_counter[word]
                
        return p_counter.most_common(1).pop(0)[0]

```

데이터를 전처리하기 위해서 정규표현식을 사용했다. 처음에는 문제에 나와 있는 특수 문자를 ''로 대체해서 지우기만 생각했었다. 그런데 콤마(,)가 있고 공백이 없는 문자열도 있어서 ' '으로 대체하고 공백이 2개인 '  '를 공백 1개로 바꿨다. 그리고 `collections`의 `Counter` 메소드를 사용해서 빈도를 체크했다. 그리고 `banned`에 들어있는 요소는 지웠다.

<br/>
<br/>

#### 생각지 못한 부분

```python
words = [word for word in re.sub(r'[^\w]', ' ', paragraph)
    .lower().split()
        if word not in banned]
```

전처리 부분에서 python의 리스트 컴프리헨션을 이용해서 한 번에 `words` 리스트를 만들어 낸 것이 인상적이었다. 그리고 정규표현식에서도 not처럼 쓰이는 `^`와 단어를 표시하는 `\w`를 사용하면 된다는 것은 마음에 넣자. 좋은 아이디어이다. return 값이 리스트이기 때문에 `.` 으로 이어나가는 것도 굉장히 낯설다.      

<br/>


```python
counts = collections.defaultdict(int)
for word in words:
    counts[word] += 1
```
`defaultdict`을 활용하는 것이 인상적이다. 딕셔너리를 생성할 때에 키가 없을 때에 항상 if 조건문으로 처리했었는데, 이제부터 구현되어 있는 객체를 사용하면 될 것 같다.   
          
<br/>          

```python
return max(counts, key=counts.get)
```


`max` 함수에서 key로 `defaultdict()`의 get을 이용해서 `argmax`를 구현한 부분이 특이하다. `get` 함수는 key를 이용해서 value를 가져오는 기능을 수행한다. 여기서는 value가 count이므로 최빈 단어를 출력하는 것이다.   

이왕 이렇게 된거 공식문서에서 max가 인자로 iterable을 받는 것을 확인해보자.   

[내장함수 - 파이썬 공식 문서 3.10.2](https://docs.python.org/ko/3/library/functions.html#max)


> **max(iterable, \*[, key, default])**
> **max(arg1, arg2, \*args[, key])**

> iterable 에서 가장 큰 항목이나 두 개 이상의 인자 중 가장 큰 것을 돌려줍니다.
>
> 하나의 위치 인자가 제공되면, 그것은 이터러블 이어야 합니다. iterable에서 가장 큰 항목을 돌려줍니다. 두 개 이상의 위치 인자가 제공되면, 위치 인자 중 가장 큰 것을 돌려줍니다.
>
> 선택적 키워드-전용 인자가 두 개 있습니다. key 인자는 list.sort() 에 사용되는 것처럼 단일 인자 순서 함수를 지정합니다. default 인자는 제공된 iterable이 비어있는 경우 돌려줄 객체를 지정합니다. iterable이 비어 있고 default 가 제공되지 않으면 ValueError 가 발생합니다.
> 
> 여러 항목이 최댓값이면, 함수는 처음 만난 항목을 돌려줍니다. 이것은 sorted(iterable, key=keyfunc, reverse=True)[0] 와 heapq.nlargest(1, iterable, key=keyfunc) 같은 다른 정렬 안정성 보존 도구와 일관성을 유지합니다.
>
>_버전 3.4에 추가: default 키워드-전용 인자._
>
>_버전 3.8에서 변경: key는 None 일 수 있습니다._


--- 

<br/>
<br/>

### LeetCode 49

문자열 배열을 입력 받아서 애너그램 단위로 그룹화 하는 문제이다.
그룹화 하는 방법에 대해서 고민이 되었던 부분이다. `set` 객체를 활용했었는데 `dict`로 하면 훨씬 편리하다는 것을 알게되었다.


#### 나의 풀이

틀린 부분이다. `set` 객체로 하다 보니 중복 캐릭터가 들어오면 다른 그룹이여야 하는 문자열이 같은 그룹으로 묶이는 문제가 생긴다.

> strs = ["ddddddddddg","dgggggggggg"]

```python
class Solution:
    def groupAnagrams(self, strs: List[str]) -> List[List[str]]:
        answer = []
        list_new_s = []
        for s in strs:
            list_s = list(Counter(s))
            new_s = ''.join(sorted(list_s))
            list_new_s.append(new_s)

        set_new_s = set(list_new_s)

        for new_s in set_new_s:
            group = []

            for i in range(len(list_new_s)):
                if list_new_s[i] == new_s:
                    group.append(strs[i])

            answer.append(group)
            
        return answer
```

<br/>

#### 생각하지 못한 부분

```python
class Solution:
    def groupAnagrams(self, strs: List[str]) -> List[List[str]]:
        anagrams = defaultdict(list)

        for word in strs:
            anagrams[''.join(sorted(word))].append(word)        
        return anagrams.values()
```

`defaultdict`을 활용하는 것이 익숙치가 않다보니 신기하다. 인자로 타입을 받으며 딕셔너리 객체와 같은 기능이나 키가 없을 때 에러를 뱉는 것이 아니라 default로 생성되는 키로 지정한 타입의 객체를 생성해준다. 예를 들어, `list`면 빈 리스트를 반환하고 `ìnt`면 0이 default로 설정되는 식이다.

[공식 문서 - defaultdict](https://docs.python.org/ko/3/library/collections.html?highlight=defaultdict#collections.defaultdict.default_factory)


> list를 default_factory로 사용하면, 키-값 쌍의 시퀀스를 리스트의 딕셔너리로 쉽게 그룹화 할 수 있습니다:
>
>\>\>\> s = [('yellow', 1), ('blue', 2), ('yellow', 3), ('blue', 4), ('red', 1)]
>\>\>\> d = defaultdict(list)
>\>\>\> for k, v in s:
>\>\>\>     d[k].append(v)
>\>\>\>
>\>\>\> sorted(d.items())
>\>\>\>`[('blue', [2, 4]), ('red', [1]), ('yellow', [1, 3])]`
>
> 각 키가 처음 발견될 때, 아직 매핑에 있지 않게 됩니다; 그래서 default_factory 함수를 사용하여 항목이 자동으로 만들어지는데, 빈 list를 반환합니다. 그런 다음 list.append() 연산이 값을 새 리스트에 추가합니다. 키를 다시 만나면, 조회가 정상적으로 진행되고 (해당 키의 리스트를 반환합니다), list.append() 연산은 다른 값을 리스트에 추가합니다. 이 기법은 dict.setdefault()를 사용하는 동등한 기법보다 간단하고 빠릅니다:
         
<br/>
리스트의  정렬 방법도 정리하자.

`sorted()` 함수는 내장함수로서 return 값이 리스트이다. 이외에도 헷갈리는 것이 리스트 자료형이 제공하는 `sort()` 메소드이다. 이는 리스트 자료형에 들어있는 함수로서 ruturn 값이 None이다. 단지 해당 메소드를 호출하면 그 리스트를 제자리에서 정렬해주는 것이다.


<br/>


### 참고

- [파이썬 알고리즘 인터뷰](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9791189909178&orderClick=LEa&Kc=)
- [파이썬 공식문서](https://docs.python.org/ko/3/)

