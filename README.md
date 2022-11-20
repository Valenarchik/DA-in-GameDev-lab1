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

- Сперва сбор данных, для этого я немного модифицировал метод Train из класса Perceptron. Теперь он ленивый и возвращает ошибку после каждой итерации.
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

- Далее я создаю скрипт Google Sheet в котором и буду собирать данные. Чтобы данные были более правдивыми, я провожу один и тот же эксперепемент нескольно раз и беру среднее за каждую эпоху. Пример, провел 4 эксперимента, данные за первую эпоху: 4, 3, 4, 3, итого среднее 3.5. Для удобства основные параметры выношу в инспектор. 
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
- Следующая большая проблема - это разобраться с Google.Apis.Sheets.v4. Скачивание простого NuGet Package не помогает. Uniti просто не видит его. Чтобы исправить проблему пришлось достать dll из пакета для netstandart 2.0 и переместить в сoзданую мной папку Plugins в Assets. И все заработало. Далее я написал методы для отправки данных в мою гугл таблицу. Вот итоговый скрипт.
```cs
using System.Collections.Generic;
using UnityEngine;
using System.IO;
using System.Linq;
using Google.Apis.Sheets.v4;
using Google.Apis.Auth.OAuth2;
using Google.Apis.Services;
using Google.Apis.Sheets.v4.Data;

public class GoogleSheet : MonoBehaviour
{
    public int repeat = 10;
    public int countEpochs = 10;
    public Perceptron or;
    public Perceptron and;
    public Perceptron nand;
    public Perceptron xor;

    private static readonly string[] scopes = {SheetsService.Scope.Spreadsheets};
    private const string ApplicationName = "UnityGoogle";
    private const string SpreadsheetId = "12dLXsTdMMP3_Px-3Ztz9OiXmtFmjWHwiZng1sLvLmE8";
    private readonly char[] alpha = "ABCDEFGHIJKLMNOPQRSTUVWXYZ".ToCharArray();
    private static SheetsService Service { get; set; }

    public void Awake()
    {
        InitializeService();
        AddData(or, "OR");
        AddData(and, "AND");
        AddData(nand, "NAND");
        AddData(xor, "XOR");
    }


    private void InitializeService()
    {
        var credential = GetCredentialsFromFile();
        Service = new SheetsService(new BaseClientService.Initializer()
        {
            HttpClientInitializer = credential,
            ApplicationName = ApplicationName
        });
    }

    private GoogleCredential GetCredentialsFromFile()
    {
        using var stream = new FileStream(
            @"C:\Users\pavel\UnityProjects\UnityDataScience\Assets\Script\unitydatascience-364414-fbce24f1ae2a.json",
            FileMode.Open,
            FileAccess.Read);
        var credential = GoogleCredential.FromStream(stream).CreateScoped(scopes);

        return credential;
    }


    public void AddData(Perceptron perceptron, string sheetName)
    {
        var stat = CollectingStatistics(perceptron);
        var range = $"{sheetName}!B2:{alpha[countEpochs + 1]}";
        var objectMatrix = new List<IList<object>>() {new List<object>(stat.Select(e => (object) e))};
        DeleteEntry(range);
        CreateEntry(range, objectMatrix);
    }

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

    static void CreateEntry(string range, List<IList<object>> objectMatrix)
    {
        var valueRange = new ValueRange();
        valueRange.Values = objectMatrix;

        var appendRequest = Service.Spreadsheets.Values.Append(valueRange, SpreadsheetId, range);
        appendRequest.ValueInputOption =
            SpreadsheetsResource.ValuesResource.AppendRequest.ValueInputOptionEnum.USERENTERED;
        var appendResponse = appendRequest.Execute();
    }

    static void DeleteEntry(string range)
    {
        var requestBody = new ClearValuesRequest();
        var deleteRequest = Service.Spreadsheets.Values.Clear(requestBody, SpreadsheetId, range);
        var deleteResponse = deleteRequest.Execute();
    }
}
```
 
## Задание 3
### Построить визуальную модель работы перцептрона на сцене Unity.

## Выводы
