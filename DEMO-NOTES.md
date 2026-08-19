# Demo / interview notes

Use this as a short explanation while sharing your screen.

## Architecture in ~60 seconds

`Telegram Receiver` accepts inbound Telegram events. `Router + session state` reads `msg.payload.content` and stores only the active calculator state per `chatId` in Node-RED flow context. Menu branches go to RedBot Buttons/Message nodes; calculator input goes to a JavaScript Function node; Currency prepares a standard Node-RED HTTP Request to the NBU endpoint and then parses only USD/EUR. All important operations write structured `node.warn` logs.

## Why this structure

- The flow remains visual and easy to follow, which fits the RedBot/Node-RED task.
- JavaScript is used only where it adds value: routing/session state, input validation/calculation and API response parsing.
- User-facing errors are separated from technical diagnostic logs.
- `/start` and `/menu` are handled before branch state, so they always recover the bot.

## Live change you can safely demonstrate

Open `Calculator (JS)` and change:

```js
const MAX_LENGTH = 60;
```

to `50`, Deploy, explain that this is one centralized validation rule, then restore `60`.

Or open `Prepare NBU request`, briefly show the `FORCE_NBU_FAILURE` switch used to reproduce the required failure scenario, but leave it `false` in the final version.

## Key messages to remember

- RedBot messages use `msg.payload`; the flow preserves `chatId` so responses go back to the correct Telegram chat.
- Calculator parsing uses an anchored regular expression, so text such as `5 і слон` cannot be partially accepted as a number.
- Division-by-zero is validated before evaluation.
- NBU failures do not expose raw errors to the end user; logs keep technical details.
- The Telegram token is a credential and is not part of exported `flows.json` / Git.
