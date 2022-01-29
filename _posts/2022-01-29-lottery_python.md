---
layout: post
title:  "직장인, 목표를 찾기 위해 방황할 때"
categories: [ python ]
tags: [ python ]
fullview: false
comments: true
---

파이썬으로 간단한 추첨 프로그램 만들기 


### 파이썬으로 뽑기 프로그램 만들기

연말 행사로 담당 전체 인원이 참여하는 온라인 미팅이 있었습니다. 어쩌다 보니 행사 진행을 돕게 되었는데 내용 중에서 경품 증정식이 있었습니다. 상당히 구미가 당기는 경품이었는데, 1명은 애플 워치이고 15명은 상당한 가격의 고급 펜이었습니다. 이 경품을 어떻게 줄 것인가에 대해 논의를 하다가 추첨을 하자는 의견이 나왔습니다. 그런데 온라인 미팅인지라 제비뽑기으로 진행하기엔 애매했습니다. 

결국 너가 만들어 보라는 숙제(?)가 나와서 얼렁뚱땅 파이썬으로 간단한 뽑기 프로그램을 만들게 되었습니다. 아주 간단한 프로그램이지만 만드는 과정을 정리해서 공유해봅니다. 

<br/>

### 프로그램 기획하기

앞에서는 금방 짠다고 이야기했으나 막상 만드려니 막막하더군요. 단순히 `random` 라이브러리에서 난수로 뽑는 건 간단하지만 보는 맛이 있게 만드려니 상당한 노력이 필요하겠더라구요. 처음 욕심에는 슬롯머신처럼 돌아가는 것을 만드려고 했으나, 시간도 부족하고 일보다 행사가 먼저가 되는 듯하여 포기했습니다. 그래서 간단하게 카드 뒤집기에 착안해서 구성했습니다. 

`tkinter` 라이브러리를 이용해 GUI를 구성했습니다. 파이썬 기본 패키지에 포함되어 있기 때문에 따로 설치가 필요 없어 편리합니다. 그리고 프레임, 버튼 등 GUI 요소가 대부분 이미 구현되어 있어서 편하게 가져다 쓰면 됩니다. 그러나 세부적인 부분까지 커스텀할 여지는 많이 없습니다. 더 예쁘게 만들기를 원한다면 다른 라이브러리도 좋은 게 많습니다. 대표적으로는 `PyQt`나 `PySide`가 있습니다[1]. 

제 로컬에서 사용하는 것이 아니기 때문에 배포를 고민했습니다. 단순 스크립트만 들고 가기에는 파이썬이 설치가 안되어 있거나, 원하는 프로그램이 설치되어 있지 않을 가능성이 컸습니다. 그래서 `PyInstaller` 라이브러리를 이용해서 윈도우 실행 프로그램(exe)을 만들었습니다. 프로그램을 사용할 컴퓨터가 윈도우이기만 하면 파이썬 설치 유무와 상관없이, 실행 파일과 데이터만 메일로 전달하면 됩니다.

자, 이제 세부 내용입니다.

<br/>

### tkinter 라이브러리 

`tkinter` 프로그래밍의 기본적인 뼈대는 다음과 같습니다.


```python
from tkinter import Tk

# tkinter
window = Tk()
...
window.mainloop()
```

