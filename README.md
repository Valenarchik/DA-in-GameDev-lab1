# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе #1 выполнил(а):
- Мокрушин Павел Михайлович
- РИ210932

Отметка о выполнении заданий (заполняется студентом):

| Задание | Выполнение | Баллы |
| ------ | ------ | ------ |
| Задание 1 | * | 60 |
| Задание 2 | * | 20 |
| Задание 3 | * | 20 |

## Цель работы
Ознакомиться с основными операторами зыка Python на примере реализации линейной регрессии.

## Задание 1
### Написать программы Hello World на Python и Unity.
Ход работы:
- **Для Pyton в отчете привести скриншоты с демонстрацией сохранения
документа google.colab на свой диск с запуском программы, выводящей 
сообщение Hello World**

```py
print("Hello World")
```
![Снимок экрана 2022-09-16 211118](https://user-images.githubusercontent.com/101575777/190683471-d878f34c-238a-4e25-b046-769d0ac9b157.png)
![Снимок экрана 2022-09-16 211244](https://user-images.githubusercontent.com/101575777/190683493-429a4393-2700-4066-9d74-5ade0e96f1cc.png)

- **Для Unity в отчете привести скриншоты вывода сообщения Hello World на консоль.**

```css
using UnityEngine;

public class HelloWorld : MonoBehaviour
{
    void Start()
    {
        Debug.Log("Hello World");
    }
}
```
![Снимок экрана 2022-09-16 212609](https://user-images.githubusercontent.com/101575777/190686382-a3e6d1bf-d52b-43b0-b8bb-c7ce92c0b77f.png)

## Задание 2
### Пошагово выполнить каждый пункт раздела "ход работы" с описанием и примерами реализации задач
Ход работы:
- **Произвести подготовку данных для работы с алгоритмом линейной регрессии. 10 видов данных были установлены случайным образом, и данные находились в линейной зависимости. Данные преобразуются в формат массива, чтобы их можно было вычислить напрямую при использовании умножения и сложения.**

![Снимок экрана 2022-09-18 122829](https://user-images.githubusercontent.com/101575777/190890876-cfb9951d-5e96-48a3-86c9-61e322c92624.png)

- **Определите связанные функции. Функция модели: определяет модель линейной регрессии wx+b. Функция потерь: функция потерь среднеквадратичной ошибки. Функция оптимизации: метод градиентного спуска для нахождения частных производных w и b.**

```py 
import numpy as np
import matplotlib.pyplot as plt


def model(a, b, x):
    return a*x+b


def loss_function(a, b, x, y):
    num = len(x)
    prediction = model(a, b, x)
    return (0.5/num) * (np.square(prediction - y)).sum()


def optimize(a, b, x, y):
    num = len(x)
    prediction = model(a, b, x)
    da = (1.0/num) * ((prediction - y)*x).sum()
    db = (1.0/num) * ((prediction - y).sum())
    a -= Lr*da
    b -= Lr*db
    return a, b


def iterate(a, b, x, y, times):
    for i in range(times):
        a, b = optimize(a, b, x, y)
    return a, b


a = np.random.rand(1)
print(a)
b = np.random.rand(1)
print(b)
Lr = 0.000001

x = np.array([3, 21, 22, 34, 54, 34, 55, 67, 89, 99])
y = np.array([2, 22, 24, 65, 79, 82, 55, 130, 150, 199])

a, b = iterate(a, b, x, y, 1)
prediction = model(a, b, x)
loss = loss_function(a, b, x, y)
print(a, b, loss)
plt.scatter(x, y)
plt.plot(x, prediction)
plt.show()
```
- **Начать итерацию**

1. Первая итерация, график неточный (вообще мимо).
![Снимок экрана 2022-09-18 130122](https://user-images.githubusercontent.com/101575777/190892168-c239e826-3b40-4745-ac14-a2f1bd71a323.png)

2. Вторая итерация, график стал немного поточнее.
![Снимок экрана 2022-09-18 130159](https://user-images.githubusercontent.com/101575777/190892171-35650758-4c98-4c50-b9c7-4a47dc933efe.png)

3. Третья итерация, видно не повезло, график опять мимо.
![Снимок экрана 2022-09-18 130224](https://user-images.githubusercontent.com/101575777/190892173-9ef71d3e-5117-4676-859b-277834c6213d.png)

4. Четвертая итерация, чуть лучше второй итерации.
![Снимок экрана 2022-09-18 130243](https://user-images.githubusercontent.com/101575777/190892175-17170c34-8035-4a21-b209-01ba0f2f2454.png)

5. Думаю, идеально.
![Снимок экрана 2022-09-18 130320](https://user-images.githubusercontent.com/101575777/190892176-d2cab0af-8e5f-43ad-88df-e449e7e6053e.png)

**Итог**, лучшая функция, полученная в данном эксперименте, это **Y =1.74X + 0.59**

## Задание 3
### Изучить код на Python и ответить на вопросы
- **Должна ли величина loss стремиться к нулю при изменении исходных данных? Ответьте на вопрос, приведите пример выполнения кода, который подтверждает ваш ответ.**

**Ответ:** Да, величина loss может стремиться к нулю. Это возможно если изначально точки были заданы, используя линейную функцию (то бишь находились на одной прямой)
![Снимок экрана 2022-09-18 151842](https://user-images.githubusercontent.com/101575777/190915885-163f39e9-0886-4e63-a251-cb2b89af61f8.png)

1. Но стоит заметь, что если параметр b отличен от нуля (прямая не проходит через начало координат), то алгоритм не способен найти нужный график и величина loss растет тем больше, чем параметр b больше по модулю.
![Снимок экрана 2022-09-18 201109](https://user-images.githubusercontent.com/101575777/190915963-4f3685f5-eb22-487b-bfae-6edc8a1cf8a4.png)

2. Если изначальная функция не линейна, то величина loss быстро возрастает.
![Снимок экрана 2022-09-18 202742](https://user-images.githubusercontent.com/101575777/190915983-cce3e065-2b9a-4c96-8af1-dcf971264ece.png)

3. Но я обнаружил исключение, если данные расположены на функции корня, то loss тоже стремится к нулю.
![Снимок экрана 2022-09-18 203259](https://user-images.githubusercontent.com/101575777/190915992-ec87679e-5121-4e10-a240-9e520e32b068.png)

4. При этом, что странно, величина loss относительно невелика на абсолютно случайных данных. 
![Снимок экрана 2022-09-18 201457](https://user-images.githubusercontent.com/101575777/190915989-cb8b4c03-1b35-41a8-b6b8-3ccd7ef519d9.png)

**Код тестов:** 
```py
import numpy as np
import matplotlib.pyplot as plt
import random


def model(a, b, x):
    return a * x + b


def loss_function(a, b, x, y):
    num = len(x)
    prediction = model(a, b, x)
    return (0.5 / num) * (np.square(prediction - y)).sum()


def optimize(a, b, x, y):
    num = len(x)
    prediction = model(a, b, x)
    da = (1.0 / num) * ((prediction - y) * x).sum()
    db = (1.0 / num) * ((prediction - y).sum())
    a -= Lr * da
    b -= Lr * db
    return a, b


def iterate(a, b, x, y, times):
    for i in range(times):
        a, b = optimize(a, b, x, y)
    return a, b


def get_data(func, times):
    x = [0]
    y = [func(0)]
    for i in range(0, times):
        x.append(x[i] + int(random.random()*10))
        y.append(func(x[i+1]))
    return x, y


def get_regression(func, times):
    data = get_data(func, times)
    a = np.random.rand(1)
    b = np.random.rand(1)

    x = np.array(data[0])
    y = np.array(data[1])
    a, b = iterate(a, b, x, y, 10000)
    plt.scatter(x, y)
    plt.plot(x, model(a, b, x))
    plt.show()
    print(f"Потери:{int(loss_function(a, b, x, y))}")


Lr = 0.000001
get_regression(lambda x: x * 2, 10)
get_regression(lambda x: x * 2 + 10, 10)
get_regression(lambda x: x * 2 + 100, 10)
get_regression(lambda x: x * x, 10)
get_regression(lambda x: 2 ** x, 10)
get_regression(lambda x: x ** 0.5, 10)
get_regression(lambda x: int(random.random()*10), 10)
```

- **Какова роль параметра Lr? Ответьте на вопрос, приведите пример выполнения кода, который подтверждает ваш ответ. В качестве эксперимента можете изменить значение параметра.**

**Ответ:** Параметр Lr определяет с какой скорость будет подравниваться график, тем самым определяя точность. Если Lr маленький, то график медленно выравнивается, но зато точнее. Если Lr сслишком велик, то график вообще выскакивает за ось OX, то есть тоности нет.

1.Как видно, что даже за одну инерацию, имея неслучайные начальные параметры  и относительно большой параметр Lr, график сильно перескочил вверх.
![Снимок экрана 2022-09-18 215735](https://user-images.githubusercontent.com/101575777/190919136-886ba818-c61a-445c-bacb-11dd6733fda7.png)

2.А здесь наоборот из-за того, что параметр Lr слишком мал и колличество итераций недостаточно, график не успел поднятся.
![Снимок экрана 2022-09-18 222145](https://user-images.githubusercontent.com/101575777/190920341-621d7b71-64c0-4552-b7c4-ef2b5b2a024a.png)

**Код примера:** 
```py
import numpy as np
import matplotlib.pyplot as plt


def model(a, b, x):
    return a * x + b


def loss_function(a, b, x, y):
    num = len(x)
    prediction = model(a, b, x)
    return (0.5 / num) * (np.square(prediction - y)).sum()


def optimize(a, b, x, y):
    num = len(x)
    prediction = model(a, b, x)
    da = (1.0 / num) * ((prediction - y) * x).sum()
    db = (1.0 / num) * ((prediction - y).sum())
    a -= Lr * da
    b -= Lr * db
    return a, b


def iterate(a, b, x, y, times):
    for i in range(times):
        a, b = optimize(a, b, x, y)
    return a, b


def get_regression():
    a = 0
    b = 0
    x = np.array([3, 21, 22, 34, 54, 34, 55, 67, 89, 99])
    y = np.array([2, 22, 24, 65, 79, 82, 55, 130, 150, 199])
    a, b = iterate(a, b, x, y, Count_iteration)
    prediction = model(a, b, x)
    loss = loss_function(a, b, x, y)
    print(f"a = {a} b = {b} loss = {loss} Lr = {Lr}")
    plt.scatter(x, y)
    plt.plot(x, prediction)
    plt.show()


Count_iteration = 10
for i in range(1, 10):
    Lr = 1/(10 ** i)
    get_regression()

```
## Выводы
1. Научился устанавливать библеотеки в PyCharm и через pip install;
2. Научался писать простейшие функции в Pynon;
3. Немного понял, что такое линейная регрессия;
