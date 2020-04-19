# Scrapper _ Flask

## 4.1 
```
from flask import Flask

app = Flask("SuperScrapper_YJ")

@app.route("/")
def home():
  return "hello to mi casa!"

@app.route("/contact")
def contact():
  return "contact me!"

app.run(host="0.0.0.0")
```
@ 는 데코레이터. 자기 아래에 있는 함수만 본다
"/"는 홈페이지를 실행시킬 때 나오는 것이라고 생각하면 된다..
google.com이나 google.com/ 이나 똑같다

그리고 host = "0.0.0.0"을 통해 repl.it이 아~ 홈페이지를 전체로(?) 만들거구나 해서 홈페이지를 만듬.

## 4.2 
* main.py
  ```
  from flask import Flask, render_template

    app = Flask("SuperScrapper_YJ")

    @app.route("/")
    def home():
    return render_template("potato.html")

    app.run(host="0.0.0.0")
    ```

* potato.html (template 폴더 안에 있다)
  ```
  <!DOCTYPE html>
    <html>
    <head>
        <title Job Search></title>
    </head>
    <body>
        <h1>Job Search</h1>
        <form>
        <input placeholder="Search for a job?" required />
        <buttons>Search</buttons>
        </form>
    </body>
    </html>
    ```

## 4.3 

* potato.html
  ```
  <!DOCTYPE html>
  <html>
    <head>
      <title Job Search></title>
    </head>
    <body>
      <h1>Job Search</h1>
      <form action="/report" method="get">
        <input placeholder="Search for a job?" required name="word"/>
        <buttons>Search</buttons>
      </form>
    </body>
  </html>
  ```

* report.html
  ```
  <!DOCTYPE html>
  <html>
    <head>
      <title Job Search></title>
    </head>
    <body>
      <h1>Search Results</h1>
      <h3>You are looking for {{searchingBy}}  </h3>
    </body>
  </html>
  ```

* main.py
  ```
  from flask import Flask, render_template, request

  app = Flask("SuperScrapper_YJ")

  @app.route("/")
  def home():
    return render_template("potato.html")

  @app.route("/report")
  def report():
    word = request.args.get('word')
    return render_template("report.html",searchingBy=word)

  app.run(host="0.0.0.0")
  ```

렌더링(rendering)이라고 한다. flask가 단어를 받아와서 html에 주는 일련의 과정.

## 4.4 
# 4.4 -1 오류 잡고 redirect
* main.py
  ```
  from flask import Flask, render_template, request, redirect

  app = Flask("SuperScrapper_YJ")

  @app.route("/")
  def home():
    return render_template("potato.html")

  @app.route("/report")
  def report():
    word = request.args.get('word')
    if word:
      word = word.lower()
    else:
      return redirect("/")
    return render_template("report.html",searchingBy=word)

  app.run(host="0.0.0.0")
  ```
# 4.4-2 
전에 만든 scrapper + flask로 만든 페이지
BeautifulSoup4, request 패키지 확장.

