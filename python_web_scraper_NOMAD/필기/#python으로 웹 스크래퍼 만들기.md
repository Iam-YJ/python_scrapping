# python으로 웹 스크래퍼 만들기

## 2강 Building a job Scrapper

### 2-2
* 1. url을 써서 접근해야함
* 2. 접근해야할 페이지가 몇 개인지 알아야한다 
* 3. 페이지 하나씩 들어가야한다

```import requests 

indeed_result = requests.get("https://www.indeed.com/jobs?q=python&limit=50")

print(indeed_result.text)
```

import requests - pythonlib를 추가해서 더욱 강력하게 url을 가져올 수 있다

### 2-3 indeed 페이지 추출하기
beautifulsoup - 데이터를 추출할 수 있는 친구. 위의 requests와 같이 repl.it에서 사용하기 위해서는 패키지를 통해 추가해야한다.

```import requests
from bs4 import BeautifulSoup

indeed_result = requests.get("https://www.indeed.com/jobs?q=python&limit=50")

indeed_soup = BeautifulSoup(indeed_result.text, "html.parser")

pagination = indeed_soup.find("div", {"class":"pagination"})

pages = pagination.find_all('a')

spans = []

for page in pages:
  spans.append(page.find("span"))

print(spans[:-1]) #get all the sapans except for the last one
```

이를 통해 모든 span을 가져왔다. (아래 페이지 처리하는 숫자)) > page에 있는 page마다 array에 넣었다

### 2-4 
```import requests
from bs4 import BeautifulSoup

indeed_result = requests.get("https://www.indeed.com/jobs?q=python&limit=50")

indeed_soup = BeautifulSoup(indeed_result.text, "html.parser")

pagination = indeed_soup.find("div", {"class":"pagination"})

links = pagination.find_all('a')
pages = []
for link in links[:-1]:
  pages.append(int(link.string))
#get all the sapans except for the last one

max_page = pages[-1]
```
### 2-5
main.py에서 작업하던 것은 indeed.py로 빼서 함수로 따로 만들었다

* indeed.py
```import requests
from bs4 import BeautifulSoup

LIMIT = 50
URL=f"https://www.indeed.com/jobs?q=python&limit={LIMIT}"

def extract_indeed_pages():
  result = requests.get(URL)
  soup = BeautifulSoup(result.text, "html.parser")
  pagination = soup.find("div", {"class":"pagination"})

  links = pagination.find_all('a')
  pages = []
  for link in links[:-1]:
    pages.append(int(link.string))
    #get all the sapans except for the last one
  max_page = pages[-1]
  return max_page

def extract_indeed_jobs(last_page):
  jobs=[]
  for page in range(last_page):
    result = requests.get(f"{URL}&start={page*LIMIT}")
    print(result.status_code)
  return jobs
  ```
  extract_indeed_pages()는 지정한 url에서 페이지 숫자를 가져오는 것
  extract_indeed_jobs()는 각 페이지에서 몇 개의 채용 정보를 볼 수 있는지 파악한다.(ex 1page=50개, 2page = 100개..) 2-5강에서 jobs=[]는 만들기만 하고 아직 사용은 안했다. 

* main.py
```from indeed import extract_indeed_pages, extract_indeed_jobs

last_indeed_page = extract_indeed_pages()

indeed_jobs = extract_indeed_jobs(last_indeed_page)
```

### 2-6 indeed 구직의 타이틀을 가져와보자!

* indeed.py
```import requests
from bs4 import BeautifulSoup

LIMIT = 50
URL=f"https://www.indeed.com/jobs?q=python&limit={LIMIT}"

def extract_indeed_pages():
  result = requests.get(URL)
  soup = BeautifulSoup(result.text, "html.parser")
  pagination = soup.find("div", {"class":"pagination"})

  links = pagination.find_all('a')
  pages = []
  for link in links[:-1]:
    pages.append(int(link.string))
    #get all the sapans except for the last one
  max_page = pages[-1]
  return max_page

def extract_indeed_jobs(last_page):
  jobs=[]
  #for page in range(last_page):
  result = requests.get(f"{URL}&start={0*LIMIT}")
  soup = BeautifulSoup(result.text, "html.parser")
  results = soup.find_all("div",{"class":"jobsearch-SerpJobCard"})
  for result in results:
    title=  result.find("div", {"class":"title"}).find("a")["title"]

  return jobs
  ```
  title에 저장한 정보를 설명하자면.. result는 일자리목록을 나타낸다. 여기서 div를 찾아 class가 title인데 anchor를 찾는다.. 그래서 최종족으로 title을 찾았다.
    print(title)