`window`라는 메인 객체를 만들고 거기에 프레임, 버튼 등의 요소를 채워넣는 식입니다. 자세한 내용은 [공식 문서](https://docs.python.org/ko/3/library/tkinter.html#module-tkinter)에서 확인하시길 바랍니다. 

`window` 객체에 필요한 속성을 입력해 줍시다.

```python
window.title(msg_title)
window.config(padx=10, pady=10, bg="lightgrey")
window.geometry("550x550")
window.resizable(True, True)
```

`tkinter` 라이브러리는 요소를 배치하기 위한 3가지 방법을 가지고 있습니다[2].

1. pack
2. grid
3. place

좌표값을 입력하거나, grid로 분할하여 해당 셀 위치를 지정한다든가 어떤 방법이든 편한 방식대로 사용하면 됩니다. 저는 원하는 위치에 배치하기 위해 화면을 4 분할로 구성했습니다. `Button`이나 `Entry` 위젯을 담고 있는 `Frame` 요소로 상위 3개 화면을 구성했습니다. 마지막 화면은 `matplotlib` 도표를 표시합니다.

```python
# message frame
message_frame = Frame(window, width=800, height=80)
...
message_frame.pack(pady=10, expand="no", fill="both", anchor="center")

# button frame
bottom_frame = Frame(window, width=800, height=100)
...
button.grid(row=0, column=0)

# card frame
card_frame = Frame(window, width=800, height=100)
...
card_frame.pack(pady=10, expand="no", fill="both", anchor="center")

# matplotlib
figure1 = plt.Figure(figsize=(3,2), dpi=100)
```

각 frame은 pack, grid, pack 방식으로 분할했습니다. 이제 ... 으로 표시된 부분에 위젯을 채워넣으면 됩니다.

최종적으로 구성될 화면은 다음과 같습니다.

![img](https://img1.daumcdn.net/thumb/R1280x0.fjpg/?fname=http://t1.daumcdn.net/brunch/service/user/1F9Q/image/-YY4u3usek6Nkne0J38EEKumV_w.JPG)


<br/>

### 위젯 채워넣기

자, 분할한 3개의 frame에는 위젯을 채워 넣어 봅시다.  

`message frame`에는 사용자에게 보여줄 메시지를 보여주는 역할을 합니다. 프로그램 사용 방법이나, 뽑기 결과를 표시할 겁니다.

메시지는 `Label` 위젯의 `text` 속성으로 표시됩니다. 위젯의 속성은 `configure` 함수를 통해서 바꿔줄 수 있습니다. 사용자가 누르는 버튼에 따라서 표시할 메시지를 새롭게 설정해주는 식으로 구현했습니다.

```python
result = Label(message_frame, text="뽑기 버튼을 눌러주세요!", pady=10)
result.pack(pady=10)

# called from somewhere
result.configure(text="새로운 메시지")
```





`button frame`에는 버튼 기능이 들어갑니다. 실제로 랜덤한 숫자를 뽑는 '숫자뽑기' 버튼과 재 추첨을 위한 '초기화' 버튼을 배치합니다. 그리고 몇 명을 뽑을지를 입력하는 Entry가 있습니다. 원래는 1명만 뽑을 수 있는 기능만 개발했었는데 15명을 일일이 추첨하기 어려워서 한번에 결과를 출력하는 기능이 추가되었습니다. 

`card frame`은 사번을 표시하는 카드가 표시됩니다. 바로 추첨된 결과를 보여주는 것보다는 다섯 자리인 사번을 하나씩 표현하려고 했습니다. 하나씩 패를 뒤집는 맛이 있죠. 해당 프레임은 맨 아래 도표하고 연결이 됩니다. 카드를 열어 보이는 자리 수와 같은 사번을 가진 인원들(생존자)이 아래 표시됩니다. 

<br/>

### 데이터 표현하기

카드 뒤집기를 구현했지만 여전히 쪼는 맛(?)이랄까요? 아직 재미가 부족합니다. 화면이 밋밋하게 느껴졌습니다. 어떻게 하면 더 재밌게 표현이 될까 싶어 가진 데이터를 표현해야겠다는 생각에 미쳤습니다. 

아무래도 제가 속한 조직이 데이터 엔지니어와 분석가들이 모인 곳이라 EDA처럼 도표가 있으면 더 의미가 있을 것 같았습니다. 또 컴퓨터를 워낙 잘 아시는 분들이 많아 '과연 저게 진짜 랜덤일까?'를 의심(ㅋㅋ)을 하시는 분들도 있어, 카드를 열 때마다 실제로 인원이 줄어드는 걸 보여줘서 신뢰성을 높여야겠다고 생각했습니다.

제가 가진 데이터는 다음과 같습니다.

![img](https://img1.daumcdn.net/thumb/R1280x0.fjpg/?fname=http://t1.daumcdn.net/brunch/service/user/1F9Q/image/zpzoLCaY4HR9M1x4UQbF8OfIfBE.JPG)

보시다 싶이 사번, 성명, 호칭, 팀이 Feature로 있는 데이터입니다. 그래서 `matplotlib` 라이브러리를 활용하기로 했습니다. `tkinter` 라이브러리는 이미지를 표시해주는 위젯이 있기 때문에, 이미지를 그려주는 라이브러리와 연동이 가능합니다[3][4].


```python
# matplotlib
figure1 = plt.Figure(figsize=(3,2), dpi=100)
ax1 = figure1.add_subplot(111)
bar1 = FigureCanvasTkAgg(figure1, window)
bar1.get_tk_widget().pack()
df1 = data.groupby('호칭')['사번'].count().sort_values(ascending=True)
df1.plot(kind='barh', legend=False, ax=ax1)
ax1.set_title('호칭별 생존자 수')
```


<br/>

### 기능 붙여넣기

그래픽 요소들은 이제 채워넣었습니다. 그렇다고 하면 사용자와 소통하기 위해서 함수를 만들어 기능을 붙여넣어 봅시다.

프로그램이 처음 실행되면 외부 엑셀 데이터를 읽습니다. 그리고 위젯에서 활용할 변수들을 초기화합니다. 엑셀을 읽는 함수는 `pandas` 라이브러리에서 가져왔습니다. ~~가볍게 만드려고 했는데 `pandas`와 `matplotlib`를 가져오니 점점 무거워졌습니다...~~ 

데이터를 읽고 여러 기능이 공통으로 사용할 global 변수를 선언합니다.


```python
# random sampling
data = read_excel("data.xlsx")
idx_data = [i for i in range(len(data.index))]
emp_id = "00000"
count = 0
msg = ""
num_result = 1

data = data.astype({'사번' : 'str'})
data['num1'] = data['사번'].str.slice(start=0, stop=1)
data['num2'] = data['사번'].str.slice(start=1, stop=2)
data['num3'] = data['사번'].str.slice(start=2, stop=3)
data['num4'] = data['사번'].str.slice(start=3, stop=4)
data['num5'] = data['사번'].str.slice(start=4, stop=5)

raw_data = data
```
<br/>

여러 기능이 있지만 숫자뽑기 기능만 예시로 보여드리겠습니다. 

숫자뽑기 버튼을 누르면 `func_pick()` 함수를 호출합니다. 호출된 함수는 `message frame`의 `Label`의 `text` 속성을 새로운 내용이 들어있는 `msg`로 바꿉니다. `msg` 변수에는 축하합니다라는 내용과 해당 인원의 데이터를 tabulate 형태로 표현해줍니다. 이는 `pandas` 데이터 프레임의 str 형태가 이쁘지 않아 사용해봤습니다.

<br/>

```python
def func_pick():

    global emp_id
    global msg
    global num_result

    num = random.sample(idx_data, num_result)
    emp_id = str(data.iloc[num, 0].tolist()[0])

    print(num_result)

    tmp = data.iloc[num, 0:4]
    msg = "축하합니다!\n" + tabulate(tmp, showindex=False)
    result.configure(text=msg)

```

### 프로그램 테스트하기

기능까지 채워넣었으니 실제로 테스트해봅시다.

숫자뽑기 버튼을 누르면 해당 버튼이 비활성화 되면서 상단 메시지가 변경됩니다. 카드를 뒤집어서 결과를 확인하면 됩니다. 카드를 뒤집을 때마다 같은 번호를 가진 생존자가 하단에 표시됩니다.


![img](https://img1.daumcdn.net/thumb/R1280x0.fjpg/?fname=http://t1.daumcdn.net/brunch/service/user/1F9Q/image/rywGV6r4cnjTrLqTD_EmHhlJbwI.JPG)


다섯 개의 카드를 전부 뒤집으면 축하 메시지와 함께 당첨자가 표시됩니다.


![img](https://img1.daumcdn.net/thumb/R1280x0.fjpg/?fname=http://t1.daumcdn.net/brunch/service/user/1F9Q/image/oDF9x3mlDzKWexT_5LsvqO0kkYM.JPG)


자, 메인 기능은 완성되었습니다. 

부가 기능으로 1명이 아닌 원하는 인원 수를 입력 받고, 한 번에 추첨되어 나오도록 하는 기능도 구현했습니다.

![img](https://img1.daumcdn.net/thumb/R1280x0.fjpg/?fname=http://t1.daumcdn.net/brunch/service/user/1F9Q/image/RuPGb8mlz0_oSddckTby9PudDxA.JPG)

<br/>


처음에는 1명만 나오는 걸로 했는데, 15명을 일일이 뽑기엔 행사 시간이 부족하다고 하여 급하게 추가한 기능입니다.

물론 이대로 끝내도 좋지만 누가 프로그램을 실행해도 망가지지 않도록 예외처리 등도 해줍니다. 사용자가 원하는 순서대로 버튼을 안 누를 수도 있으니, 해당 순서에 맞지 않는 버튼은 비활성화 하는 등 예외 상황에 대비해 줍니다. 

![img](https://img1.daumcdn.net/thumb/R1280x0.fjpg/?fname=http://t1.daumcdn.net/brunch/service/user/1F9Q/image/OUyVEevakPdV0H5kAaS9B0bdb4I.JPG)

<br/>

예를 들어, 데이터가 6개 밖에 없는데 뽑을 인원을 10명을 입력하면 더 작은 숫자를 입력하라는 오류 메시지가 나옵니다. `tkinter` 라이브러리는 `messagebox` 위젯이 구현되어 있습니다. 

이 외에도 숫자 말고 문자를 입력한다든지 여러 상황을 대비해 줍니다. 사실 이런 예외 처리가 힘들기 때문에 사용자에게 값을 입력 받는 기능을 넣는 것이 간단하기만한 작업은 아닙니다. 그래도 이건 웹 프로그램이 아니기 때문에 행사 중간에 꺼지지 않을 정도로만 처리해 둡니다.


### exe 실행파일 만들기

프로그램 코드를 다 작성했다면 이를 배포할 실행파일을 만들어 봅시다. `pyinstaller` 라이브러리를 이용하면 간단하게 사용한 패키지와 함께 exe 파일을 만들 수 있습니다[5].

터미널을 열고 pip으로 라이브러리를 설치합니다.

```sh
pip install pyinstaller
```
<br/>

실행파일은 다음과 같은 명령어를 넣어줍니다.

```sh
pyinstaller random_sample.py
```
<br/>

하지만 위 코드만 입력하면 exe 파일 외에도 필요한 파일들이 같이 생성됩니다.

![img](https://wikidocs.net/images/page/21952/8_7.png)

<br/>

다른 부차적인 파일은 숨기고 exe 파일만 있어도 실행되기를 바라기 때문에 다음과 같이 하나의 파일로 생성합니다.


```sh
pyinstaller -w -F random_sample.py
```
<br/>




<br/>

### 전체 코드

이제 프로그램이 완성되었습니다. 아래는 전체 코드니까 필요하신 분은 가져가서 쓰시면 됩니다. 저는 무사히 연말 행사를 치렀고 나름 소소한 재미를 드렸던 것 같습니다. 애플 워치는 못 받았지만 상당히 좋은 독일제 펜을 얻게 되었습니다. :)

<script src="https://gist.github.com/qqplot/0331bc9732daa23f7fc3eb65347ccda3.js"></script>

<br/>


### 참고

- [1] [[파이썬] GUI (그래픽 인터페이스) 라이브러리 선택](https://showering.tistory.com/150)
- [2] [Tkinter 튜토리얼-레이아웃 관리](https://www.delftstack.com/ko/tutorial/tkinter-tutorial/tkinter-geometry-managers/)
- [3] [Matplotlib Tkinter 연결](https://rootiel.tistory.com/32)
- [4] [Embedding in Tk](https://matplotlib.org/3.1.0/gallery/user_interfaces/embedding_in_tk_sgskip.html)
- [5] [09. 실행파일 만들기 (PyInstaller)](https://wikidocs.net/21952)

---

