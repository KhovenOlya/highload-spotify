# **Содержание**  
1. [Тема и целевая аудитория](#тема-и-целевая-аудитория)  
2. [Расчётная нагрузка](#расчётная-нагрузка)  
3. [Список источников](#список-источников)  

---

## 1. Тема и целевая аудитория  
**Spotify** — это сервис для стриминга различного контента (треков, подкастов и видеороликов) от авторов со всего мира.  

---

## 2. Функционал MVP  
1. Регистрация и авторизация пользователей  
2. Прослушивание треков/аудиокниг/подкастов  
3. Создание плейлиста  
4. Добавление в избранное  
5. Поиск трека/аудиокниги/подкаста  
6. Прослушивание чартов  
7. История прослушиваний пользователей  

---

## 3. Анализ трафика и вовлечённости  

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

## 4. Расчётная нагрузка  

| Метрика                               | Значение |
|---------------------------------------|----------|
| MAU (ежемесячные активные пользователи) | 602 млн  |
| DAU (ежедневные активные пользователи) | 665 603  |
| Количество треков                     | 100 млн  |
| Количество плейлистов                  | 4 млрд   |
| Среднее количество прослушиваний треков в день | 150 мин |
| Среднее количество поисков за день     | 62.8 млн |

---

### Средний размер хранилища  

| Данные               | Размер единицы | Количество на пользователя | Размер на пользователя |
|----------------------|--------------|--------------------------|------------------------|
| Треки               | ~3 МБ        | 1000                     | 3 ГБ                   |
| Плейлисты           | ~51.5 КБ *   | 1                        | 67.5 КБ **             |
| Данные пользователя | ~100 КБ ***  | 1                        | 100 КБ                 |

#### Предположения  

\* Метаданные (название, описание, автор) ~500 Байт, обложка ~50 КБ (JPEG/PNG). Итого **51.5 КБ**  

\** 57.5 КБ + ID-трека (предположим, что ID-трека = 16 Б) × N  

\*** Данные пользователя включают:  
- Имя, аватар, email ~10 КБ  
- Лайки (избранные треки, альбомы, артисты) ~50 КБ  
- История прослушиваний (ID треков, временные метки) ~30 КБ  
- Настройки аккаунта (язык, плейлисты в оффлайн-режиме) ~10 КБ  
Итого **100 КБ**  

---

### Размер хранения по типам данных  

Треки могут загружаться в разном качестве, от чего зависит их размер. Предположим, что средний размер трека ~3 МБ.  

| Тип данных   | Размер                              |
|-------------|-------------------------------------|
| Треки       | 100 млн × 3 МБ = **300 ТБ**        |
| Плейлисты   | 4 млрд × 51.5 КБ = **206 ТБ**      |
| Пользователи | 602 млн × 100 КБ = **60.2 ТБ**    |

---

### Сетевой трафик  

У Spotify есть разные битрейты для потокового воспроизведения:  
- **Низкое качество** ~2.16 МБ  
- **Среднее качество** ~3.6 МБ  
- **Высокое качество** ~7.2 МБ  

| Действие пользователя | Суммарный суточный трафик | Среднее потребление | Пиковое потребление | RPS (запросов в секунду) |
|-----------------------|--------------------------|----------------------|----------------------|------------------|
| Прослушивание треков  | `119.7 ПБ`               | `4.6 Тбит/с`         | `9.2 Тбит/с`         | `726K`          |
| Поиск треков         | `15.7 ТБ`                | `1.45 Гбит/с`        | `2.9 Гбит/с`         | `728`           |
| Добавление в избранное | `2.1 ТБ`                | `194 Мбит/с`         | `388 Мбит/с`         | `97`            |
| Создание плейлистов  | `5.7 ТБ`                 | `526 Мбит/с`         | `1.05 Гбит/с`        | `263`           |
| История прослушиваний | `9.1 ТБ`                 | `839 Мбит/с`         | `1.68 Гбит/с`        | `420`           |

### Как рассчитано:
1. **Прослушивание треков**:  
   - `602 млн пользователей × 150 минут / день × 3 МБ / минута = 119.7 ПБ / день`  
   - `119.7 ПБ × 8 бит = 957.6 Пбит`  
   - `957.6 Пбит / (24 × 3600) ≈ 4.6 Тбит/с`  
   - Пик: удвоение нагрузки в часы пик (`4.6 × 2 = 9.2 Тбит/с`)  
   - `RPS = 602M × 150 × 60 / 86400 ≈ 726K`  

2. **Поиск треков**:  
   - `62.8 млн поисков × 250 КБ (ответ) = 15.7 ТБ`  
   - `15.7 ТБ × 8 / (24 × 3600) ≈ 1.45 Гбит/с`  
   - Пик: `1.45 × 2 = 2.9 Гбит/с`  
   - `RPS = 62.8M / 86400 ≈ 728`  

3. **Добавление в избранное**:  
   - `602M × 2 добавления × 1.8 КБ = 2.1 ТБ`  
   - `2.1 ТБ × 8 / (24 × 3600) ≈ 194 Мбит/с`  
   - Пик: `194 × 2 = 388 Мбит/с`  
   - `RPS = 602M × 2 / 86400 ≈ 97`  

4. **Создание плейлистов**:  
   - `602M × 1 плейлист × 10 КБ = 5.7 ТБ`  
   - `5.7 ТБ × 8 / (24 × 3600) ≈ 526 Мбит/с`  
   - Пик: `526 × 2 = 1.05 Гбит/с`  
   - `RPS = 602M × 1 / 86400 ≈ 263`  

5. **История прослушиваний**:  
   - `602M × 20 записей × 8 КБ = 9.1 ТБ`  
   - `9.1 ТБ × 8 / (24 × 3600) ≈ 839 Мбит/с`  
   - Пик: `839 × 2 = 1.68 Гбит/с`  
   - `RPS = 602M × 20 / 86400 ≈ 420`  

---

## 5. Список источников  

- [Статистика Spotify](https://thesocialshepherd.com/blog/spotify-statistics)  
- [Трафик](https://www.similarweb.com/ru/website/spotify.com/)  
- [Статистика треков](https://habr.com/ru/articles/375365/)  
- [Посещения](https://hypestat.com/info/spotify.com)  
- [Размер треков](https://www.whistleout.com.au/MobilePhones/Guides/How-Much-Data-Does-Spotify-Use)  
