# OpenWeatherMap-Dashboard-API-
Джерело даних – OpenWeatherMap API

Поточна погода: https://api.openweathermap.org/data/2.5/weather?q={city}&units=metric&appid={API_KEY}

Прогноз на 5 днів: https://api.openweathermap.org/data/2.5/forecast?q={city}&units=metric&appid={API_KEY}

Мета: створити інтерактивний дашборд у Looker Studio (з альтернативою в Excel), який відображає поточні та історичні дані погоди з відкритого джерела (OpenWeatherMap API).

Завдання: 
- Налаштувати підключення до OpenWeatherMap API та збір даних у табличний формат.

- Автоматизувати оновлення даних через Google Sheets.

- Розробити дашборд із ключовими показниками погоди, трендами та можливістю переглянути різні міста.

Формат даних: JSON

Ключові поля:

- temp (температура, °C)

- humidity (вологість, %)

- wind.speed (швидкість вітру, м/с)

- weather.description (умови: ясно, дощ, хмарно тощо)

- dt_txt (дата і час вимірювання)

Кроки реалізації проекту:
1. Збір даних

Google Sheets + Apps Script:

Скрипт витягує дані з API для вибраних міст.

Розклад виконання: кожна година (через тригер).

Дані зберігаються у таблиці у форматі:
Дата, Місто, Температура, Вологість, Вітер, Опис погоди

2. Дашборд у Looker Studio

MVP (мінімально життєздатний продукт) дашборду погоди

Колонки таблиці:
Date/Time, City, Temp (°C), Feels Like (°C), Humidity (%), Wind (m/s), Description

KPI для MVP дашборду:

- Поточна температура.

- Середня температура за день.

- Макс/мін температура за день.

3. Візуалізації

Карточки KPI

Лінійний графік: Температура по часу для обраного міста.

Стовпчиковий графік: Мін/макс температура по дням.

Фільтр міста: Можливість обрати Київ, Рівне, Львів.

Текстовий блок: Опис погоди (clear sky, light rain) останнього прогнозу по обраному місту у фільтрі.





Структура проекту:

▶ Інтерактивний дашборд у Looker Studio з відкритим доступом у режимі перегляду.

https://lookerstudio.google.com/reporting/3793ad7a-cd9b-49a8-b1b9-6a55d9c6aa0b

▶ Автоматично оновлюваний датасет (Google Sheets або Excel).

https://docs.google.com/spreadsheets/d/1D5hNngcTUC1QqKNOT3-WqlcmUwMF1_mpqlmA7omJSUo/edit?gid=28750195#gid=28750195

▶ Документація проєкту: опис кроків інтеграції API, приклад коду Apps Script.

https://script.google.com/u/0/home/projects/1gTpZJYlo-BCdbZTT0MI3lEHV3OuHZmZmTgkY4gYU_3q10VvzfI4QjIxt/edit

function getWeatherData() {
  // Налаштування
  const apiKey = 'ТВОЙ_API_KEY';  // <-- тут встав свій ключ
  const city = 'Kyiv';           // <-- місто, яке ти хочеш перевірити
  const units = 'metric';        // 'metric' для °C, 'imperial' для °F, або стандартний (Кельвіни)
  const lang = 'en';             // мова опису погоди, наприклад 'uk', 'en'

  // Формуємо URL запиту
  const url = `https://api.openweathermap.org/data/2.5/weather?q=${encodeURIComponent(city)}&appid=${apiKey}&units=${units}&lang=${lang}`;

  try {
    // Виконуємо HTTP GET
    const response = UrlFetchApp.fetch(url);
    const code = response.getResponseCode();
    if (code !== 200) {
      Logger.log(`Помилка: статус ${code}`);
      return;
    }

    // Розпарсимо JSON
    const data = JSON.parse(response.getContentText());

    // Отримаємо потрібні поля
    const temp = data.main.temp;  // температура
    const description = data.weather[0].description;  // опис
    const humidity = data.main.humidity;  // вологість
    const dt = new Date(data.dt * 1000);  // дата/час (перетворюємо UNIX timestamp)
    
    // Запис у Google Sheet
    const ss = SpreadsheetApp.getActiveSpreadsheet();
    const sheet = ss.getSheetByName('Weather');  // <-- ім'я листа, може бути інше
    // знайдемо перший порожній рядок
    const lastRow = sheet.getLastRow();
    const nextRow = lastRow + 1;
    sheet.getRange(nextRow, 1).setValue(city);
    sheet.getRange(nextRow, 2).setValue(temp);
    sheet.getRange(nextRow, 3).setValue(description);
    sheet.getRange(nextRow, 4).setValue(humidity);
    sheet.getRange(nextRow, 5).setValue(dt);

    Logger.log(`Дані записані: ${city}, ${temp}°C, ${description}, ${humidity}%, ${dt}`);
  } catch (err) {
    Logger.log('Сталася помилка: ' + err);
  }
}


▶ Скріншоти ключових сторінок дашборду для портфоліо.

<img width="786" height="491" alt="Знімок екрана 2025-09-22 о 23 01 43" src="https://github.com/user-attachments/assets/cef9f3cd-ea56-43b5-a373-b81e34142f26" />