* scrapper.py
  ```
  # step1. get the page 
  # step2. make the requests
  # step3. extract the jobs
  import requests
  from bs4 import BeautifulSoup


  def get_last_page(url):
    result = requests.get(url)
    soup = BeautifulSoup(result.text, "html.parser")
    pages = soup.find("div",{"class":"s-pagination"}).find_all("a")
    last_page = pages[-2].get_text(strip=True)
    return int(last_page)


  def extract_job(html):
    title = html.find("h2").find("a")["title"]
    company, location = html.find("h3").find_all("span",recursive=False)
    company = company.get_text(strip=True)
    location = location.get_text(strip=True).strip("-").strip(" \r").strip("\n")
    job_id = html['data-jobid']
    #print(company.get_text(strip=True), location.get_text(strip=True))
    return {'title':title,'company':company,'location':location,"apply_link":f"https://stackoverflow.com/jobs/{job_id}"}


  def extract_jobs(last_page,url):
    jobs = []
    for page in range(last_page):
      print(f"Scrapping SO: Page : {page}")
      result = requests.get(f"{url}&pg={page+1}")
      soup = BeautifulSoup(result.text,"html.parser")
      results = soup.find_all("div",{"class":"-job"})
      for result in results:
        job = extract_job(result)
        jobs.append(job)
    return jobs


  def get_jobs(word):
    url = f"https://www.stackoverflow.com/jobs?q={word}&sort=i"
    last_page = get_last_page(url)
    jobs = extract_jobs(last_page, url)
    return jobs
    ```

    URL로 선언되어 있어 바꿀 수 없던 것은 main의 word로 받아와 사용할 수 있도록 조작함.
  
  * main.py
    ```
    from flask import Flask, render_template, request, redirect
    from scarpper import get_jobs

    app = Flask("SuperScrapper_YJ")

    @app.route("/")
    def home():
      return render_template("potato.html")

    @app.route("/report")
    def report():
      word = request.args.get('word')
      if word:
        word = word.lower()
        jobs = get_jobs(word)
        print(jobs)
      else:
        return redirect("/")
      return render_template("report.html",searchingBy=word)

    app.run(host="0.0.0.0")
    ```

## 4.5 
* main.py
  ```
  from flask import Flask, render_template, request, redirect
  from scrapper import get_jobs

  app = Flask("SuperScrapper_YJ")

  db = {}

  @app.route("/")
  def home():
    return render_template("potato.html")

  @app.route("/report")
  def report():
    word = request.args.get('word')
    if word:
      word = word.lower()
      fromDb = db.get(word)
      if fromDb:
        jobs= fromDb    
      else:
        jobs = get_jobs(word)
        db[word] = jobs
    else:
      return redirect("/")
    return render_template("report.html",searchingBy=word, resultsNumber=len(jobs))

  app.run(host="0.0.0.0")
  ```
main에서 fake DB를 만들었다. fake db를 함수 밖에 만들어서 페이지가 새로고침되어도 다시 작업하지 않도록한다(다시 db에 정보를 collect하는 작업을 반복하지 않도록). 

* report.html
  ```
    <!DOCTYPE html>
  <html>
    <head>
      <title Job Search></title>
    </head>
    <body>
      <h1>Search Results</h1>
      <h3>Found{{resultsNumber}} results for: {{searchingBy}}  </h3>
    </body>
  </html>
  ```
  report.html도 원하는 정보(예 python)가 잘 나왔는지 확인할 수 있도록 조금 수정한다.

## 4.6
html과 css를 이용해 페이지에 정보가 예쁘게 나올 수 있도록 조정했다.

* report.html
  ```
  <!DOCTYPE html>
  <html>
    <head>
      <title Job Search></title>
      <style>
        section{
          display:grid;
          gap:20px;
          grid-template-columns: repeat(4,1fr);
        }
        </style>

    </head>
    <body>
      <h1>Search Results</h1>
      <h3>Found{{resultsNumber}} results for: {{searchingBy}}  </h3>
      <section>
      <h4>Title</h4>
      <h4>Company</h4>
      <h4>Locaion</h4>
      <h4>Link</h4>
      {% for job in jobs %}
        <span>{{job.title}}</span>
        <span>{{job.company}}</span>
        <span>{{job.location}}</span>
        <a href="{{job.link}}" target="_blank">Apply</a>
      {% endfor %}
      <section>
    </body>
  </html>
```
* main.py
  ```
  from flask import Flask, render_template, request, redirect
  from scrapper import get_jobs

  app = Flask("SuperScrapper_YJ")

  db = {}

  @app.route("/")
  def home():
    return render_template("potato.html")

  @app.route("/report")
  def report():
    word = request.args.get('word')
    if word:
      word = word.lower()
      fromDb = db.get(word)
      if fromDb:
        jobs= fromDb    
      else:
        jobs = get_jobs(word)
        db[word] = jobs
    else:
      return redirect("/")
    return render_template("report.html",searchingBy=word, resultsNumber=len(jobs), jobs=jobs)

  app.run(host="0.0.0.0")
  ```

