# **Содержание**  
1. [Тема и целевая аудитория](#тема-и-целевая-аудитория)  
2. [Расчётная нагрузка](#расчётная-нагрузка)
3. [Глобальная балансировка нагрузки](#3-глобальная-балансировка-нагрузки)
5. [Логическая схема базы данных](#5логическая-схема-базы-данных)
6. [Список источников](#список-источников)  

---

## 1. Тема и целевая аудитория  
**Spotify** — это сервис для стриминга различного контента (треков, подкастов и видеороликов) от авторов со всего мира.  

---

##  Функционал MVP  
1. Регистрация и авторизация пользователей  
2. Прослушивание треков/аудиокниг/подкастов  
3. Создание плейлиста  
4. Добавление в избранное  
5. Поиск трека/аудиокниги/подкаста  
6. Прослушивание чартов  
7. История прослушиваний пользователей  

---

## Анализ трафика и вовлечённости  

- Ежемесячно на Spotify зарегистрировано **393 млн** активных пользователей.  
- На Spotify более **100 млн** треков.  
- Spotify достигает **32,5 млн** слушателей подкастов в месяц.  
- Около **4 млрд** плейлистов.  
- У сервиса **246 млн** подписчиков **Premium** по всему миру.  
- В среднем каждый пользователь слушает музыку **150 минут в день**.  

### Веб-трафик по странам  
![Веб трафик по странам](/img/country.png)

### Целевая аудитория  
![Целевая аудитория](/img/demography.png)

---

## 2. Расчётная нагрузка  

| Метрика                               | Значение |
|---------------------------------------|----------|
| MAU (ежемесячные активные пользователи) | 602 млн  |
| DAU (ежедневные активные пользователи) | 104 млн  |
| Количество треков                     | 100 млн  |
| Количество плейлистов                  | 4 млрд   |
| Среднее количество прослушиваний треков в день | 150 мин |
| Среднее количество поисков за день     | 62.8 млн |

---

### Средний размер хранилища  

Размер треков зависит от качества. Допустим среднее время трека - 3 минуты. Трек хранится в таких вариантах качества: низкое весит около 540KB, среднее - 2.16MB, высокое - 	3.6MB, очень высокое - 	7.2MB. 
Для того, чтобы расчитать размер библиотеки, возьмем средний размер треков. Таким образом, на каждую композицию выделяется в среднем 13.5 МБ. 


| Данные библиотеки    | Размер единицы |
|----------------------|----------------|
| Треки                | ~13.5 МБ       |
| Альбомы*             | ~600.5КБ       |
| Плейлисты            | ~600.5КБ       |
| Сохраненные артисты**  | ~400.04КБ    |


| Данные               | Размер единицы | Количество на пользователя | Размер на пользователя |
|----------------------|--------------|--------------------------|------------------------|
| Плейлисты           | ~600.5 КБ   | 1                        | 600.7 КБ ***             |
| Данные пользователя | ~1.1 МБ ****  | 1                        | 1.1 МБ                  |

#### Предположения  
\* Альбом включает в себя ID трека(16Б), длительность(2Б), обложку(600КБ), название(~60Б), дату релиза (8Б), количество треков (~2Б), автора (~40Б). Предположим, что треков в альбоме 1. То размер альбома будет составлять ≈ 600.5 КБ.

С технической точки зрения, плейлист и альбом одно и тоже, поэтому такие же характеристики оставим и для плейлиста.  

\** Сохраненный артист в библиотеке включает в себя имя артиста (~40Б), обложку (~400 КБ). Итого 400.04 КБ

\*** Предположим что треков в плейлисте пользователя 10, то размер плейлиста будет составлять 600.5 КБ + ID трека × 10

\**** Данные пользователя включают:  
- Имя, аватар, email ~ 1МБ  
- Лайки (избранные треки, альбомы, артисты) ~50 КБ  
- История прослушиваний (ID треков, временные метки) ~30 КБ  
- Настройки аккаунта (язык, плейлисты в оффлайн-режиме) ~10 КБ  

---

### Размер хранения по типам данных  

| Тип данных   | Размер                             |
|-------------|------------------------------------ |
| Треки       | 100 млн × 13.5 МБ = **1350 ПБ**     |
| Плейлисты   | 4 млрд × 600.7 КБ = **2.4 ПБ**      |
| Пользователи | 602 млн × 1.1 МБ = **662.2 ТБ**    |

---

### Сетевой трафик  

| Действие пользователя  | Суммарный суточный трафик | Среднее потребление | Пиковое потребление | RPS (запросов в секунду) |
|------------------------|--------------------------|----------------------|----------------------|------------------|
| Прослушивание треков  | **46.8 ПБ**               | **4.33 Тбит/с**       | **8.66 Тбит/с**       | **10.83M**       |
| Поиск треков         | **15.7 ТБ**                | **1.45 Гбит/с**      | **2.9 Гбит/с**       | **728**          |
| Добавление в избранное | **0.374 ТБ**              | **33.85 Мбит/с**     | **67.7 Мбит/с**      | **2407**         |
| Создание плейлистов  | **0.99 ТБ**               | **94.04 Мбит/с**     | **188.08 Мбит/с**    | **1204**         |
| История прослушиваний | **15.87 ТБ**              | **1.5 Тбит/с**       | **3.01 Тбит/с**      | **24074**        |

### Как рассчитано:
1. **Прослушивание треков**:  
   - `104 млн пользователей × 150 минут / 86400 секунд × 3 МБ / минута = 529 ПБ / день`  
   - `529 ПБ × 8 бит = 4333 Пбит`  
   - `4333 Пбит / (24 × 3600) ≈ 50.15 Тбит/с`  
   - Пик: удвоение нагрузки в часы пик (`50.15 × 2 = 100.31 Тбит/с`)  
   - `RPS = 104 млн × 150 × 60 / 86400 ≈ 10.83M`  

2. **Поиск треков**:  
   - `62.8 млн поисков × 250 КБ (ответ) = 14.97 ТБ`  
   - `14.97 ТБ × 8 / (24 × 3600) ≈ 1.42 Гбит/с`  
   - Пик: `1.42 × 2 = 2.84 Гбит/с`  
   - `RPS = 62.8M / 86400 ≈ 727`  

3. **Добавление в избранное**:  
   - `104 млн × 2 добавления × 1.8 КБ = 357 ГБ (0.357 ТБ)`  
   - `0.357 ТБ × 8 / (24 × 3600) ≈ 33.85 Мбит/с`  
   - Пик: `33.85 × 2 = 67.71 Мбит/с`  
   - `RPS = 104 млн × 2 / 86400 ≈ 2407`  

4. **Создание плейлистов**:  
   - `104 млн × 1 плейлист × 10 КБ = 992 ГБ (0.99 ТБ)`  
   - `0.99 ТБ × 8 / (24 × 3600) ≈ 94.04 Мбит/с`  
   - Пик: `94.04 × 2 = 188.08 Мбит/с`  
   - `RPS = 104 млн × 1 / 86400 ≈ 1204`  

5. **История прослушиваний**:  
   - `104 млн × 20 записей × 8 КБ = 15.87 ТБ`  
   - `15.87 ТБ × 8 / (24 × 3600) ≈ 1.5 Тбит/с`  
   - Пик: `1.5 × 2 = 3.01 Тбит/с`  
   - `RPS = 104 млн × 20 / 86400 ≈ 24074`  

---

## 3. Глобальная балансировка нагрузки

### Функциональное разбиение по доменам 

Из пункта 2 видно, что большая часть нагрузки приходится на прослушивание треков, поэтому выделять отдельные домены нет смысла. 

### Обоснования расположения ДЦ
Разбивка регионов, из которых больше всего пользовтелей Spotify:
![Разбивка регионов](/img/byRegion.png)

#### Расположение ДЦ


| **Регион**             | **Страна**       | **Город**        | **Обоснование** |
|------------------------|-----------------|----------------|----------------|
| Европа                 |Швеция       |Стокгольм   | Штаб-квартира Spotify, развитая инфраструктура |
|                        |Нидерланды   | Амстердам  | Один из крупнейших интернет-узлов (AMS-IX), высокая пропускная способность сетей |
|                        |Германия     |Франкфурт   | Крупный дата-центр Европы (DE-CIX), развитая облачная инфраструктура  |
|                        |Великобритания |Лондон    | Высокая концентрация дата-центров, удобная связь с Европой и США  |
|Северная Америка        |США          |Нью-Йорк    | Ключевой интернет-хаб, мощные дата-центры, соединение с Европой |
|                        |Канада       |Торонто     | Крупнейший интернет-узел Канады, близость к США |
|Латинская Америка       |Бразилия     |Сан-Паулу   | Крупнейший рынок региона, развитая сеть подводных кабелей с США и Европой |
|                        |Чили         |Сантьяго    | Один из главных облачных узлов в Латинской Америке |
|Азия                    |Япония       |Токио       | Высокая интернет-проникаемость, связь с США через подводные кабели |
|                        |Сингапур     |Сингапур    | Крупнейший интернет-узел Азии |
|Ближний Восток          |ОАЭ          |Дубай       | Ведущий интернет-центр региона, соединение с Европой и Азией и Африкой |

Так как Spotify работает с Google Cloud, поэтому выбор будет основываться на странах, где есть GCP-инфраструктура.

**Расположение CDN**

| Страна      | Город           |Обоснования выбора    |
|-------------|-----------------|----------------------|
|Швеция       |Стокгольм        |Основатель. В Стокгольме находится штаб-квартира|
|Нидерланды   |Амстердам        |Один из крупнейших интернет-узлов (AMS-IX)|
|Германия     |Франкфурт, Берлин|Крупный центр дата-центров(DE-CIX)|
|Бельгия      |Сен-Жислен       |Центральное расположение в Европе|
|Франция      |Марсель          |Один из крупнейших подводных интернет-узлов в Европе (каналы связи с Африкой и Азией)|
|Италия       |Милан            |Интернет-хаб для Италии и Южной Европы, соединение с подводными кабелями|
|Великобритания|Лондон          |Крупнейший интернет-центр Европы|
|Канада        |Торонто         |Крупнейший интернет-узел Канады. Близость к США|
|              |Ванкувер        |Выход к азиатским рынкам (близость к Тихоокеанскому региону)|
|США           |Техас           |Близость к Латинской Америке|
|              |Нью-Йорк        |Крупный интернет-хаб, соединение с Европой|
|              |Чикаго          |Центральное расположение в США|
|Бразилия      |Сан-Паулу       |Развитая сеть подводных кабелей, соединяющих Бразилию с США и Европой|
|Чили          |Сантьяго        |Один из главных облачных узлов в регионе|
|Мексика       |Мехико          |Близость к США|
|Колумбия      |Богота          |Географически выгодное расположение для соединения с США и Южной Америкой|
|Аргентина     |Буэнос-Айрес    |Крупные города с высокой интернет-проникаемостью|
|Япония        |Токио           |Подключение к США через подводные кабели|
|Южная Корея   |Сеул            |Интернет-центр для Кореи и соединение с Японией и Китаем|
|Китай         |Пекин           |Важный узел для интернета, соединение с другими регионами Китая|
|Сингапур      |Сингапур        |Один из крупнейших интернет-узлов Азии|
|Индонезия     |Джакарта        |Крупнейший рынок Юго-Восточной Азии|
|Австралия     |Сидней          |Крупнейший интернет-центр Австралии, соединение с Азией и США|
|ОАЭ           |Дубай           |Ведущий интернет-центр для Ближнего Востока, соединение с Европой и Азией|

### Распределение запросов по типам и датацентрам 

Количество ДЦ равняется 11.

#### Количество датацентров по регионам

| Регион                  | Доля нагрузки | Датацентры |
|-------------------------|--------------|------------|
| Европа                 | 34%          | **4**      |
| Северная Америка + Канада | 24%     | **3**      |
| Латинская Америка      | 22%          | **2**      |
| Остальное             | 20%           | **2**      |
| **Всего**             | **100%**      | **11**     |

---

#### RPS по регионам

 **Прослушивание треков (10.83M RPS)**

| Регион                  | Общий RPS по региону | RPS на 1 ДЦ |
|-------------------------|---------------------|------------|
| Европа                 |10.83M × 0.34 = 3.68M |3.68M/4 = 920K |
| Северная Америка + Канада |10.83M × 0.24 = 2.60M |2.60M/3 = 867K |
| Латинская Америка      | 10.83M × 0.22 = 2.38M | 2.38M/2 = 1.19M  |
| Остальное             | 10.83M × 0.20 = 2.17M |2.17M/2 = 1.08M |

---

 **Поиск треков (728 RPS)**

| Регион                  | Общий RPS по региону | RPS на 1 ДЦ |
|-------------------------|---------------------|------------|
| Европа                 | 728 × 0.34 = 248 |248/4 = 62 |
| Северная Америка + Канада |728 × 0.24 = 175 |175/3 = 58 |
| Латинская Америка      |728 × 0.22 = 160 |160/2 = 80 |
| Остальное             | 728 × 0.20 = 146 | 146/2 = 73  |


---

 **Добавление в избранное (2407 RPS)**

| Регион                  | Общий RPS по региону | RPS на 1 ДЦ |
|-------------------------|---------------------|------------|
| Европа                 |2407 × 0.34 = 818 | 818/4 = 205 |
| Северная Америка + Канада |2407 × 0.24 = 577 | 577/3 = 192 |
| Латинская Америка      |2407 × 0.22 = 529 | 529/2 = 265 |
| Остальное             |2407 × 0.20 = 481 |481/2 = 241 |

---

### **Создание плейлистов (1204 RPS)**

| Регион                  | Общий RPS по региону | RPS на 1 ДЦ |
|-------------------------|---------------------|------------|
| Европа                 | 1204 × 0.34 = 409 | 409/4 = 102 |
| Северная Америка + Канада |1204 × 0.24 = 289 | 289/3 = 96|
| Латинская Америка      | 1204 × 0.22 = 265 | 265/2 = 133  |
| Остальное             |1204 × 0.20 = 241 | 241/2 = 121  |

---

 **История прослушиваний (24074 RPS)**

| Регион                  | Общий RPS по региону | RPS на 1 ДЦ |
|-------------------------|---------------------|------------|
| Европа                 | 24074 × 0.34 = 8185 | 8185/4 = 2046  |
| Северная Америка + Канада |24074 × 0.24 = 5778|5778/3 = 1926  |
| Латинская Америка      |24074 × 0.22 = 5296| 5296/2 = 2648  |
| Остальное             | 24074 × 0.20 = 4815 |4815/2} = 2408  |

---



### Схема DNS балансировки
Для данного сервиса подойдет Latency-based DNS балансировка, так как она обеспечивает низкую задержку, гибкость и быстрое распределение контента по миру.

## 5.Логическая схема базы данных 

![ER-диаграмма spotify](/img/er-spotify.png)

**Размер таблиц**  

**Таблица `User`**  
| Поле              | Тип данных       | Размер (байт) |
|------------------|-----------------|--------------|
| `user_id`        | `uuid`           | 16           |
| `username`       | `text`           | 50   |
| `login`          | `varchar(20)`    | 21           |
| `password`       | `varchar(25)`    | 26           |
| `avatar_url`     | `varchar(100)`   | 101          |
| `birth_date`     | `date`           | 4            |
| `country`        | `text`           | 20           |
| `subscription_id`| `uuid`           | 16           |
| `created_at`     | `timestamp`      | 8            |

Итого: 262 байта. 

---

**Таблица `Tracks`**  
| Поле          | Тип данных       | Размер (байт) |
|--------------|-----------------|--------------|
| `track_id`   | `uuid`           | 16           |
| `title`      | `varchar(20)`    | 21           |
| `avatar_url` | `varchar(100)`   | 101          |
| `duration`   | `int`            | 4            |
| `artist_id`  | `uuid`           | 16           |
| `album_id`   | `uuid`           | 16           |

Итого: 174 байта.  

---

**Таблица `Artist`**  
| Поле          | Тип данных       | Размер (байт) |
|--------------|-----------------|--------------|
| `artist_id`  | `uuid`           | 16           |
| `username`   | `varchar(15)`    | 16           |
| `avatar_url` | `varchar(100)`   | 101          |
| `genre`      | `text`           | 50           |

Итого: 183 байта. 

---

**Таблица `Album`**  
| Поле          | Тип данных       | Размер (байт) |
|--------------|-----------------|--------------|
| `album_id`   | `uuid`           | 16           |
| `title`      | `varchar(25)`    | 26           |
| `avatar_url` | `varchar(100)`   | 101          |
| `release_date` | `date`         | 4            |
| `duration`   | `int`            | 4            |
| `artist_id`  | `uuid`           | 16           |

Итого: 167 байт.

---

**Таблица `Playlist`**  
| Поле          | Тип данных       | Размер (байт) |
|--------------|-----------------|--------------|
| `playlist_id` | `uuid`          | 16           |
| `title`      | `varchar(50)`    | 51           |
| `duration`   | `int`            | 4            |
| `created_at` | `timestamp`      | 8            |
| `user_id`    | `uuid`           | 16           |

Итого: 95 байт. 

---

**Таблица `Playlist_tracks`**  
| Поле          | Тип данных       | Размер (байт) |
|--------------|-----------------|--------------|
| `track_id`   | `uuid`           | 16           |
| `user_id`    | `uuid`           | 16           |

Итого: 32 байта. 

---

**Таблица `Listening_history`**  
| Поле          | Тип данных       | Размер (байт) |
|--------------|-----------------|--------------|
| `track_id`   | `uuid`           | 16           |
| `user_id`    | `uuid`           | 16           |
| `duration`   | `int`            | 4            |

Итого: 36 байт

---

**Таблица `Session`**  
| Поле          | Тип данных       | Размер (байт) |
|--------------|-----------------|--------------|
| `session_id` | `uuid`           | 16           |
| `user_id`    | `uuid`           | 16           |
| `started_date` | `timestamp`    | 8            |
| `finished_date` | `timestamp`   | 8            |

Итого: 48 байт.

---

**Таблица `Subscription`**  
| Поле            | Тип данных       | Размер (байт) |
|----------------|-----------------|--------------|
| `subscription_id` | `uuid`        | 16           |
| `type`        | `text`           | 20 |
| `price`       | `integer`        | 4            |

Итого: 40 байт.

---

**Таблица `Subscription_User`**  
| Поле            | Тип данных       | Размер (байт) |
|----------------|-----------------|--------------|
| `user_id`      | `uuid`           | 16           |
| `subscription_id` | `uuid`        | 16           |
| `started`      | `timestamp`      | 8            |
| `finished`     | `timestamp`      | 8            |

Итого: 48 байт. 
 
| Таблица             | Размер записи, байт | Количество записей  | Итоговый размер | Чтение(QPS)   | Запись(QPS) |
|---------------------|--------------------|---------------------|----------------|----------------|----------------|
| `User`             | 262                | 392 млн            | 95.65 ГБ  | 4550 | 45.5 |
| `Tracks`           | 174                | 100 млн            | 16.21 ГБ  | 227 546 | 1.16 ****| 
| `Artist`           | 183                | 8 млн [6]              | 1.36 ГБ | 227 546 | - |
| `Album`            | 167                | 7 млн*              | 1.09 ГБ  | 227 546| - |
| `Playlist`         | 95                 | 4 млрд             | 353.90 ГБ |13 475 |46.3 |
| `Playlist_tracks`  | 32                 | 120млрд **         | 3.58 ТБ | 1 375 000 | 	1 389|
| `Listening_history`| 36                 | 589.5 млрд***         | 19.76 ТБ  | 227 546 | 227 546 |
| `Subscription_User`| 48                 | 246 млн            | 10.99 ГБ  |2 847 |28.5|

* - Обычно средний альбом содержит от 10 до 15 треков. Разделив 100 млн треков на среднее число треков в альбоме. `100 млн ÷ 13 ≈ 7 млн`.
** - Возьмем среднее количество треков в альбоме 30. Количество записей: `4 млрд × 30 = 120 млрд`.
*** - Каждый пользователь слушает 150 минут в день, средняя длина трека — 3 минуты. Кол-во прослушиваний в день: `150 ÷ 3 = 50 треков`. 
**** - Предположим 100к новых треков в день. `100K ÷ 86400`

Кол-во прослушиваний в месяц: `50 × 30 = 1500`. Количество записей: `393 млн × 1500 = 589.5 млрд`


##  Список источников  

- [Статистика Spotify](https://thesocialshepherd.com/blog/spotify-statistics)  
- [Трафик](https://www.similarweb.com/ru/website/spotify.com/)  
- [Статистика треков](https://habr.com/ru/articles/375365/)  
- [Посещения](https://hypestat.com/info/spotify.com)  
- [Размер треков](https://www.whistleout.com.au/MobilePhones/Guides/How-Much-Data-Does-Spotify-Use)  
- [Количество артистов](https://xmldatafeed.com/spotify-stats-2022-fakty-dannye-polzovateli-ispolzovanie-i-chasy-proslushivaniya/)