* main.py
```from indeed import extract_indeed_pages, extract_indeed_jobs

last_indeed_page = extract_indeed_pages()

indeed_jobs = extract_indeed_jobs(last_indeed_page)
```

### 2-7 indeed내 회사 이름을 가져와보자

* indeed.py
```import requests
from bs4 import BeautifulSoup

LIMIT = 50
URL=f"https://www.indeed.com/jobs?q=python&limit={LIMIT}"

def extract_indeed_pages():
  result = requests.get(URL)
  soup = BeautifulSoup(result.text, "html.parser")
  pagination = soup.find("div", {"class":"pagination"})

  links = pagination.find_all('a')
  pages = []
  for link in links[:-1]:
    pages.append(int(link.string))
    #get all the sapans except for the last one
  max_page = pages[-1]
  return max_page

def extract_indeed_jobs(last_page):
  jobs=[]
  #for page in range(last_page):
  result = requests.get(f"{URL}&start={0*LIMIT}")
  soup = BeautifulSoup(result.text, "html.parser")
  results = soup.find_all("div",{"class":"jobsearch-SerpJobCard"})
  for result in results:
    title=  result.find("div", {"class":"title"}).find("a")["title"]
    company = result.find("span",{"class":"company"})
    company_anchor = company.find("a")

    if company_anchor is not None:
      company = str(company_anchor.string)
      #company가 앵커가(회사링크) 있으면! ~ 앵커안으로 가서 string 출력
    else:
      company = str(company.string)
      # 앵커 없으면~ 그냥 span의 string 출력
    company = company.strip()
    print(title, company)
  return jobs
  ```
  순서대로 해석해보면..company는 soup에서 companay_anchor에다가 soup로 찾은 결과를 넣음. 그리고 기존 company에 있는 soup를 삭제하고 string을 넣었다

* main.py
변동사항 없음

### 2-8 location, link 가져오기
조건문 사용하지 않고 html 이용해서 정보를 가져온다

* indeed.py
```
import requests
from bs4 import BeautifulSoup

LIMIT = 50
URL = f"https://www.indeed.com/jobs?q=python&limit={LIMIT}"


def extract_indeed_pages():
    result = requests.get(URL)
    soup = BeautifulSoup(result.text, "html.parser")
    pagination = soup.find("div", {"class": "pagination"})

    links = pagination.find_all('a')
    pages = []
    for link in links[:-1]:
        pages.append(int(link.string))
        #get all the sapans except for the last one
    max_page = pages[-1]
    return max_page


def extract_job(html):
    title = html.find("div", {"class": "title"}).find("a")["title"]
    company = html.find("span", {"class": "company"})
    company_anchor = company.find("a")

    if company_anchor is not None:
        company = str(company_anchor.string)
        #company가 앵커가(회사링크) 있으면! ~ 앵커안으로 가서 string 출력
    else:
        company = str(company.string)
        # 앵커 없으면~ 그냥 span의 string 출력
    company = company.strip()
    location = html.find("div", {"class": "recJobLoc"})["data-rc-loc"]
    job_id = html["data-jk"]
    return {
        'title': title,
        'company': company,
        'location': location,
        "link": f"https://www.indeed.com/viewjob?jk={job_id}"
    }


def extract_indeed_jobs(last_page):
    jobs = []
    for page in range(last_page):
      print(f"Scrapping page {page}")
      result = requests.get(f"{URL}&start={page*LIMIT}")
      soup = BeautifulSoup(result.text, "html.parser")
      results = soup.find_all("div", {"class": "jobsearch-SerpJobCard"})
      for result in results:
          job = extract_job(result)
          jobs.append(job)
    return jobs
    ```
    
