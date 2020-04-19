#python 0414

## 1.4 creationg a your first python function
``` python
def say_hello():
    print("hello")

say_hello()
```
()는 함수 버튼을 누른다는 의미로 이해하자

## 1.5 function arguments
```python
def say_hello(who):
    print("hello", who)

say_hello("nico")
```

```python
def plus(a, b):
    print(a+b)

def minus(a, b=0):
    print(a-b)

plus(1,2)
minus(2)
```
대부분의 함수는 외부에서 input을 받아서 작동한다.

## 1.6 returns
```python
def p_plus(a,b):
    print(a+b)

def plus(a,b):
    return a+b


p_result = p_plus(2,3)
r_result= p_plus(2,3)

print(p_result, r_result)
```
출력하면 None 5 가 나온다.

파이썬에에서 무언가를 return하면(함수안에서) 그 함수는 종료된다.

## 1.8 
위 함수 안에 있는 것은 positional arguments.. 위치에 따라서 정해지기 때문이다.
위치에 따라서 정해지지 않는 인자는 keyword arguments라고 한다.

```python
def p_plus(a,b):
    print(a+b)

plus(b=1,a=3)
```
plus 함수에서 사용한 것이 keyword arguments 이다.

```python
def say_hello(name,age):
    return f"hello {name} you are {age} years old"

hello = say_hello("nico","22")
print(hello)
```





