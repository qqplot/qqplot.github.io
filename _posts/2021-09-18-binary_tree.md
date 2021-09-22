---
layout: post
title:  "python으로 이진 트리 구현하기"
author: qqplot
categories: [ ComputerScience ]
image: assets/images/12.jpg
beforetoc: "이진 트리를 구현해보자"
toc: true
---





### 이진 트리란

트리는 **노드**(Node)와 **엣지**(Edge)로 구성된다. 

노드는 동그라미이고 엣지는 선이다. 노드는 자식을 가질 수 있다. 

맨 꼭 대기에 있는 1번 노드는 **루트**(Root)라고 한다. 루트 노드의 **깊이**를 1로 하고 한 단계 내려가면 깊이가 1만큼 증가한다.



![image-20210922231833177](/Users/kyubyungchae/Downloads/image-20210922231833177.png)



트리1과 트리2는 노드와 엣지를 가지고 있는 트리다. 

1번부터 9번 노드를 가지고 있다. 둘 다 1번 노드가 루트이고 깊이는 4이다. 

2번 노드를 보면 두 트리의 차이점을 알 수 있다. 트리1의 2번 노드는 자식 노드가 3개이지만 트리2는 2번 노드의 자식 노드는 2개이다. 

트리2처럼 모든 노드가 최대 두 개의 자식을 갖는 트리를 **이진 트리**라고 한다.

이진 트리는 깊이 h에 최대 $2^{h-1}$만큼 노드를 가질 수 있다.



다음과 같은 이진 트리를 python으로 생성해보자.



![image-20210922231818572](/Users/kyubyungchae/Downloads/image-20210922231818572.png)



`Node` 는 자기 자신의 값인 `item` 과  자식의 위치인 `left` 와 `right` 를 가진다.

```python
class Node:
    def __init__(self, item):
        self.item = item
        self.left = None
        self.right = None
```



각 노드를 생성한다.

```python
n1 = Node(10)
n2 = Node(20)
n3 = Node(30)
n4 = Node(40)
n5 = Node(50)
n6 = Node(60)
n7 = Node(70)
n8 = Node(80)
```





각 노드를 엮어주기 위하여 `BinaryTree` 를 만든다.

```python
class BinaryTree():
    def __init__(self): #트리 생성
        self.root = None
```



순서에 맞게 노드를 엮는다.

```python
tree = BinaryTree()
tree.root = n1
n1.left = n2
n1.right = n3
n2.left = n4
n2.right = n5
n3.left = n6
n3.right = n7
n4.left = n8
```









### 트리 순회란

트리 순회란 트리의 노드를 지정한 순서대로 방문하는 것을 말한다.

이진 트리를 순회하는 방법은 대표적으로 3개이다. 여기에 1가지를 더해 총 4가지를 python으로 구현해보자.

- 전위 순회 (Preorder)
- 후위 순회 (Postorder)
- 중위 순회 (Inorder)
- 레벨 순회 (Levelorder)



트리를 읽을 때에 대전제는 왼쪽부터 오른쪽으로 읽는다는 것이다. 오른쪽에서부터 왼쪽으로는 읽지 않는다.

"방문"은 자신의 번호를 출력하는 `print()` 함수로 표현한다. 

이 출력 함수의 위치에 따라서 트리를 순회하는 방법을 구분하는 것이다.



![image-20210922231123982](/Users/kyubyungchae/Downloads/image-20210922231123982.png)





### 전위 순회 (Inorder)

자기 자신을 먼저 출력하고 자식 노드를 호출한다. 순서는 다음과 같다.



1. 자기 자신 출력
2. 왼쪽 서브트리 호출
3. 오른쪽 서브트리 호출



그러다면 출력은 어떤 순서로 될까? 

우선 맨 위 루트부터 방문하므로 자기 자신인 10을 출력한다. 그 후에 왼쪽에 있는 트리를 호출한다.

왼쪽에 있는 20번 노드를 방문했으므로 20을 출력한다. 그 후에 왼쪽에 있는 트리를 호출한다.

왼쪽에 있는 40번 노드를 방문했으므로 40을 출력한다. 그 후에 왼쪽에 있는 트리를 호출한다.

왼쪽에 있는 80번 노드를 방분했으므로 80을 출력한다. 80번 노드는 자식 노드가 없으므로 더 호출하지 않는다.

40번 노드로 올라간다. 40번 노드의 오른쪽 노드가 없으므로 더 호출하지 않는다.

20번 노드로 올라간다. 20번 노드의 오른쪽 노드인 50번 노드를 방문한다. 50번 노드 출력. 50번 노드가 자식 노드가 없으므로 호출 끝.

20번 노드로 올라간다. 호출할 자식 노드 없으므로 10번 노드로 올라간다.

오른쪽의 30번 노드를 방문한다. 30을 출력한다. 왼쪽 노드인 60번 노드 방문. 60을 호출. 자식 노드 없으므로 호출 끝.