* main.py
  ```from indeed import extract_indeed_pages, extract_indeed_jobs

last_indeed_page = extract_indeed_pages()

indeed_jobs = extract_indeed_jobs(last_indeed_page)

print(indeed_jobs)
```

### 2-9
main에서 indeed를 위해 사용하는 것을 indeed.py 안에 함수로 넣어주고
stackoverflow에서 추출하기 위한 것을 만들기 시작했다.
* indeed.py
```import requests
from bs4 import BeautifulSoup

LIMIT = 50
URL = f"https://www.indeed.com/jobs?q=python&limit={LIMIT}"


def get_last_page():
    result = requests.get(URL)
    soup = BeautifulSoup(result.text, "html.parser")
    pagination = soup.find("div", {"class": "pagination"})

    links = pagination.find_all('a')
    pages = []
    for link in links[:-1]:
        pages.append(int(link.string))
        #get all the sapans except for the last one
    max_page = pages[-1]
    return max_page


def extract_job(html):
    title = html.find("div", {"class": "title"}).find("a")["title"]
    company = html.find("span", {"class": "company"})
    if company:
      company_anchor = company.find("a")

      if company_anchor is not None:
          company = str(company_anchor.string)
          #company가 앵커가(회사링크) 있으면! ~ 앵커안으로 가서 string 출력
      else:
          company = str(company.string)
          # 앵커 없으면~ 그냥 span의 string 출력
          company = company.strip()
    else:
      company = None

   
    location = html.find("div", {"class": "recJobLoc"})["data-rc-loc"]
    job_id = html["data-jk"]
    return {
        'title': title,
        'company': company,
        'location': location,
        "link": f"https://www.indeed.com/viewjob?jk={job_id}"
    }


def extract_jobs(last_page):
    jobs = []
    for page in range(last_page):
      print(f"Scrapping page {page}")
      result = requests.get(f"{URL}&start={page*LIMIT}")
      soup = BeautifulSoup(result.text, "html.parser")
      results = soup.find_all("div", {"class": "jobsearch-SerpJobCard"})
      for result in results:
          job = extract_job(result)
          jobs.append(job)
    return jobs

def get_jobs():
  last_page = get_last_page()
  jobs = extract_jobs(last_page)
  return jobs
  ```

* so.py
  ```
  # step1. get the page 
  # step2. make the requests
  # step3. extract the jobs
import requests
from bs4 import BeautifulSoup

URL = f"https://www.stackoverflow.com/jobs?q=python&sort=i"


def get_last_page():
  result = requests.get(URL)
  soup = BeautifulSoup(result.text, "html.parser")
  pages = soup.find("div",{"class":"s-pagination"}).find_all("a")
 


def get_jobs():
  last_page = get_last_page()
  return [] ```

* main.py
```from indeed import get_jobs as get_indeed_jobs
from so import get_jobs as get_so_jobs

#indeed_jobs = get_indeed_jobs()
so_jobs = get_so_jobs()

#print(indeed_jobs)
```

### 2-10 stackoverflow를 위한 작업중이다. indeed와 비슷하다. 아니 거의 똑같다
* so.py
  ```
import requests
from bs4 import BeautifulSoup

URL = f"https://www.stackoverflow.com/jobs?q=python&sort=i"


def get_last_page():
  result = requests.get(URL)
  soup = BeautifulSoup(result.text, "html.parser")
  pages = soup.find("div",{"class":"s-pagination"}).find_all("a")
  last_page = pages[-2].get_text(strip=True)
  return int(last_page)


def extract_jobs(last_page):
  jobs = []
  for page in range(last_page):
    result = requests.get(f"{URL}&pg={page+1}")
    soup = BeautifulSoup(result.text,"html.parser")
    results = soup.find_all("div",{"class":"-job"})
    for result in results:
      print(result["data-jobid"])


def get_jobs():
  last_page = get_last_page()
  jobs = extract_jobs(last_page)
  return jobs ```
   
* main. py
  ```
  from indeed import get_jobs as get_indeed_jobs
from so import get_jobs as get_so_jobs

#indeed_jobs = get_indeed_jobs()
so_jobs = get_so_jobs()

#print(indeed_jobs) ```

