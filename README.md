# РАЗРАБОТКА СИСТЕМЫ МАШИННОГО ОБУЧЕНИЯ 
Отчет по лабораторной работе #5 выполнил(а):
- Мокрушин Павел Михайлович
- РИ210932

Отметка о выполнении заданий (заполняется студентом):

| Задание | Выполнение | Баллы |
| ------ | ------ | ------ |
| Задание 1 | * | 80 |
| Задание 2 | * | 20 |

## Цель работы
Интеграция экономической системы в проект Unity и обучение ML-Agent.

## Задание 1 и Задание 2
### Измените параметры файла.yaml-агента и определить какие параметры и как влияют на обучение модели. Опишите результаты, выведенные в TensorBoard. 
Изначально решил просто прогнать тот .yaml-файл, который уже присутсвовал в проекте, а потом решил с ним поэкспериментировать.

<img width=100% src="https://user-images.githubusercontent.com/101575777/205045801-66a38add-2ace-40b0-934c-049eca630feb.png" />

---

Открыл .yaml-файл и первое что изменил это max_steps(сколько итераций всего надо сделать) на 100000, так как я явно не стану обучать нейросеть дольше(да это и не нужно). Далее решил поэкспериментировать с batch_size и buffer_size, заметил одну закономерность buffer_size в 10 раз больше batch_size, сперва решил не изметь эту пропорцию, а просто уменьшил их до 100 и 1000 соответственно (вместо 1024 и 10240). Как видно успешность обучения не изменилась, а вот потери (Value Loss) вырости в примерно в 10 раз.

<img width=100% src="https://user-images.githubusercontent.com/101575777/205047864-8aef02c9-b42f-483c-9405-b946d2fac0b9.png" />

---

Решил еще раз меньшить batch_size и buffer_size в 10раз. Когда начал опучение, то сразу заметил некоторые подвисания раз в 4-5секунд, я так предполагаю, что это подведение неких итогов между буферами, они стали проходить чаще. При этом, сильное уменьшение этих параметров плохо сказалось на обучении, оно стало происходить дольше.

<img width=100% src="https://user-images.githubusercontent.com/101575777/205051136-5705c884-6741-4930-871c-65c3360010b7.png" />

---


## Выводы


