## python 0420

## 2.14 what is CSV
CSV = Comma Separated Values

``` python save.py
import csv

def save_to_file(jobs):
    return  
```

``` python main.py
from indeed import get_jobs as get_indeed_jobs
from save import save_to_file

indeed_jobs = get_indeed_jobs()
jobs = indeed_jobs

save_to_file(jobs)
```

## 2.15 saving to CSV

``` python save.py
import csv

def save_to_file(jobs):
    file = open("jobs.csv", mode="w") #write 형식
    writer = csv.writer(file)
    writer.writerow(["title","company","location","link"])
    for job in jobs:
        writer.writertow(list(job.values()))
    return  
```

``` python main.py
from indeed import get_jobs as get_indeed_jobs
from save import save_to_file

indeed_jobs = get_indeed_jobs()
jobs = indeed_jobs

save_to_file(jobs)
```

## 2.16 omg this is awesome

repl.it에서 만들었다면, spread sheet로 만들기 위해 모든 파일을 zip 형태로 다운받는다 -> googlesheet -> import jobs.csv -> comma, replace import 선택