main.py 아래쪽에 쓰인 jobs=jobs를 통해서 report.html에서 정보를 가져올 수 있게한다. {% 반복문 %} {{job.title}} 등으로 정보를 가져온다.

* scrapper.py
  ```
  # step1. get the page 
  # step2. make the requests
  # step3. extract the jobs
  import requests
  from bs4 import BeautifulSoup


  def get_last_page(url):
    result = requests.get(url)
    soup = BeautifulSoup(result.text, "html.parser")
    pages = soup.find("div",{"class":"s-pagination"}).find_all("a")
    last_page = pages[-2].get_text(strip=True)
    return int(last_page)


  def extract_job(html):
    title = html.find("h2").find("a")["title"]
    company, location = html.find("h3").find_all("span",recursive=False)
    company = company.get_text(strip=True)
    location = location.get_text(strip=True).strip("-").strip(" \r").strip("\n")
    job_id = html['data-jobid']
    #print(company.get_text(strip=True), location.get_text(strip=True))
    return {'title':title,'company':company,'location':location,"apply_link":f"https://stackoverflow.com/jobs/{job_id}"}


  def extract_jobs(last_page,url):
    jobs = []
    for page in range(last_page):
      print(f"Scrapping SO: Page : {page}")
      result = requests.get(f"{url}&pg={page+1}")
      soup = BeautifulSoup(result.text,"html.parser")
      results = soup.find_all("div",{"class":"-job"})
      for result in results:
        job = extract_job(result)
        jobs.append(job)
    return jobs


  def get_jobs(word):
    url = f"https://www.stackoverflow.com/jobs?q={word}&sort=i"
    last_page = get_last_page(url)
    jobs = extract_jobs(last_page, url)
    return jobs
    ```

## 4.7 csv export & save
* main.py
  ``` python
  from flask import Flask, render_template, request, redirect, send_file
  from scrapper import get_jobs
  from exporter import save_to_file

  app = Flask("SuperScrapper_YJ")

  db = {}

  @app.route("/")
  def home():
    return render_template("potato.html")

  @app.route("/report")
  def report():
    word = request.args.get('word')
    if word:
      word = word.lower()
      fromDb = db.get(word)
      if fromDb:
        jobs= fromDb    
      else:
        jobs = get_jobs(word)
        db[word] = jobs
    else:
      return redirect("/")
    return render_template("report.html",searchingBy=word, resultsNumber=len(jobs), jobs=jobs)

  @app.route("/export")
  def export():
    try:
      word = request.args.get('word')
      if not word:
        raise Exception()
      word = word.lower()
      jobs = db.get(word)
      if not jobs:
        raise Exception()
      #save_to_file(jobs)
      return f"Generate CSV for {word}"
      #return send_file("jobs.csv")
    except:
      return redirect("/")



  app.run(host="0.0.0.0")
  ```
export 함수를 만들어서 csv로 export 하기 위한 첫번째 과정을 했다.
그리고 try-Exception 구문을 사용해서 예상치못한 모든 에러 발생시에 홈("/")으로 돌아갈 수 있도록 했다.

### 4.8
* main.py
```python
from flask import Flask, render_template, request, redirect, send_file
from scrapper import get_jobs
from exporter import save_to_file

app = Flask("SuperScrapper_YJ")

db = {}

@app.route("/")
def home():
  return render_template("potato.html")

@app.route("/report")
def report():
  word = request.args.get('word')
  if word:
    word = word.lower()
    fromDb = db.get(word)
    if fromDb:
      jobs= fromDb    
    else:
      jobs = get_jobs(word)
      db[word] = jobs
  else:
    return redirect("/")
  return render_template("report.html",searchingBy=word, resultsNumber=len(jobs), jobs=jobs)

@app.route("/export")
def export():
  try:
    word = request.args.get('word')
    if not word:
      raise Exception()
    word = word.lower()
    jobs = db.get(word)
    if not jobs:
      raise Exception()
    save_to_file(jobs)
    return send_file("jobs.csv",mimetype='text/csv', attachment_filename=f"{word}.csv", as_attachment=True)
  except:
    return redirect("/")



app.run(host="0.0.0.0")
```

