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
### Познакомиться с программными средствами для организции передачи данных между инструментами google, Python и Unity

## Задание 1
### Реализовать совместную работу и передачу данных в связке Python - Google-Sheets – Unity. При выполнении задания используйте видео-материалы и исходные данные, предоставленные преподавателя курса.
Ход работы:
- В облачном сервисе google console подключить API для работы с google sheets и google drive.

| Создал апи-ключь и сервер |
| :--: |
| ![АПИ](https://user-images.githubusercontent.com/101575777/194851600-a1bf7b0a-190c-4dc7-910e-79d9ea0bb7dd.png) |

- Реализовать запись данных из скрипта на python в google-таблицу. Данные описывают изменение темпа инфляции на протяжении 11 отсчётных периодов, с учётом стоимости игрового объекта в каждый период.

| Скрипт из Python успешно отправил данные |
| :--: |
| ![Python](https://user-images.githubusercontent.com/101575777/194852828-422f28af-58e8-4dcd-8c8c-c646eb95fca3.png) |

| Данные записались |
| :--: |
| ![таблица](https://user-images.githubusercontent.com/101575777/194855239-e482acef-0b87-4e86-b939-cf11b38c6675.png) |

- Создать новый проект на Unity, который будет получать данные из google-таблицы, в которую были записаны данные в предыдущем пункте.

| Создал проект и в нем скрипт |
| :--: |
| ![проект](https://user-images.githubusercontent.com/101575777/194855825-40c07811-76b0-4608-b7e1-a5ad87a4a25e.png) |

- Написать функционал на Unity, в котором будет воспризводиться аудио-файл в зависимости от значения данных из таблицы.

| Данные успешно обрабатываются |
| :--: |
| ![проект работает](https://user-images.githubusercontent.com/101575777/194856767-39d1900b-3c01-474a-ad1f-c26bd918a2da.png) |

```css
using System;
using System.Collections;
using System.Collections.Generic;
using SimpleJSON;
using UnityEngine;
using UnityEngine.Networking;

namespace Script
{
    public class AudioScript: MonoBehaviour
    {
        public AudioClip goodSpeak;
        public AudioClip normalSpeak;
        public AudioClip badSpeak;
        private AudioSource selectAudio;
        private Dictionary<string, float> dataSet = new();
        private bool statusStart = false;
        private int i = 1;
        private string sheetUrl = "";
        private string apiKey = "";


        void Awake()
        {
            StartCoroutine(GoogleSheets());
        }

        private void Update()
        {
            if (dataSet.Count > 0)
            {
                var data = dataSet[$"Mon_{i}"];
                if (data <= 10 && !statusStart && i != dataSet.Count)
                {
                    StartCoroutine(PlaySelectAudio(goodSpeak));
                    Debug.Log(data);
                }
                else if (data <= 100 && !statusStart && i != dataSet.Count)
                {
                    StartCoroutine(PlaySelectAudio(normalSpeak));
                    Debug.Log(data);
                }
                else if (!statusStart && i != dataSet.Count)
                {
                    StartCoroutine(PlaySelectAudio(badSpeak));
                    Debug.Log(data);
                }
            }
        }

        IEnumerator GoogleSheets()
        {
            var currentResp = UnityWebRequest.Get($"https://sheets.googleapis.com/v4/" +
                                                  $"spreadsheets/{sheetUrl}/values/Лист1?key={apiKey}");
            yield return currentResp.SendWebRequest();
            var rawResp = currentResp.downloadHandler.text;
            var rawJson = JSON.Parse(rawResp);
            foreach (var item in rawJson["values"])
            {
                var parseJson = JSON.Parse(item.ToString());
                var selectRow = parseJson[0].AsStringList;
                dataSet.Add("Mon_" + selectRow[0], float.Parse(selectRow[2]));
            }
        }

        IEnumerator PlaySelectAudio(AudioClip clip)
        {
            statusStart = true;
            selectAudio = GetComponent<AudioSource>();
            selectAudio.clip = clip;
            selectAudio.Play();
            yield return new WaitForSeconds(clip.length);
            statusStart = false;
            i++;
        }
    }
}
```

## Задание 2
### Реализовать запись в Google-таблицу набора данных, полученных с помощью линейной регрессии из лабораторной работы № 1. 
Перенес данные из первой практики, тепрь данные неслучайны.
```py
price = np.array([3, 21, 22, 34, 54, 34, 55, 67, 89, 99, 2, 22, 24, 65, 79, 82, 55, 130, 150, 199])
mon = list(range(1, len(price)))
```
| Данные упешно записались |
| :--: |
| ![результат](https://user-images.githubusercontent.com/101575777/194904139-b7af07ff-63e0-4661-89fc-d882d5264181.png)|

## Задание 3
### Самостоятельно разработать сценарий воспроизведения звукового сопровождения в Unity в зависимости от изменения считанных данных в задании 2.
| Создал скипт расчета инфляции и построения графика на его основе |
| :--: |
| https://user-images.githubusercontent.com/101575777/194904877-00412325-865f-4265-92c5-fa6399d4500b.mp4 |
## Выводы
1. Узнал как отправлять данные через Google API;
2. Узнал как обрабатывать ответ от сервера, полученный в виде JSON;
3. Немного понял, как работать со звуком в Unity;
