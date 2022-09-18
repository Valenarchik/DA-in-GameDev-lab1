# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе #1 выполнил(а):
- Мокрушин Павел Михайлович
- РИ210932

Отметка о выполнении заданий (заполняется студентом):

| Задание | Выполнение | Баллы |
| ------ | ------ | ------ |
| Задание 1 | * | 60 |
| Задание 2 | * | 20 |
| Задание 3 | # | 20 |

[![N|Solid](https://cldup.com/dTxpPi9lDf.thumb.png)](https://nodesource.com/products/nsolid)

[![Build Status](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](https://travis-ci.org/joemccann/dillinger)

## Цель работы
Ознакомиться с основными операторами зыка Python на примере реализации линейной регрессии.

## Задание 1
### Написать программы Hello World на Python и Unity.
Ход работы:
- Для Pyton в отчете привести скриншоты с демонстрацией сохранения
документа google.colab на свой диск с запуском программы, выводящей 
сообщение Hello World

```py
print("Hello World")
```
![Снимок экрана 2022-09-16 211118](https://user-images.githubusercontent.com/101575777/190683471-d878f34c-238a-4e25-b046-769d0ac9b157.png)
![Снимок экрана 2022-09-16 211244](https://user-images.githubusercontent.com/101575777/190683493-429a4393-2700-4066-9d74-5ade0e96f1cc.png)

- Для Unity в отчете привести скриншоты вывода сообщения Hello World на консоль.

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

- Произвести подготовку данных для работы с алгоритмом линейной регрессии. 10 видов данных были установлены случайным образом, и данные находились в линейной зависимости. Данные преобразуются в формат массива, чтобы их можно было вычислить напрямую при использовании умножения и сложения.
![Снимок экрана 2022-09-18 122829](https://user-images.githubusercontent.com/101575777/190890876-cfb9951d-5e96-48a3-86c9-61e322c92624.png)

- Определите связанные функции. Функция модели: определяет модель линейной регрессии wx+b. Функция потерь: функция потерь среднеквадратичной ошибки. Функция оптимизации: метод градиентного спуска для нахождения частных производных w и b.
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
- Начать итерацию


Первая итерация, график неточный (вообще мимо).
![Снимок экрана 2022-09-18 130122](https://user-images.githubusercontent.com/101575777/190892168-c239e826-3b40-4745-ac14-a2f1bd71a323.png)

Вторая итерация, график стал немного поточнее.
![Снимок экрана 2022-09-18 130159](https://user-images.githubusercontent.com/101575777/190892171-35650758-4c98-4c50-b9c7-4a47dc933efe.png)

Третья итерация, видно не повезло, график опять мимо.
![Снимок экрана 2022-09-18 130224](https://user-images.githubusercontent.com/101575777/190892173-9ef71d3e-5117-4676-859b-277834c6213d.png)

Четвертая итерация, чуть лучше второй итерации.
![Снимок экрана 2022-09-18 130243](https://user-images.githubusercontent.com/101575777/190892175-17170c34-8035-4a21-b209-01ba0f2f2454.png)

Думаю, идеально.
![Снимок экрана 2022-09-18 130320](https://user-images.githubusercontent.com/101575777/190892176-d2cab0af-8e5f-43ad-88df-e449e7e6053e.png)

Итог, лучшая функция, полученная в данном эксперименте, это **Y =1.74X + 0.59 **

## Задание 3
### Изучить код на Python и ответить на вопросы
- Должна ли величина loss стремиться к нулю при изменении исходных данных? Ответьте на вопрос, приведите пример выполнения кода, который подтверждает ваш ответ.
- Какова роль параметра Lr? Ответьте на вопрос, приведите пример выполнения кода, который подтверждает ваш ответ. В качестве эксперимента можете изменить значение параметра.
## Выводы


| Plugin | README |
| ------ | ------ |
| Dropbox | [plugins/dropbox/README.md][PlDb] |
| GitHub | [plugins/github/README.md][PlGh] |
| Google Drive | [plugins/googledrive/README.md][PlGd] |
| OneDrive | [plugins/onedrive/README.md][PlOd] |
| Medium | [plugins/medium/README.md][PlMe] |
| Google Analytics | [plugins/googleanalytics/README.md][PlGa] |

## Powered by
**Pavel Mokrushin**
