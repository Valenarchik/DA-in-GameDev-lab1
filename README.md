# Перцептрон
Отчет по лабораторной работе #4 выполнил:
- Мокрушин Павел Михайлович
- РИ210932

Отметка о выполнении заданий (заполняется студентом):

| Задание | Выполнение | Баллы |
| ------ | ------ | ------ |
| Задание 1 | * | 60 |
| Задание 2 | * | 20 |
| Задание 3 | * | 20 |

## Цель работы
Ознакомится с понятием Перцептрон, понять как он устроен.

## Задание 1
### В проекте Unity реализовать перцептрон, который умеет производить вычисления:
- **OR**

|Внес начальные условия и желаемый результат|
|:--:|
| ![or](https://user-images.githubusercontent.com/101575777/202890048-fd9b1f5f-3697-45e2-870a-62051da9a35a.png) |

|Как видно, перцептрон успешно проходит обучение, в среднем это занимает около 4 эпох|
|:--:|
|![or2](https://user-images.githubusercontent.com/101575777/202890204-6a11b122-ec49-4670-b575-9e8b28831526.png) |

***

- **AND**

|Также внес начальные условия и желаемый результат|
|:--:|
| ![And](https://user-images.githubusercontent.com/101575777/202890307-b2234b6c-5622-4d80-9d5a-ea38b7077827.png) |

|Иногда перцептрон проходит обучение быстро|
|:--:|
| ![And2](https://user-images.githubusercontent.com/101575777/202890323-767d1e03-c7ab-43c3-81ad-a6fd5cc68eaa.png) |

|А иногда ему не хаватает и восьми эпох, что научиться вести себя как AND|
|:--:|
| ![and3](https://user-images.githubusercontent.com/101575777/202890360-c4fbb30f-96da-4116-8a39-a76965688145.png) |

***

- **NAND**

|Всё также внес начальные условия и желаемый результат|
|:--:|
| ![NAND](https://user-images.githubusercontent.com/101575777/202890565-b4a3d126-087a-4f09-9a7a-c52b96d1d0b9.png) |

|Обучение проходит также как и с оперетором AND|
|:--:|
| ![NAND 2](https://user-images.githubusercontent.com/101575777/202890608-aef0721c-8269-44bb-8299-759c642de089.png) |

***

- **XOR**

|Внес начальные условия и желаемый результат|
|:--:|
| ![XOR](https://user-images.githubusercontent.com/101575777/202890673-ec047153-172b-412f-96a6-f66ea81c6485.png) |

|И тут меня удивило, какое бы большое колличество эпох я не выставлял, обучение проходило безрезультатно|
|:--:|
|![XOR2](https://user-images.githubusercontent.com/101575777/202890681-131171a8-4990-4392-8213-e285a869b057.png) |


|Тут например даже за сотню эпох перецептрон ничему не обучился. Обучение делает только хуже, ведь изначально ошибка меньше.|
|:--:|
| ![XOR3](https://user-images.githubusercontent.com/101575777/202890807-81065ce7-b629-4bca-bf4f-9eb241471c56.png) |

## Задание 2
### Построить графики зависимости колличества эпох от ошибки обучения. Указать от чего зависит необходимое колличество эпох.
Чтобы построить графики я решил использовать уже знакомые Google Sheets. Оставались всего несколько проблем, как союрать данные и как их отправить в таблицу. Руками я это делать не хотел, поэтому решил все это автоматизировать, использовав Google.Apis.Sheets.v4. 

- Сперва сбор данных, для этого я немного модифицировал метод Train из класса Perceptron.
```cs
    public IEnumerable<double> Train(int epochs)
    {
        InitialiseWeights();

        for (var e = 0; e < epochs; e++)
        {
            totalError = 0;
            for (var t = 0; t < trainingSets.Length; t++)
                UpdateWeights(t);
            
            yield return totalError;
        }
    }
```
Теперь он ленивый и возвращает ошибку после каждой итерации.

- Далее я создаю скрипт Google Sheet в котором и буду собирать данные:
```cs
public class GoogleSheet : MonoBehaviour
{
    public int repeat = 10;
    public int countEpochs = 10;
    public Perceptron or;
    public Perceptron and;
    public Perceptron nand;
    public Perceptron xor;
    
    private List<double> CollectingStatistics(Perceptron perceptron)
    {
        var temp = new List<List<double>>();
        for (var i = 0; i < countEpochs; i++)
            temp.Add(new List<double>()); 
        for (var i = 0; i < repeat; i++)
        {
            var j = 0;
            foreach (var totalError in perceptron.Train(countEpochs))
            {
                temp[j].Add(totalError);
                j++;
            }
        }

        return new List<double>(temp.Select(e => e.Average()));
    }
}
```
 
## Задание 3
### Построить визуальную модель работы перцептрона на сцене Unity.

## Выводы
