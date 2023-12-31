# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе #2 выполнила:
- Ануфриева Анастасия Андреевна
- ФО-220007

| Задание | Выполнение | Баллы |
| ------ | ------ | ------ |
| Задание 1 | * | 60 |
| Задание 2 | * | 20 |
| Задание 3 | * | 20 |

знак "*" - задание выполнено; знак "#" - задание не выполнено;

## Цель работы
научиться передавать в Unity данные из Google Sheets с помощью Python.

## Задание 1
### Выберите одну из компьютерных игр, приведите скриншот её геймплея и краткое описание концепта игры. Выберите одну из игровых переменных в игре (ресурсы, внутри игровая валюта, здоровье персонажей и т.д.), опишите её роль в игре, условия изменения / появления и диапазон допустимых значений. Постройте схему экономической модели в игре и укажите место выбранного ресурса в ней.

Я выбрала игру "The Witcher 3: Wild Hunt" и игровую переменную "здоровье персонажей".

![image](https://github.com/anafryy/DA-in-GameDev-lab-2/assets/148782257/302e6c15-de1a-4b74-a52d-311945d7ce76)

Концепт игры "The Witcher 3: Wild Hunt" представляет собой открытый мир фэнтези-ролевой игры, разработанной компанией CD Projekt RED. Игрок управляет Геральтом из Ривии, профессиональным охотником на чудовищ, в мире, полном опасностей, приключений и политических интриг. Игра предлагает уникальную сюжетную линию, ветвящуюся систему выборов и многочисленные задания, которые могут влиять на ход и исход игры.

Здоровье персонажей в игре играет критическую роль. Оно представляет собой показатель физического благополучия и выносливости Геральта. Уровень здоровья влияет на способность персонажа выживать в битвах с врагами. Когда здоровье падает до нуля, персонаж погибает.

Здоровье персонажей может изменяться в зависимости от событий в игре. В боевых ситуациях Геральт может получать повреждения от атак врагов. Он также может использовать зелия или специальные навыки для восстановления здоровья. Здоровье может быть также повышено или восстановлено путем использования предметов, таких как пища или зелья, или с помощью медитации.

Диапазон допустимых значений здоровья зависит от максимального здоровья Геральта, которое может изменяться с прогрессом в игре и по мере прокачки персонажа. В начале игры максимальное здоровье могло быть низким, но по мере прохождения заданий, боевых ситуаций и прокачки героя, оно может увеличиваться.

Экономическая модель в игре "The Witcher 3: Wild Hunt" не представляет собой основного фокуса игры. В игре есть возможность торговли и покупки предметов у торговцев, но она не сильно связана с здоровьем персонажа. Здоровье, скорее, является частью игровой системы, ориентированной на выживание и битвы.

## Задание 2
### С помощью скрипта на языке Python заполните google-таблицу данными, описывающими выбранную игровую переменную в выбранной игре (в качестве таких переменных может выступать игровая валюта, ресурсы, здоровье и т.д.). Средствами google-sheets визуализируйте данные в google-таблице (постройте график, диаграмму и пр.) для наглядного представления выбранной игровой величины.

Скрипт для _Python_ будет генерировать количество очков здоровья главного героя:

```py

import gspread
import numpy as np
gc = gspread.service_account(filename='da-in-gamedev-lab2-268aa5da7b15.json')
sh = gc.open("DA-in-GameDev-lab-2")
hp = np.random.randint(1, 100, 11)
mon = list(range(1,11))
i = 0
while i <= len(mon) - 1:
    i += 1
    if i == 0:
        continue
    else:
        sh.sheet1.update(('A' + str(i)), str(i))
        sh.sheet1.update(('B' + str(i)), str(mon[i]))
        print(i, mon[i])

```
Ссылка на таблицу:
https://docs.google.com/spreadsheets/d/1J9suMJBJNFLx-i96Sefmyo1uaANsbVW6jThdJUqS0xg/edit#gid=0


## Задание 3
### Настройте на сцене Unity воспроизведение звуковых файлов, описывающих динамику изменения выбранной переменной. Например, если выбрано здоровье главного персонажа вы можете выводить сообщения, связанные с его состоянием.

Скрипт для Unity будет запускать звуковые сигналы, в зависимости от количества очков здоровья:
1. Меньше 25 - badSpeak;
2. В промежутке от 25 до 75 - normalSpeak;
3. Больше 75 - goodSpeak.

```cs
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.Networking;
using SimpleJSON;

public class GoogleSheet : MonoBehaviour
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
        if(dataSet.Count == 0)
            return;

        if (dataSet["Mon_" + i.ToString()] > 75 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioGood());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }

        if (dataSet["Mon_" + i.ToString()] >= 25 & dataSet["Mon_" + i.ToString()] <= 75 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioNormal());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }

        if (dataSet["Mon_" + i.ToString()] < 25 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioBad());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }
    }

    IEnumerator GoogleSheets()
    {
        UnityWebRequest curentResp = UnityWebRequest.Get("https://sheets.googleapis.com/v4/spreadsheets/1DXr_F4tzujM2XSaE6TjYJh7Hh8Kw_Lio4XVDpMHuMD0/values/Лист1?key=AIzaSyC5XCKRGFbxalcIt38IN5kBlIeb0ITZwmg");
        yield return curentResp.SendWebRequest();
        string rawResp = curentResp.downloadHandler.text;
        var rawJson = JSON.Parse(rawResp);
        foreach (var itemRawJson in rawJson["values"])
        {
            var parseJson = JSON.Parse(itemRawJson.ToString());
            var selectRow = parseJson[0].AsStringList;
            dataSet.Add(("Mon_" + selectRow[0]), float.Parse(selectRow[1]));
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

```

## Выводы

В процессе выполнения лабораторной работы я научилась передавать данные из Google Sheets в Unity с помощью Python, а также познакомилась с GoogleCloudApi.

## Powered by

**BigDigital Team: Denisov | Fadeev | Panov**
