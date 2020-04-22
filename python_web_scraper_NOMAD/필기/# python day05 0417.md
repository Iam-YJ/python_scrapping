# python 0417 

## 2.3 extraction indeed page
```python
import requests

indeed_result = requests.get("https://www.indeed.com/jobs?q=python&limit=50")

print(indeed_result)
```
지난시간 request를 이용해서 indeed 페이지의 html을 모두 가져왔다

soup는 데이터를 추출하는 애다.

```python
import requests
from bs4 import BeautifulSoup

indeed_result = requests.get("https://www.indeed.com/jobs?q=python&limit=50")

indeed_soup = BeautifulSoup(indeed_result.text, "html.parser")

print(indeed_soup)
```
soup = BeautifulSoup(html_doc, 'html.parser')
의 형식으로 입력해야 beautifulsoup에게 html.parser를 사용한다고 알려주는 것이다.

```html
<div class = "pagination">
    <a href = "...">
      <span class="...">
```

``` python
import requests
from bs4 import BeautifulSoup

indeed_result = requests.get("https://www.indeed.com/jobs?q=python&limit=50")

indeed_soup = BeautifulSoup(indeed_result.text, "html.parser")

pagination = indeed_soup.find("div", {"class":"pagination"})
#pagination = indeed_soup.find(name, {attribute})

print(pagination)
```
후에 콘솔창에서 pagination을 찾는다.

``` python
import requests
from bs4 import BeautifulSoup

indeed_result = requests.get("https://www.indeed.com/jobs?q=python&limit=50")

indeed_soup = BeautifulSoup(indeed_result.text, "html.parser")

pagination = indeed_soup.find("div", {"class":"pagination"})
#pagination = indeed_soup.find(name, {attribute})

pages = pagination.find_all('a')
# pages는 list 이다

spans=[]
for page in pages:
    spans.append(page.find("span"))
spans = spans[-1]
```
하면 콘솔에 페이지 번호를 나타내는 것이 리스트 형식으로 출력된다.
<span class="pn>11</span>...
이 출력됨

## 2.4 extractiong indeed pages part two 
``` python
import requests
from bs4 import BeautifulSoup

indeed_result = requests.get("https://www.indeed.com/jobs?q=python&limit=50")

indeed_soup = BeautifulSoup(indeed_result.text, "html.parser")

pagination = indeed_soup.find("div", {"class":"pagination"})
#pagination = indeed_soup.find(name, {attribute})

links = pagination.find_all('a')
# pages는 list 이다

pages=[]
for link in links[:-1]:
    pages.append(int(page.find("span").string))
    # span을 찾은 후 안에 있는 text만 가져온다
    #pages.append(link,string) 도 같은 결과
max_page = print(pages[-1])
#가장 큰 수 찾는다
```

## 2.5 requesting each page
``` python #main.py
from indeed import extract_indeed_pages, extract_indeed_jobs

last_indeed_page = extract_indeed_pages()

indeed_jobs = extract_indeed_jobs(last_indeed_page)

```
```python #indeed.py
import requests
from bs4 import BeautifulSoup

LIMIT = 50
URL = f"https://www.indeed.com/jobs?q=python&limit={LIMIT}"

def extract_indeed_pages(): #주어진 url을 통해 페이지 (1,2..) 정보를 가져옴 
    result = requests.get(URL)
    soup = BeautifulSoup(result.text, "html.parser")
    pagination = soup.find("div", {"class":"pagination"})
    #pagination = indeed_soup.find(name, {attribute})

    links = pagination.find_all('a')
    # pages는 list 이다
    pages=[]
    for link in links[:-1]:
        pages.append(int(page.find("span").string))
        # span을 찾은 후 안에 있는 text만 가져온다
        #pages.append(link,string) 도 같은 결과
    max_page = print(pages[-1])
    #가장 큰 수 찾는다
    return max_page

def extract_indeed_jobs(last_page):
    jobs= []
    for page in range(last_page):
        result = requests.get(f"{URL}&start={page*LIMIT}")
        print(result.status_code) #url이 작동하는지 확인하기위함
```