* exporter.py
``` python
import csv

def save_to_file(jobs):
  print("in save file", jobs)
  file = open("jobs.csv",mode="w")

  writer = csv.writer(file)
  writer.writerow(["title","company","location","link"])
  for job in jobs:
    writer.writerow(list(job.values()))
  return
  ```

* sctapper.py
``` python
# step1. get the page 
# step2. make the requests
# step3. extract the jobs
import requests
from bs4 import BeautifulSoup


def get_last_page(url):
  result = requests.get(url)
  soup = BeautifulSoup(result.text, "html.parser")
  pages = soup.find("div",{"class":"s-pagination"}).find_all("a")
  last_page = pages[-2].get_text(strip=True)
  return int(last_page)


def extract_job(html):
  title = html.find("h2").find("a")["title"]
  company, location = html.find("h3").find_all("span",recursive=False)
  company = company.get_text(strip=True)
  location = location.get_text(strip=True).strip("-").strip(" \r").strip("\n")
  job_id = html['data-jobid']
  #print(company.get_text(strip=True), location.get_text(strip=True))
  return {'title':title,'company':company,'location':location,"apply_link":f"https://stackoverflow.com/jobs/{job_id}"}


def extract_jobs(last_page,url):
  jobs = []
  for page in range(last_page):
    print(f"Scrapping SO: Page : {page}")
    result = requests.get(f"{url}&pg={page+1}")
    soup = BeautifulSoup(result.text,"html.parser")
    results = soup.find_all("div",{"class":"-job"})
    for result in results:
      job = extract_job(result)
      jobs.append(job)
  return jobs


def get_jobs(word):
  url = f"https://www.stackoverflow.com/jobs?q={word}&sort=i"
  last_page = get_last_page(url)
  jobs = extract_jobs(last_page, url)
  return jobs
```

* potato.html
``` html
<!DOCTYPE html>
<html>
  <head>
    <title Job Search></title>
  </head>
  <body>
    <h1>Job Search</h1>
    <form action="/report" method="get">
      <input placeholder="Search for a job?" required name="word"/>
      <buttons>Search</buttons>
    </form>
  </body>
</html>
```

* reporter.html
``` html
<!DOCTYPE html>
<html>
  <head>
    <title Job Search></title>
    
    <style>
      section{
        display:grid;
        gap:20px;
        grid-template-columns: repeat(4,1fr);
      }
      </style>

  </head>
  <body>
    <h1>Search Results</h1>
    <h3>Found{{resultsNumber}} results for: {{searchingBy}}  </h3>
    <a href="/export?word={{searchingBy}}">Export to CSV</a>
    <section>
    <h4>Title</h4>
    <h4>Company</h4>
    <h4>Locaion</h4>
    <h4>Link</h4>
    {% for job in jobs %}
      <span>{{job.title}}</span>
      <span>{{job.company}}</span>
      <span>{{job.location}}</span>
      <a href="{{job.link}}" target="_blank">Apply</a>
    {% endfor %}
    <section>
  </body>
</html>
```

main에서 export 함수를 통해 csv로 아웃한다. 근데 window에서는 설정을 조금 더 해줘야되서 return send_file 하고 추가적으로 더 적었다. 그리고 save_to_file은 전에 만들었던 것을 다른 클래스에서 작성 후 import했다. flask에 이미 send_file이 있어서 편하게 할 수 있었다.


