# python 0416

## 1.12 for in
```python
days = {"mon","tue","wed","thu","fri"}

for x in days:
    print(x)
```
x 는 프로그램이 동작하면서 선언된다.

```python
days = {"mon","tue","wed","thu","fri"}

for day in days:
    if day is "wed":
        break
    else:
        print(day)
```
for은 list, str 등에서 쓰일 수 있다.

## 1.13 modules
```python
import math

print(math.ceil(1.2))

```
import로 하면 사용하지 않는 많은 기능들도 같이 오기 때문에 비효율적이다.

```python
from math import ceil, fsum

print(math.ceil(1.2))
print(fsum([1,2,3]))
```
from math import ceil as cc
임포트 할 때 이름도 바꿀 수 있다.

from 폴더명 import 함수명
으로 폴더 안에 있는 함수도 가져올 수 있다.

## 2.0 what is web scrapping

## 2.1 what are we building

## 2.2 navigation with python
step 1. 목표하는 url로 접근해야한다
step 2. 해당 url에 페이지가 보여주는게 몇 개인지 판단한다
step 3. 페이지 하나씩 들어간다.

<시작>
request 는  파이썬에서 요청을 만드는 기능을 모아둔 것이다.

```python
import requests
r = requests.get(가져오려는 url, 가져올 정보.. 등)
```
이런 식으로 사용한다.

1. repl.it package에 들어가 requests(python http for humans) 다운한다

2. request를 사용해 url을 가져온다
```python
import requests

indeed_result = requests.get("https://www.indeed.com/jobs?q=python&limit=50")

print(indeed_result)
```
출력은 <Response[200]> 이 나온다.
200 은 okay 라는 뜻이다.

3. html 확인
   print(indeed_result.text) 로 변경하여 페이지 내의 모든 html을 가져온다.

4. html에서 필요한 정보만 추출해야 한다
   page 정보를 가져와야하는데, 
   이 때 beautifulsoup4(screen-scrapping library) 를 package에서 추가하여 사용한다. 





