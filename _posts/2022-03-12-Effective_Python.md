---
layout: post
title:  "Effective Python"
categories: [ python ]
tags: [ basic ]
fullview: false
comments: true
---

Effective Python (파이썬 코딩의 기술)

---


# Python을 어떻게 코딩할 것인가

Python으로 많이들 코딩을 한다. 배우기도 쉽고 읽기도 편한 언어이기 때문이다. 하지만 Python스럽게 코딩하는 것은 그저 문법만 익히는 것보다는 좀 더 훈련이 필요하다. 괜찮은 책을 소개받았다. Effective Python이라는 책이다. Effecitve 시리즈로 c++이나 Java는 기존에 책이 있다. 저자는 이에 영감을 받아 작성했다고 한다. 도서관에서 책을 빌렸는데 개정 전이다. 개정판은 좀 더 내용이 보강되어서 거의 두 배의 두께를 자랑한다. 곁에 두고 필요한 내용을 뽑아쓰면 좋을 것 같다.

![img](http://image.kyobobook.co.kr/images/book/large/825/l9791186978825.jpg)



## None을 반환하기보다는 예외를 일으키자
특별한 상황을 알릴 때 None을 반환하는 함수는 오류를 일으키기 쉽다. 왜냐하면 None이나 다른 값(0이나 빈 문자열)이 조건식에서 False로 평가되기 때문이다.

```python
# 0으로 나누는 경우
def divide(a, b):
	try:
		return a / b
	except ZeroDivisionError:
		return None
```

0으로 나누는 경우에 대비해서 다음과 같이 조건문으로 처리할 수 있을 것이다.

```python
result = divide(x, y)
if result is None:
	print('Invalid inputs')
```

그러나 분자가 0인 경우는 어떻게 검사할 것인가? None에 특별한 의미가 있을 때 파이썬 코드에서 흔히 하는 실수다. 이런 상황을 줄이는 방법은 두 가지이다.

```python
def divide(a, b):
	try:
		return True, a / b
	except ZeroDivisionError:
		return False, None
```

```python
# case 1
success, result = divide(x, y)
if not success:
	print('Invalid inputs')

# case 2
_, result = divide(x, y)
if not result:
	print('Invalid inputs')
```

이러한 코드는 여전히 문제가 많다. 호출하는 쪽에서 튜플을 풀어야 하는 불편함이 있다. 또한 그냥 None 반환한 것과 의도치 않게 다르지 않게(case2) 될 수도 있다. 이럴 바에는 차라리 호출하는 쪽에서 잘못된 입력에 대한 예외를 처리하도록 하는 것이 낫다. 또한 조건문으로 예외를 검사할 필요 없어서 더 깔끔해진다.

```python
def divide(a, b):
	try:
		return a / b
	except ZeroDivisionError as e:
		raise ValueError('Invalid inputs') from e

x, y = 5, 2
try:
	result = divide(x, y)
except ValueError:
	print('Invalid inputs')
else:
	print(f'Result is {result:.1f}')

# >>> Result is 2.5
```

## 클로저가 변수 스코프와 상호 작용하는 방법을 알자

→ 말이 좀 어려운데 쉽게 말해서 글로벌/로컬 변수가 작용하는 범위에 대해서 명확히 알자는 것이다.

```python
def sort_priority(values, group):
	def helper(x):
		if x in group:
			return (0, x)
		return (1, x)
	values.sort(key=helper)

numbers = [8, 3, 1, 2, 5, 4, 7, 6]
group = {2, 3, 5, 7}
sort_priority(numbers, group)
print(numbers)

# >>> [2, 3, 5, 7, 1, 4, 6, 8]
```

왜 동작하는가?

- 파이썬은 **클로저(closure)**를 지원한다. 클로저란 자신이 정의된 스코프(범위)에 있는 변수를 참조하는 **함수**다. 바로 이 점 덕분에 helper 함수가 sort_priority의 group 인수에 접근할 수 있다.
- 파이썬에서 함수는 일급 객체(first-class object)다. 즉, 함수를 직접 참조하고, 변수에 할당하고, 다른 함수의 인수로 전달하고, 표현식과 if문 등에서 비교할 수 있다는 의미다. 따라서 sort 메서드에서 클로저 함수를 key 인수로 받을 수 있다.  → 뭔 말? 생각해보면 바깥쪽 함수(sort_priority)의 선언이 끝나지도 않았는데 helper 함수를 쓰고 있다.
- 파이썬에는 튜플을 비교하는 특정한 규칙이 있다. 먼저 인덱스 0으로 아이템을 비교하고 그 다음으로 인덱스 1, 다음은 인덱스 2와 같이 진행한다. helper 클로저의 반환 값이 정렬 순서를 분리된 두 그룹으로 나뉘게 한 것은 이 규칙 때문이다.

우선 순위가 높은 아이템을 발견했을 때 플래그도 같이 리턴해주는 기능을 추가해보자.

```python
def sort_priority2(numbers, group):
	found = False
	def helper(x):
		if x in group:
			found = True # 간단해 보임
			return (0, x)
		return (1, x)
	numbers.sort(key=helper)
	return found

found = sort_priority2(numbers, group)
print('Found:', found)
print(numbers)

# >>> 
# Found: False 
# [2, 3, 5, 7, 1, 4, 6, 8]
```

정렬은 제대로 되었으나 Found 결과가 틀렸다. 파이썬 인터프리터는 다음과 같은 순서로 스코프(범위)를 탐색한다.

1. 현재 함수의 스코프
2. (현재 스코프를 담고 있는 다른 함수 같은) 감싸고 있는 스코프
3. 코드를 포함하고 있는 모듈의 스코프(전역 스코프)
4. (len이나 str 같은 함수를 담고 있는) 내장 스코프

이 중 어느 스코프에도 참조한 이름으로 된 변수가 정의되어 있지 않으면 NameError 예외가 일어난다.

```python
def sort_priority3(numbers, group):
	found = False
	def helper(x):
		nonlocal found
		if x in group:
			found = True
			return (0, x)
		return (1, x)
	numbers.sort(key=helper)
	return found
```

nonlocal 문은 특정 변수 이름에 할당할 때 스코프 탐색이 일어나야 함을 나타낸다. 유일한 제약은 nonlocal이 모듈 수준의 스코프까지는 탐색할 수 없다. 하지만 이러한 방법은 간단한 함수 외에서는 피해야 한다. 권장되는 방법은 `__call__` 이라는 특별한 메소드를 이용하는 것이다.

```python
class Sorter:
	def __init__(self, group):
		self.group = group
		self.found = False

	def __call__(self, x):
		if x in self.group:
			self.found = True
			return (0, x)
		return (1, x)

sorter = Sorter(group)
numbers.sort(key=sorter)
assert sorter.found is True
```

## 리스트를 반환하는 대신 제너레이터를 고려하자

보통 append를 많이 쓸 것이다.

```python
def index_words(text):
	result = []
	if text:
		result.append(0)
	for index, letter in enumerate(text):
		if letter == ' ':
			result.append(index + 1)
	return result

address = 'Four score and seven years ago...'
result = index_words(address)
print(result[:3])

# >>> [0, 5, 11]
```

위의 코드는 두 가지 문제점이 있다.

1. 코드가 복잡하고 깔끔하지 못하다
    1. `result.append` 호출이 많아서 `index + 1` 부분이 중요해 보이지 않는다.
    2. 문자가 130개 가량 있는데 그 중에서 75개 문자만 중요하다.
2. 반환하기 전에 모든 결과를 리스트에 저장해야 한다
    1. 입력이 매우 많으면 프로그램 실행 중에 메모리가 고갈된다
    2. 반면 다음과 같은 제너레이터를 이용하면, 이 함수가 동작할 때 사용하는 메모리는 입력 한 줄의 최대 길이까지다.

```python
def index_words_iter(text):
	if text:
		yield 0
	for index, letter in enumerate(text):
		if letter == ' ':
			yield index + 1

result = list(index_words_iter(address))
```

제너레이터를 정의할 때 유의해야 할 점은 반환되는 이터레이터에 상태가 있고, 재사용할 수 없다는 사실을 호출하는 쪽에서 알아야 한다는 점이다.


# 참고

- [파이썬 코딩의 기술](http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9791186978825)