# NovaIT Application Engineer Home Task — RedBot Telegram Bot

A small Telegram bot built with **Node-RED + RedBot (`node-red-contrib-chatbot`)**. The flow demonstrates visual routing, JavaScript validation/calculation, an external HTTP API call to the National Bank of Ukraine, friendly error handling, and diagnostic logging.

## How to run

1. Install Node.js and Node-RED, then start Node-RED and open `http://localhost:1880`.
2. In the Node-RED user directory (`~/.node-red` on macOS/Linux), install RedBot:
   ```bash
   npm install node-red-contrib-chatbot
   ```
   Restart Node-RED after installation.
3. In Node-RED choose **Menu → Import → Clipboard**, paste/import `flows.json`, then Deploy.
4. Create a Telegram bot with **@BotFather** and copy its token.
5. Open the RedBot **Telegram bot configuration** used by `Telegram Receiver` / `Telegram Sender`, paste the token there, save and Deploy. The token is intentionally **not** stored in this repository.
6. Open the bot in Telegram and send `/start`.

The default Node-RED port is **1880**. The Telegram bot uses polling, so no public webhook URL is required for this local demo.

## Implemented

- `/start` and `/menu` return to the main menu from any branch.
- Inline main menu with Calculator, Currency Rate and About.
- Back button in secondary screens and a fallback for unknown input.
- Calculator implemented in a JavaScript Function node. Input format: `12 + 7`; supported operators are `+`, `-`, `*`, `/`, `×`, `÷`.
- Validation for empty input, non-numeric/bad format, division by zero and input longer than 60 characters.
- NBU API call to `https://bank.gov.ua/NBUStatService/v1/statdirectory/exchange?json`; only USD and EUR are shown with the exchange date.
- Friendly handling for HTTP/transport failures and unexpected NBU responses.
- Structured diagnostic log lines containing ISO timestamp, chat id, step name and a short summary.

## Known issues / not done

- Telegram does not normally allow sending a truly empty text message, but an empty/undefined payload is still guarded in the calculator Function node.
- Conversation state is stored in Node-RED flow memory; it resets when the runtime restarts.
- Bonus NLP/free-text parsing is not included in the base version.
- Before submission, replace the placeholders in `logs.md` with real log lines and add the required screenshots/GIFs from your own Telegram bot.

## Time spent

2 h 25 min.

## Checklist


- ✅ RedBot deployed, bot responds to `/start`
- ✅ 3-item inline menu + Back
- ✅ Unknown-input fallback
- ✅ Calculator logic implemented
- ✅ Invalid/non-numeric input handled
- ✅ Division by zero handled
- ✅ Empty and too-long input handled
- ✅ NBU USD/EUR exchange-rate branch implemented
- ✅ API-unavailable handling implemented
- ✅ Successful calculation log captured in `logs.md`
- ✅ Invalid-input log captured in `logs.md`
- ✅ API-failure log captured in `logs.md`
- ✅ README in English

## Security

The Telegram token must never be committed. RedBot stores credentials separately from exported `flows.json`; `flows_cred.json`, `.env`, and local `.node-red/` data are ignored by `.gitignore`.
