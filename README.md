# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе #2 выполнил(а):
- Харьков Ярослав Русланович
- РИ222702
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
Научиться передавать в Unity данные из Google Sheets с помощью Python.

## Задание 1
### Выберите одну из компьютерных игр, приведите скриншот её геймплея и краткое описание концепта игры. Выберите одну из игровых переменных в игре (ресурсы, внутри игровая валюта, здоровье персонажей и т.д.), опишите её роль в игре, условия изменения / появления и диапазон допустимых значений. Постройте схему экономической модели в игре и укажите место выбранного ресурса в ней

- Для выполнения работы была выбрана игра Dead Cells. Это двухмерный рогалик-платформер с быстрой боёвкой.
- В игре предусмотрена награда за убийство 60 врагов на уровне без получения урона. Для этого существует счётчик убитых врагов без получения урон (черепок с цифрами в нижнем правом углу, рядом с миникартой). Эта переменная и была взята для выполнения работы.
- Счётчик растёт при убийстве противников, если игрок получает урон, счётчик обнуляется. При достижении 60 убийств иконка счётчика меняется (черепок становится золотым), что гарантирует получние награды даже при последующем получении урона.
![изображение](https://github.com/Quvir/-DA-in-GameDev-lab2/blob/main/deadcells.jpg)

## Задание 2
### С помощью скрипта на языке Python заполните google-таблицу данными, описывающими выбранную игровую переменную в выбранной игре (в качестве таких переменных может выступать игровая валюта, ресурсы, здоровье и т.д.). Средствами google-sheets визуализируйте данные в google-таблице (постройте график, диаграмму и пр.) для наглядного представления выбранной игровой величины.


- Создаем следующий скрипт на языке Python:

```py

import gspread
import numpy as np
gc = gspread.service_account(filename='unityda-401509-0d8e23472dd0.json')
sh = gc.open("UnityDASheet")
mobcount = [0]
damage = np.random.randint(0, 100, 14)
for i in range(1,14):
    if damage[i]<90:
        mobcount.append(mobcount[i-1] + 10)
    else:
        mobcount.append(0)
mon = list(range(1,13))
i = 0
while i <= len(mon):
    i += 1
    if i == 0:
        continue
    else:
        tempmob = mobcount[i] - mobcount[i-1]
        tempmob = str(tempmob)
        tempmob = tempmob.replace('.',',')
        sh.sheet1.update(('A' + str(i)), str(i))
        sh.sheet1.update(('B' + str(i)), str(mobcount[i]))
        if mobcount[i] == 60:
            tempmob = 60
            sh.sheet1.update(('C' + str(i)), str(tempmob))
            print(tempmob)
            break
        sh.sheet1.update(('C' + str(i)), str(tempmob))
        print(tempmob)

```
- В нём сначала случайно заполняется массив "damage" значениями от 0 до 100. Далее берутся эти значения из массива, и если они меньше 90, то в массив "mobcount" добавляется 10 и добавляется 0 если больше 90. Т. е. в массиве "mobcount" содержатся значения необходимого игрового счётчика обновляющегося за каждые 10 убийств (для уменьшения кол-ва значений берём сразу по 10), при этом принимаем что игрок получает урон лишь с 10% шансом при победе над 10-ю врагами.
- В конце заносим динамику изменения счётчика в переменную "tempmob" (при достижении 60 "tempmob" тоже принимает значение 60 как сигнал о получении награды и цикл останавливается) и заполняем значениями "mobcount" и "tempmob" google-таблицу.
- Получается следующая таблица:
![изображение](https://github.com/Quvir/-DA-in-GameDev-lab2/blob/main/%D1%82%D0%B0%D0%B1%D0%BB%D0%B8%D1%86%D0%B0.png)
## Задание 3
### Настройте на сцене Unity воспроизведение звуковых файлов, описывающих динамику изменения выбранной переменной. Например, если выбрано здоровье главного персонажа вы можете выводить сообщения, связанные с его состоянием.

- В Unity создаём пустой GameObject и подключаем к нему .cs скрипт, в котором описывается подключение к google-таблице по API и воспроизведение звуков в игре в соответствии со считываемыми значениями:

```py

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
    private Dictionary<string,float> dataSet = new Dictionary<string, float>();
    private bool statusStart = false;
    private int i = 1;

    // Start is called before the first frame update
    void Start()
    {
        StartCoroutine(GoogleSheets());
    }

    // Update is called once per frame
    void Update()
    {
        if (dataSet["Mon_" + i.ToString()] > 11 & statusStart == false & i != dataSet.Count+1)
        {
            StartCoroutine(PlaySelectAudioGood());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }

        if (dataSet["Mon_" + i.ToString()] >= 0 & dataSet["Mon_" + i.ToString()] < 11 & statusStart == false & i != dataSet.Count+1)
        {
            StartCoroutine(PlaySelectAudioNormal());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }

        if (dataSet["Mon_" + i.ToString()] < 0 & statusStart == false & i != dataSet.Count+1)
        {
            StartCoroutine(PlaySelectAudioBad());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }
    }

    IEnumerator GoogleSheets()
    {
        UnityWebRequest curentResp = UnityWebRequest.Get("https://sheets.googleapis.com/v4/spreadsheets/1f1e2KrDRryqCcVqKhreuvF4aQmzfTr80bhOsxxGX1RQ/values/Лист1?key=AIzaSyAjK4LwPWKm4W-kBgFo-s2WEnAbb6jWomk");
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
- В этом скрипте при увеличении счётчика срабатывает функция PlaySelectAudioNormal(), при обнулнии счётчика функция PlaySelectAudioBad(), а при получении награды функция PlaySelectAudioGood().
- Запускаем скрипт и слышим необходимые звуки, а также наши значения в окне вывода:
![изображение](https://github.com/Quvir/-DA-in-GameDev-lab2/blob/main/%D0%B2%D1%8B%D0%B2%D0%BE%D0%B42.png) 
## Выводы

В ходе данной работы мы научились заполнять google-таблицу данными при помощи Python скрипта, а также передавать эти данные в Unity.

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
