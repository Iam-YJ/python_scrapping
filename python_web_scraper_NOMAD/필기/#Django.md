# Django

### 3.1
positional arguments, *args -> () tuple로 묶임

keywords arguments, **kwargs -> {} dictionary로 묶임

* main.py
  ```def plus(*args):
  result = 0
  for number in args:
    result += number
  print(result)
  plus(1,2,3,4,5,5,6)
  ```

### 3.2 Class와 instance
* main.py
  ```
  class Car():
  wheels = 4
  doors = 4
  windows = 4
  seats = 4

    porche = Car()
    porche.color = "red"

    ferrari = Car()
    ferrari.color="yellow"
    print(ferrari.color)
    ```
Car class로 instance인 porche와 ferrari를 만들었다.
class는 설계도, instance는 완성품(?) 이라고 생각하면 된다.

### 3.3 
파이썬은 method를  호출할 때 그 method의 instance를 첫번째 argument로 사용한다. method는 그냥 클래스 안에 있는 함수다
너가 만든 모든 method의 첫번째 argument는 그 method를 호출한 instance다. 
예시 porche.start(porche)

### 3.4 객체지향 프로그래밍 예시
```
class Car():

  def __init__(self, **kwargs):
    self.wheels = 4
    self.doors = 4
    self.windows = 4
    self.seats = 4
    self.color = kwargs.get("color","black")
    self.price = kwargs.get("price","$20")

  def __str__(self):
    return f"Car with {self.wheels} wheels"


  porche = Car(color="green", price="$40")
  print(porche.color, porche.price)

  mini = Car()
  print(mini.color, mini.price)
  ```

  ### 3.5 상속과 확장
  ```
  class Car():

  def __init__(self, **kwargs):
    self.wheels = 4
    self.doors = 4
    self.windows = 4
    self.seats = 4
    self.color = kwargs.get("color","black")
    self.price = kwargs.get("price","$20")

  def __str__(self):
    return f"Car with {self.wheels} wheels"


  class Convertible(Car):
    
    def __init__(self,**kwargs):
      super().__init__(**kwargs)
      self.time = kwargs.get("time",10)

    def take_off(self):
      reutnr "taking off"
    
  ```



