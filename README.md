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

3. Создать сервисный аккаунт

    ![image](https://user-images.githubusercontent.com/102403656/195144716-3232d2a8-ac21-4071-b617-23481ac58c73.png)

4. Создать ключ и сохранить его в python проэкт![image](https://user-images.githubusercontent.com/102403656/195145832-d14e44b9-4956-4ae9-99a9-01f62ab1b289.png)
5. Создать google таблицу и предоставить доступ сервиснуму аккаунту, созданного ранее![image](https://user-images.githubusercontent.com/102403656/195146307-3e868e90-8cea-4d00-94dd-c582690753ff.png)

- Реализовать запись данных из скрипта на python в google-таблицу.
1. Подключить библеотеки "gspread" и "numpy" и написать скрипт на Python

```py

import gspread
import numpy as np

gc = gspread.service_account(filename='unitydatasciense-365216-c7a229f4132b.json')
sh = gc.open('UnitySheets')
price = np.random.randint(2000, 10000, 11)
mon = list(range(1, 11))
i = 0
while i <= len(mon):
    i += 1
    if i == 0:
        continue
    else:
        tempInf = ((price[i - 1] - price[i - 2]) / price[i - 2]) * 100
        tempInf = str(tempInf).replace('.', ',')
        sh.sheet1.update(('A' + str(i)), str(i))
        sh.sheet1.update(('B' + str(i)), str(price[i - 1]))
        sh.sheet1.update(('C' + str(i)), str(tempInf))

```
2. Таблица автоматически сформируется
    
    ![image](https://user-images.githubusercontent.com/102403656/195152712-7cee96f6-a8e1-4e02-8a06-7190e90bcb28.png)

- Создать новый проект на Unity, который будет получать данные из google-таблицы, в которую были записаны данные в предыдущем пункте.
1. Создаем новый проект![image](https://user-images.githubusercontent.com/102403656/195153984-c83de277-487f-4336-bd07-01b7a6695f7e.png)

- Написать функционал на Unity, в котором будет воспризводиться аудио-файл в зависимости от значения данных из таблицы.
1. Добавляем нужные ассеты и создаем новый звуковой объект![image](https://user-images.githubusercontent.com/102403656/195155178-2b913b19-de8b-444a-a88a-f9a45d82a572.png)
2. Пишем код, который воспроизводит .wav аудио-файлы в зависимости от значения таблицы.

```c#

using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.Networking;
using SimpleJSON;

public class NewBehaviourScript : MonoBehaviour
{
    public AudioClip goodSpeak;
    public AudioClip normalSpeak;
    public AudioClip badSpeak;
    private AudioSource selectAudio;
    private Dictionary<string, float> dataSet = new Dictionary<string, float>();
    private bool statusStart = false;
    private int i = 1;
    
    void Start()
    {
        StartCoroutine(GoogleSheets());
    }

    void Update()
    {
        if (dataSet["Mon_" + i.ToString()] <= 10 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioGood());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }

        if (dataSet["Mon_" + i.ToString()] > 10 & dataSet["Mon_" + i.ToString()] < 100 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioNormal());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }

        if (dataSet["Mon_" + i.ToString()] >= 100 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioBad());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }
    }

    IEnumerator GoogleSheets()
    {
        UnityWebRequest curentResp = UnityWebRequest.Get(url);
        yield return curentResp.SendWebRequest();
        string rawResp = curentResp.downloadHandler.text;
        var rawJson = JSON.Parse(rawResp);
        foreach (var itemRawJson in rawJson["values"])
        {
            var parseJson = JSON.Parse(itemRawJson.ToString());
            var selectRow = parseJson[0].AsStringList;
            dataSet.Add(("Mon_" + selectRow[0]), float.Parse(selectRow[2]));
        }
    }

    IEnumerator PlaySelectAudioGood()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = goodSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(3);
        statusStart = false;
        i++;
    }
    
    IEnumerator PlaySelectAudioNormal()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = normalSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(3);
        statusStart = false;
        i++;
    }

    IEnumerator PlaySelectAudioBad()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = badSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(4);
        statusStart = false;
        i++;
    }
}
```
3. Запускаем программу и слышим реакции в зависимости от случайных значений в таблице![image](https://user-images.githubusercontent.com/102403656/195162963-fd05af88-6611-4afa-8563-308ee259eb54.png)


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
