#python 0415

## 1.10 conditionals part one
```python
def plus(a, b):
    if type(b) is str:
        return None
    else:
        return a+b


print(plus(12,"10"))
```


## 1.11 if else and or
```python
def age_check(age):
    print(f"you are {age}")
    if age < 18:
        print("you can't drink")
    elif age == 18:
        print("you are new to this")
    else:
        print("enjoy yout drink")

age_check(23)
```