### 2-11 일자리를 가져오는 함수 extrack_job
* so.py (작동 됨)
``` # step1. get the page 
# step2. make the requests
# step3. extract the jobs
import requests
from bs4 import BeautifulSoup

URL = f"https://www.stackoverflow.com/jobs?q=python&sort=i"


def get_last_page():
  result = requests.get(URL)
  soup = BeautifulSoup(result.text, "html.parser")
  pages = soup.find("div",{"class":"s-pagination"}).find_all("a")
  last_page = pages[-2].get_text(strip=True)
  return int(last_page)


def extract_job(html):
  title = html.find("div",{"class":"grid--cell fl1"}).find("h2").find("a")
  company, location = html.find("h3",{"class":"fs-body1"}).find_all("span",recursive=False)
  print(company.get_text(strip=True), location.get_text(strip=True))
  return {'title':title}


def extract_jobs(last_page):
  jobs = []
  for page in range(last_page):
    result = requests.get(f"{URL}&pg={page+1}")
    soup = BeautifulSoup(result.text,"html.parser")
    results = soup.find_all("div",{"class":"-job"})
    for result in results:
      job = extract_job(result)
      jobs.append(job)
  return jobs


def get_jobs():
  last_page = get_last_page()
  jobs = extract_jobs(last_page)
  return jobs
  ```

  오류는 AttributeError: 'NoneType' object has no attribute 'find' 였고, 검색되어지는 것이 없을 때 나타나는 오류라고 했다.
  왜그러지.. 생각해보다가 아래 댓글들로 추론한 이유는 stackoverflow의 html 코드가 바뀌어서(아마도 이름이?) 검색이 안되는 것 같았다.

  def extract_job()의 코드를 바꾸고 나니 정상적으로 작동했다.

* so.py(바꾸기 전)
  ```def extract_job(html):
  title = html.find("div",{"class":"-title"}).find("h2").find("a")["title"]
  company, location = html.find("div",{"class":"-company"}).find_all("span",recursive=False)
  print(company.get_text(strip=True), location.get_text(strip=True))
  return {'title':title}
  ```

### 2-13 so.py 마무리
* so.py
```# step1. get the page 
# step2. make the requests
# step3. extract the jobs
import requests
from bs4 import BeautifulSoup

URL = f"https://www.stackoverflow.com/jobs?q=python&sort=i"


def get_last_page():
  result = requests.get(URL)
  soup = BeautifulSoup(result.text, "html.parser")
  pages = soup.find("div",{"class":"s-pagination"}).find_all("a")
  last_page = pages[-2].get_text(strip=True)
  return int(last_page)


def extract_job(html):
  title = html.find("div",{"class":"grid--cell fl1"}).find("h2").find("a")
  company, location = html.find("h3",{"class":"fs-body1"}).find_all("span",recursive=False)
  company = company.get_text(strip=True)
  location = location.get_text(strip=True)
  job_id = html['data-jobid']
  #print(company.get_text(strip=True), location.get_text(strip=True))
  return {'title':title,'company':company,'location':location,"apply_link":f"https://stackoverflow.com/jobs/{job_id}"}


def extract_jobs(last_page):
  jobs = []
  for page in range(last_page):
    print(f"Scrapping SO: Page : {page}")
    result = requests.get(f"{URL}&pg={page+1}")
    soup = BeautifulSoup(result.text,"html.parser")
    results = soup.find_all("div",{"class":"-job"})
    for result in results:
      job = extract_job(result)
      jobs.append(job)
  return jobs


def get_jobs():
  last_page = get_last_page()
  jobs = extract_jobs(last_page)
  return jobs
  ```

  
  
* main.py
```
from indeed import get_jobs as get_indeed_jobs
from so import get_jobs as get_so_jobs

indeed_jobs = get_indeed_jobs()
so_jobs = get_so_jobs()
jobs = so_jobs + indeed_jobs
print(jobs)
```

### 2-14, 15, 16 csv 만들기
csv는 어디서든 쓸 수 있다. vscode로 만들어서 구글에서도 볼 수 있는데, 파이썬에는 내장함수가 있어서 더 편리하게 쓸 수 있다.




  


