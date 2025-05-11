파이썬 스크립트로 while loop를 작성하고, 특정 조건 시에 break로 루프를 빠져나오도록 하였는데, 특정 조건이 성립하는데도 break가 호출되지 않는 경우가 있었음. 삽질을 거듭하며 확인해보니 if 문 내에 \==가 아닌 is 오퍼레이터를 사용하고 있었음. \==와 is는 비슷한 일을 수행하는 오퍼레이터지만 분명히 구분해야하는 차이가 있음. 이는 아래와 같음.

> The `==`operator compares the value or **equality** of two objects, whereas the Python `is` operator checks whether two variables point to the same object in memory. In the vast majority of cases, this means you should use the equality operators `==` and `!=`, except when you’re comparing to `None`.

값을 비교할 때는 == 를 사용하도록 하자

**References**
- [https://realpython.com/courses/python-is-identity-vs-equality/](https://realpython.com/courses/python-is-identity-vs-equality/)