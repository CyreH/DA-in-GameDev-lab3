# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе #3 выполнил:
- Гусамов Артур Филаритович
- РИ210950
Отметка о выполнении заданий (заполняется студентом):

| Задание | Выполнение | Баллы |
| ------ | ------ | ------ |
| Задание 1 | * | 60 |
| Задание 2 | * | 20 |
| Задание 3 | * | 20 |

знак "*" - задание выполнено; знак "#" - задание не выполнено;

Работу проверили:
- к.т.н., доцент Денисов Д.В.
- к.э.н., доцент Панов М.А.
- ст. преп., Фадеев В.О.

[![N|Solid](https://cldup.com/dTxpPi9lDf.thumb.png)](https://nodesource.com/products/nsolid)

[![Build Status](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](https://travis-ci.org/joemccann/dillinger)

Структура отчета

- Данные о работе: название работы, фио, группа, выполненные задания.
- Цель работы.
- Задание 1.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Задание 2.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Задание 3.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Выводы.
- ✨Magic ✨

## Цель работы
познакомиться с программными средствами для создания системы машинного обучения и ее интеграции в Unity.

## Задание 1
### Реализовать систему машинного обучения в связке Python - Google-Sheets – Unity.
- Создать новый 3D проект в Unity![image](https://user-images.githubusercontent.com/102403656/198213588-40d16a9d-a47a-4134-8a36-9b7af6e7696e.png)
- Скачать MLAgent и добавить в созданный проект![image](https://user-images.githubusercontent.com/102403656/198215381-764f7847-9b82-42f3-8186-83cd93e7794e.png)
- Пишем серию команд для создания и активации нового ML-агента, а также для скачивания необходимых библиотек:
    - mlagents 0.28.0
    - torch 1.7.1
 ![image](https://user-images.githubusercontent.com/102403656/198271796-33d1ae0f-48a9-45b9-9446-1d1c558e50e6.png)
 ![image](https://user-images.githubusercontent.com/102403656/198271988-30f28357-3de8-42f7-b6e5-0d08463dcb1a.png)
 

## Задание 2
### Реализовать запись в Google-таблицу набора данных, полученных с помощью линейной регрессии из лабораторной работы № 1
- Записать полученные данные при помощи линейной регрессии в google-таблицу![image](https://user-images.githubusercontent.com/102403656/195168128-a396a63b-4941-4d03-b204-f22efcb6fb65.png)

```py

import gspread
import numpy as np

gc = gspread.service_account(filename='unitydatasciense-365216-c7a229f4132b.json')
sh = gc.open('UnitySheets').worksheet('Лист2')

x = [3, 21, 22, 34, 54, 34, 55, 67, 89, 99]
x = np.array(x)
y = [2, 22, 24, 65, 79, 82, 55, 130, 150, 199]
y = np.array(y)


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
    a = a - Lr * da
    b = b - Lr * db
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

a, b = iterate(a, b, x, y, 1)
prediction = model(a, b, x)
loss = loss_function(a, b, x, y)
n = 1
for i in range(0, 1001, 200):
    if i == 0:
        i = 1
    a, b = iterate(a, b, x, y, i)
    prediction = model(a, b, x)
    loss = loss_function(a, b, x, y)
    print(a, b, loss)
    sh.update(('A' + str(n)), str(i))
    sh.update(('B' + str(n)), str(a).replace('.', ',')[1:-1])
    sh.update(('C' + str(n)), str(b).replace('.', ',')[1:-1])
    sh.update(('D' + str(n)), str(loss).replace('.', ','))
    n += 1
```



## Задание 3
### Самостоятельно разработать сценарий воспроизведения звукового сопровождения в Unity в зависимости от изменения считанных данных в задании 2
- Немного изменив код, я добавил возможность самостоятельно вписывать границы "хороших" и "плохих" значений
- При данных значениях(100 и 0) хорошая реакция воспроизведется при значении < 0, плоха при значении > 100, в остальных случаях, соответсвтенно, средняя

    ![image](https://user-images.githubusercontent.com/102403656/195170297-acb95df3-e456-4d9c-95c4-8e72e03b0f6a.png)

```c#

public float badValue;
public float goodValue;

void Update()
    {
        if (dataSet["Mon_" + i.ToString()] <= goodValue & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioGood());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }

        if (dataSet["Mon_" + i.ToString()] > goodValue & dataSet["Mon_" + i.ToString()] < badValue & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioNormal());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }

        if (dataSet["Mon_" + i.ToString()] >= badValue & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioBad());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }
    }
```


## Выводы

При выполнении лабораторной работы я освоил как:
- передавать данные между python, Unity и google-таблицами
- компилировать скрипты в Unity и воспроизводить звуковые файлы

| Plugin | README |
| ------ | ------ |
| Dropbox | [plugins/dropbox/README.md][PlDb] |
| GitHub | [plugins/github/README.md][PlGh] |
| Google Drive | [plugins/googledrive/README.md][PlGd] |
| OneDrive | [plugins/onedrive/README.md][PlOd] |
| Medium | [plugins/medium/README.md][PlMe] |
| Google Analytics | [plugins/googleanalytics/README.md][PlGa] |

## Powered by

**BigDigital Team: Denisov | Fadeev | Panov**
