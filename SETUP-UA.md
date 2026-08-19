# Покроковий запуск у VS Code

## 1. Створити папку для здачі

У VS Code відкрий Terminal і виконай:

```bash
mkdir novait-redbot-home-task
cd novait-redbot-home-task
```

Скопіюй у цю папку файли з цього starter-пакета (`flows.json`, `README.md`, `logs.md`, `.gitignore`, `REPOSITORY_LINK.txt`).

## 2. Перевірити Node.js

```bash
node -v
npm -v
```

Якщо обидві команди повертають версії — переходь далі.

## 3. Встановити Node-RED

```bash
npm install -g node-red
```

На macOS/Linux, якщо npm повідомляє про permissions, краще використовувати Node через nvm, а не додавати `sudo` без потреби.

Запусти:

```bash
node-red
```

У терміналі має з’явитися адреса редактора. Відкрий:

```text
http://localhost:1880
```

Не закривай термінал, поки тестуєш бота.

## 4. Встановити RedBot

Зупини Node-RED у терміналі через `Ctrl+C`, потім:

```bash
cd ~/.node-red
npm install node-red-contrib-chatbot
node-red
```

Після рестарту в лівій палітрі Node-RED мають з’явитися RedBot/chatbot nodes, зокрема Telegram Receiver/Sender і Buttons.

Альтернатива через UI: Menu → Manage palette → Install → знайти `node-red-contrib-chatbot` → Install → перезапустити Node-RED.

## 5. Імпортувати готовий flow

1. В Node-RED натисни меню ☰ справа вгорі.
2. `Import` → `select a file to import` і вибери `flows.json` (або Clipboard і встав JSON).
3. Імпортуй у `new flow`.
4. Натисни **Deploy**.

Якщо Telegram nodes показують червоний трикутник — це нормально до моменту, поки не додано токен.

## 6. Створити Telegram-бота

1. В Telegram відкрий **@BotFather**.
2. Надішли `/newbot`.
3. Вкажи display name, наприклад `NovaIT Test Bot`.
4. Вкажи username, який закінчується на `bot`, наприклад `elisabeth_novait_test_bot` (ім’я має бути унікальне).
5. BotFather надішле token. **Нікуди його не коміть і не вставляй у README/flows.json.**

## 7. Додати token у RedBot

1. У Node-RED двічі клікни `Telegram Receiver`.
2. Біля поля Bot відкрий/відредагуй configuration node (іконка олівця).
3. Встав token від BotFather у поле Token.
4. Save/Add → Done.
5. Переконайся, що `Telegram Sender` використовує той самий Bot configuration.
6. Натисни **Deploy**.

## 8. Перша перевірка

Відкрий свого бота в Telegram і надішли:

```text
/start
```

Очікування: з’явиться меню:

- 🧮 Калькулятор
- 💱 Курс валют
- ℹ️ Про бота

Якщо відповіді немає: перевір terminal Node-RED, token, що Node-RED продовжує працювати, і що flow задеплоєний.

## 9. Перевірити калькулятор

Натисни `🧮 Калькулятор` і послідовно протестуй:

```text
12 + 7
```

Очікування: `✅ 12 + 7 = 19` і повернення меню.

Повтори Calculator →

```text
abc
```

Очікування: читабельна помилка формату, бот лишається придатним для повторної спроби, є `⬅️ Назад`.

Також перевір:

```text
5 / 0
5 і слон
1234567890123456789012345678901234567890123456789012345678901 + 1
```

Для довгого рядка очікується помилка `Максимум — 60`.

## 10. Перевірити `/start`, `/menu`, Back і fallback

- зайди у Calculator та надішли `/start` → повинно відкритися головне меню;
- зайди у About → натисни `⬅️ Назад`;
- на головному екрані надішли `hello123` → бот має пояснити, що команда невідома, і показати меню;
- надішли `/menu` у будь-який момент → головне меню.

## 11. Перевірити NBU API

Натисни `💱 Курс валют`.

Очікування приблизно такого формату (цифри залежать від дати):

```text
💱 Курс НБУ
USD: 1 USD = ... UAH
EUR: 1 EUR = ... UAH
Дата: ...
```

Flow не показує сирий JSON — `Parse USD + EUR` знаходить тільки об’єкти `cc === 'USD'` та `cc === 'EUR'`.

## 12. Зібрати 3 обов’язкові логи

Логи генеруються через `node.warn(...)`, тому їх видно в Node-RED / Debug і в терміналі, де працює `node-red`.

### Сценарій A — успішний `12 + 7`

1. Calculator → `12 + 7`.
2. Знайди рядки `calc:input_received` та `calc:success`.
3. Скопіюй їх у розділ 1 `logs.md`.

### Сценарій B — `abc`

1. Calculator → `abc`.
2. Знайди `calc:input_received` і `calc:validation_error`.
3. Скопіюй у розділ 2 `logs.md`.

### Сценарій C — поламаний API

1. Двічі клікни Function `Prepare NBU request`.
2. Зміни тільки:

```js
const FORCE_NBU_FAILURE = false;
```

на:

```js
const FORCE_NBU_FAILURE = true;
```

3. Deploy.
4. Натисни Currency.
5. Скопіюй `nbu:request_sent` і `nbu:error` у `logs.md`.
6. **Обов’язково поверни `false` і знову Deploy.**

Кожен лог уже містить 4 потрібні частини: timestamp, `chat=...`, step name, summary.

## 13. Зробити скріншоти/GIF

Створи папку `screenshots/` і поклади щонайменше:

```text
screenshots/01-calculator-success.png
screenshots/02-calculator-invalid.png
screenshots/03-currency.png
```

На скріні має бути видно діалог із ботом, але **не token BotFather**.

## 14. Експортувати остаточний `flows.json`

Після всіх правок:

1. Node-RED menu → `Export`.
2. Обери `Current flow` або всі потрібні nodes.
3. Export/Download JSON.
4. Замінити repo-файл `flows.json` фінальним експортом.
5. Перевір через пошук у VS Code, що token ніде не потрапив.

Пошук секрету: в VS Code `Cmd/Ctrl+Shift+F` і встав перші 8–10 символів token. Результатів у repo бути не повинно.

## 15. Заповнити README перед здачею

У `README.md`:

- заміни `REPLACE WITH YOUR REAL TIME` на реальний час;
- після фактичної перевірки зміни відповідні `⚠️` на `✅`;
- якщо щось не зроблено, чесно залиш `⚠️`/`❌` і коротко поясни.

## 16. Створити private Git repo

На GitHub створи **Private** repository, наприклад `novait-application-engineer-test`.

У VS Code Terminal у папці здачі:

```bash
git init
git add .
git commit -m "Complete NovaIT RedBot home task"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/novait-application-engineer-test.git
git push -u origin main
```

Встав реальний URL private repo у `REPOSITORY_LINK.txt`.

## 17. Перед відправкою — фінальна структура

```text
novait-redbot-home-task/
├── flows.json
├── README.md
├── logs.md
├── REPOSITORY_LINK.txt
├── .gitignore
└── screenshots/
    ├── 01-calculator-success.png
    ├── 02-calculator-invalid.png
    └── 03-currency.png
```

Архівуй цю папку й додай посилання на private Git repo так, як вимагає тестове.
