# РАЗРАБОТКА СИСТЕМЫ МАШИННОГО ОБУЧЕНИЯ 
Отчет по лабораторной работе #3 выполнил(а):
- Мокрушин Павел Михайлович
- РИ210932

Отметка о выполнении заданий (заполняется студентом):

| Задание | Выполнение | Баллы |
| ------ | ------ | ------ |
| Задание 1 | * | 60 |
| Задание 2 | * | 20 |
| Задание 3 | * | 20 |

## Цель работы
Познакомиться с программными средствами для создания системы машинного обучения и ее интеграции в Unity.

## Задание 1
### Реализовать систему машинного обучения в связке Python - Google-Sheets – Unity.

| Установил необходимые пакеты (при этом пришлось откатить их до релизнутой версии, так как экспереметальная не работала) |
| :--: |
| ![Снимок экрана 2022-10-24 224720](https://user-images.githubusercontent.com/101575777/197593352-d50d8499-736c-490e-aab7-7c9ef40c8110.png) |

| Далее установил приложение "Anaconda", скачал Python c Torch и соединил с Юнити |
| :--: |
|![Снимок экрана 2022-10-24 224451](https://user-images.githubusercontent.com/101575777/197593896-d4ea5ab3-88d4-4a7f-a69d-50528549d707.png) |

- Начал процесс обучения

https://user-images.githubusercontent.com/101575777/197595645-2dd67c32-862d-4358-b0a9-1e7e9c6ce64e.mp4

- Тройное обучение

https://user-images.githubusercontent.com/101575777/197596435-dc2fbb09-0d64-4573-af02-a373330434d4.mp4

- Еще раз уроили

https://user-images.githubusercontent.com/101575777/197596450-024655f0-25a9-4b12-96da-0dcd23039eaa.mp4

- И еще раз

https://user-images.githubusercontent.com/101575777/197596465-48c9f8ff-668e-4a37-b707-3205c221bb8e.mp4

**Результат**
Получилась неплохая нейронная сеть, которая хоть и медленно, но способна идти к кубику.

https://user-images.githubusercontent.com/101575777/197597179-57bdf4f1-dc94-4f53-b68c-c527bf48f071.mp4


## Задание 2
### Подробно опишите каждую строку файла конфигурации нейронной сети.
``` yaml
behaviors:
  RollerBall: # Название 
    trainer_type: ppo # Тип обучения с поощрением
    hyperparameters:
      batch_size: 10 # Общее число тренировочных объектов, представленных в одном пакете.
      buffer_size: 100 # Количество опытов, которые необходимо собрать перед обновлением модели.
      learning_rate: 3.0e-4 # Коэффициент скорости обучения — это параметр градиентных алгоритмов обучения нейронных сетей,
                            # позволяющий управлять величиной коррекции весов на каждой итерации. Уменьшается с числом итераций.
      beta: 5.0e-4 # Соответствует силе регуляризации энтропии (мутаций)
      epsilon: 0.2 # Небольшое вещественное число, добавляемое к дисперсии, чтобы избежать деления на 0.
      lambd: 0.99 # Определяет насколько агент полагается на свою текущую оценку стоимости при вычислении обновленной оценки стоимости. 
                  # Низкие значения соответствуют большей зависимости от текущей оценки стоимости,
                  # а высокие значения соответствуют большей зависимости от фактических вознаграждений
      num_epoch: 3 # Число эпох. Эпоха это, когда весь датасет прошел через нейронную сеть в прямом
                   # и обратном направлении только один раз.
      learning_rate_schedule: linear #Определяет как будет меняться скорость обучения, в данном случае - линейно.
    network_settings:
      normalize: false # Соответствует тому, применяется ли нормализация к входным данным векторного наблюдения.
      hidden_units: 128 # Колличество нейронов в одном слое нейронной сети.
      num_layers: 2 # Колличесво слоев нейронной сети.
    reward_signals:
      extrinsic:
        gamma: 0.99 # коэффициент дисконтирования для будущих вознаграждений
        strength: 1.0 # коэффициент, на который умножается необработанное вознаграждение (От 0.01 до 1.0)
    max_steps: 500000 # максимальное число итераций
    time_horizon: 64 # соответствует количеству шагов опыта, которые нужно собрать для каждого агента,
                     # прежде чем добавлять его в буфер опыта. 
    summary_freq: 10000 # через сколько итераций будет промежуточный результат
```

## Задание 3
### Доработайте сцену и обучите ML-Agent таким образом, чтобы шар перемещался между двумя кубами разного цвета.
**В коде присутсвуют комментарии**

```cs

using Unity.MLAgents;
using Unity.MLAgents.Actuators;
using Unity.MLAgents.Sensors;
using UnityEngine;

public class RollerAgent : Agent
{
    private Rigidbody rBody;
    [SerializeField] private Transform targetOne;
    [SerializeField] private Transform targetTwo; // Добавил второй обьект-цель
    [SerializeField] private float forceMultiplier = 10;
    private float delta = 1.42f;
    
    private Vector3 GetRandomPosition => new Vector3(Random.value * 8-4, 0.5f, Random.value * 8-4);
    private Vector3 GetMiddle(Vector3 v1, Vector3 v2) => (v1 + v2) / 2;
    void Start()
    {
        rBody = GetComponent<Rigidbody>();
    }
    
    public override void OnEpisodeBegin()
    {
        if (this.transform.localPosition.y < 0)
        {
            this.rBody.angularVelocity = Vector3.zero;
            this.rBody.velocity = Vector3.zero;
            this.transform.localPosition = new Vector3(0, 0.5f, 0);
        }

        targetOne.localPosition = GetRandomPosition;
        targetTwo.localPosition = GetRandomPosition; // также задаю ему случайное положение по окончанию эпизода.
    }
    public override void CollectObservations(VectorSensor sensor)
    {
        // нахожу середину и добавляю ее в "чувства" агента
        var middle = GetMiddle(targetOne.localPosition, targetTwo.localPosition); 
        sensor.AddObservation(middle);
        sensor.AddObservation(this.transform.localPosition);
        sensor.AddObservation(rBody.velocity.x);
        sensor.AddObservation(rBody.velocity.z);
    }
  
    public override void OnActionReceived(ActionBuffers actionBuffers)
    {
        Vector3 controlSignal = Vector3.zero;
        controlSignal.x = actionBuffers.ContinuousActions[0];
        controlSignal.z = actionBuffers.ContinuousActions[1];
        rBody.AddForce(controlSignal * forceMultiplier);

        var localPosition = this.transform.localPosition;
        var middle = GetMiddle(targetOne.localPosition, targetTwo.localPosition); 
        float distanceToMiddle = Vector3.Distance(localPosition, middle);
        
        // проверяю достиг ли агент середины.
        if (distanceToMiddle < delta)
        {
            SetReward(1.0f);
            EndEpisode();
        }
        else if (this.transform.localPosition.y < 0)
        {
            EndEpisode();
        }
    }
}

```

Агент успешно проходит обучение


https://user-images.githubusercontent.com/101575777/197723290-fd650641-a624-457e-9000-769582f31623.mp4


## Выводы
### Дайте развернутый ответ, что такое игровой баланс и как системы машинного обучения могут быть использованы для того, чтобы его скорректировать.

>>>**Что такое игровой баланс?** Игровой баланс — это то, как игра в совокупности ощущается игроком. Это ощущение состоит из множества сложных взаимодействий, именно поэтому до сих пор не существует рецепта, как создать игровой баланс. Существует лишь множество методов, которые призваны помочь сделать игровой баланс. Зачем вообще нужен игровой баланс? Он нужен, чтобы удерживать игроков в игре, без него игра бы слишком быстро надоедала.  Игровой баланс — это не весы (да, в каком-то представлении, его можно представить весами, но не во всех), игровой баланс — это уровень *фана* в игре, если *фана много*, то и игровой баланс был успешно достигнут. То бишь разработчик при создании гейм-дизайна не обязан строго математически доказывать, что все игровые сущности равны (как бы сравнивая их на весах), а скорее должен опираться на его опыт и ощущения.

>>>Теперь ответим на второй вопрос, **как системы машинного обучения могут быть использованы для того, чтобы его скорректировать?** Я считаю, что главным плюсом нейронных сетей является их гибкость и адаптивность. Если позволить нейросети управлять некоторым аспектом игрового баланса, например экономики, и дать ей возможность обучаться, то в некотором роде мы переложили часть своей ответственности, как разработчика, на нейросеть. В чем плюс такого подхода? Во-первых, нейросеть никогда не устает, она обрабатывает данные постоянно. Во-вторых, она способна мгновенно отреагировать на некоторый триггер (к примеру, огромную скупку некоторого товара). А в чем минусы? На мой взгляд, это уменьшение контроля над игрой, как и любое перекладывание ответственности, оно несет издержки. Нейросеть ведь не осознает, что  она делает и зачем это она делает. Поэтому она может что-нибудь и учудить.


