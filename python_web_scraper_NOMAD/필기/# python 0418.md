# python 0418

## 2.6 extracting titles & 2.7 extracting company
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
    #for page in range(last_page):
    result = requests.get(f"{URL}&start={0*LIMIT}")
    soup = BeautifulSoup(result.text, "html.parser")
    results = soup.find_all("div",{"class":"jobsearch-SerpJobCard"})
    for result in results: #results는 soup를 사용한 html 리스트이다.
        title = result.find("div",{"class":"title"}).find("a")["title"] #이거도 위에 result를 궁극적으로 이용하기 때문에 리스트이다.
        # result는 각 일자리다. result의 soup를 사용해서 title을 가져옴.
        company = result.find("span",{"class":"company"})
        company_anchor = company.find("a")
        if company_anchor is not None:
            company = (str(company_anchor.string))
        else:
            company = (str(company.string))
        company = company.strip()
        

    return jobs
```

## 2.8 extracting locations and finishing up
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

def extract_job(html):
    title = result.find("div",{"class":"title"}).find("a")["title"] #이거도 위에 result를 궁극적으로 이용하기 때문에 리스트이다.
    # result는 각 일자리다. result의 soup를 사용해서 title을 가져옴.
    company = result.find("span",{"class":"company"})
    if company:
        company_anchor = company.find("a")
            if company_anchor is not None:
                company = (str(company_anchor.string))
            else:
                company = (str(company.string))
            company = company.strip()
    else:
        company = None
    location = html.find("div",{"class":"recJobLoc"}).["data-rc-loc"]
    job_id = html["data-jk"]
    return {'title':title, 'company':company, 'location':location, "link":f"https://www.indeed.com/viewjob?jk={job_id}"}


def extract_indeed_jobs(last_page):
    print(f"scrapping pages {page}")
    jobs= []
    for page in range(last_page):
        result = requests.get(f"{URL}&start={page*LIMIT}")
        soup = BeautifulSoup(result.text, "html.parser")
        results = soup.find_all("div",{"class":"jobsearch-SerpJobCard"})
        for result in results: #results는 soup를 사용한 html 리스트이다.
        job =  extract_jobs(result)
        jobs.append(job)
        return jobs

def get_jobs():
    last_indeed_page = extract_indeed_pages()
    jobs = extract_indeed_jobs(last_indeed_page)
    return jobs
```
```python main.py
from indeed import extract_indeed_pages, extract_indeed_jobs, get_jobs

indeed_jobs = get_indeed_jobs()

print(indeed_jobs)
```

## 2.9 stackoverflow pages
``` python so.py

import requests
from bs4 import BeautifulSoup

LIMIT = 50
URL = f"https://stackoverflow.com/jobs?=q=python&sort=1"

def get_last_page():
    result = requests.get(URL)
    soup = BeautifulSoup(result.text,"html.parser")
    pagination = soup.find("div",{"class":"pagination"}).find_all("a")

def get_jobs():
    last_page = get_last_page()
    return []


```

```python main.py
from indeed import extract_indeed_pages, extract_indeed_jobs, get_jobs
from so import get_jobs as get_so_jobs

#indeed_jobs = get_indeed_jobs()
so_jobs = get_so_jobs()

print(indeed_jobs)
```