30번 노드로 올라간다. 오른쪽 노드인 70번 노드를 방문한다. 70을 출력한다. 자식 노드 없으므로 호출 끝.



정답 : 10 20 40 80 50 30 60 70



```python
    # 전위 순회
    def preorder(self, n):
        if n != None:
            print(n.item, '', end='') # 노드 방문
            if n.left:
                self.preorder(n.left) # 왼쪽 서브트리 순회
            if n.right:
                self.preorder(n.right) # 오른쪽 서브트리 순회
    
```





### 후위 순회

자기 자신 출력을 가장 나중에 한다.



1. 왼쪽 서브트리 호출
2. 오른쪽 서브트리 호출
3. 자기 자신 출력



```python
    # 후위 순회
    def postorder(self, n):
        if n != None:
            if n.left:
                self.postorder(n.left)
            if n.right:
                self.postorder(n.right)
            print(n.item, '', end='') # 노드 방문
    
```



호출을 먼저하고 끝이 보이면 자기 자신을 출력하므로 가장 왼쪽 노드인 80부터 출력된다.



정답 : 80 40 50 20 60 70 30 10



### 중위 순회

자기 자신 출력이 중간에 있다.



1. 왼쪽 서브트리 호출
2. 자기 자신 출력
3. 오른쪽 서브트리 호출



```python
    # 중위 순회
    def inorder(self, n):
        if n != None:
            if n.left:
                self.inorder(n.left)
            print(n.item, '', end='') # 노드 방문
            if n.right:
                self.inorder(n.right)
```



정답 : 80 40 20 50 10 60 30 70





### 레벨 순회

레벨 순회는 깊이(레벨) 단위로 출력하는 것이다. 그 순서는 깊이가 낮은 순서이다.

우리가 흔히 생각하는 10 20 30 40 50 60 70 80 순으로 출력하고 싶다면 어떻게 해야할까?

호출될 때마다 자식 노드를 따로 저장하는 것이다.

선입선출 구조인 큐로 구현한다.



```python
    # 레벨 순회
    def levelorder(self, n):
        q = []
        q.append(n)
        while q:
            t = q.pop(0)
            print(t.item, '', end='')
            if t.left != None:
                q.append(t.left)
            if t.right != None:
                q.append(t.right)
```



정답 : 10 20 30 40 50 60 70 80



### 전체 코드

```python
class Node:
    def __init__(self, item):
        self.item = item
        self.left = None
        self.right = None

class BinaryTree():
    def __init__(self): #트리 생성
        self.root = None
    
    # 트리 높이
    def height(self, root):
        if root == None:
            return 0
        return max(self.height(root.left), self.height(root.right)) + 1

    # 전위 순회
    def preorder(self, n):
        if n != None:
            print(n.item, '', end='') # 노드 방문
            if n.left:
                self.preorder(n.left) # 왼쪽 서브트리 순회
            if n.right:
                self.preorder(n.right) # 오른쪽 서브트리 순회
    
    # 후위 순회
    def postorder(self, n):
        if n != None:
            if n.left:
                self.postorder(n.left)
            if n.right:
                self.postorder(n.right)
            print(n.item, '', end='') # 노드 방문
    
    # 중위 순회
    def inorder(self, n):
        if n != None:
            if n.left:
                self.inorder(n.left)
            print(n.item, '', end='') # 노드 방문
            if n.right:
                self.inorder(n.right)

    # 레벨 순회
    def levelorder(self, n):
        q = []
        q.append(n)
        while q:
            t = q.pop(0)
            print(t.item, '', end='')
            if t.left != None:
                q.append(t.left)
            if t.right != None:
                q.append(t.right)


                

tree = BinaryTree()
n1 = Node(10)
n2 = Node(20)
n3 = Node(30)
n4 = Node(40)
n5 = Node(50)
n6 = Node(60)
n7 = Node(70)
n8 = Node(80)

tree.root = n1
n1.left = n2
n1.right = n3
n2.left = n4
n2.right = n5
n3.left = n6
n3.right = n7
n4.left = n8

print('트리 높이 : ', tree.height(tree.root))

# 전위 순회: 10 20 40 80 50 30 60 70
print('전위 순회: ', end='')
tree.preorder(tree.root)
print()

# 후위 순회: 80 40 50 20 60 70 30 10 
print('후위 순회 : ', end='')
tree.postorder(tree.root)
print()

# 중위 순회: 80 40 20 50 10 60 30 70
print('중위 순회 : ', end='')
tree.inorder(tree.root)
print()

# 레벨 순회: 10 20 30 40 50 60 70 80
print('레벨 순회 : ', end='')
tree.levelorder(tree.root)
print()

```



이제 이진 트리라는 자료구조를 구현하는 방법을 알았다.

다음에는 이 구조를 활용하여 결정 트리(Decision Tree)를 만들어 보자.



