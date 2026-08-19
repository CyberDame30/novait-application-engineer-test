# Diagnostic logs

> Replace every placeholder below with **real lines copied from the Node-RED terminal / Debug sidebar** after you reproduce the scenarios. Do not submit invented logs.

## 1. Successful calculation: `12 + 7`


19 Aug 22:55:40 - [warn] [function:Calculator (JS)] 2026-08-19T19:55:40.510Z | chat=688306917 | calc:input_received | value="12 + 7"
19 Aug 22:55:40 - [warn] [function:Calculator (JS)] 2026-08-19T19:55:40.510Z | chat=688306917 | calc:success | expression=12+7 result=19


What happened: The user selected Calculator and sent `12 + 7`. The `calc:success` line confirms that the expression was parsed and produced `19`; the bot showed `✅ 12 + 7 = 19` and then returned to the main menu.

## 2. Invalid input: `abc`

19 Aug 22:58:45 - [warn] [function:Calculator (JS)] 2026-08-19T19:58:45.427Z | chat=688306917 | calc:input_received | value="abc"
19 Aug 22:58:45 - [warn] [function:Calculator (JS)] 2026-08-19T19:58:45.427Z | chat=688306917 | calc:validation_error | reason=not_numeric_or_bad_format value="abc"

What happened: The user sent `abc` while the calculator branch was active. The validation log contains `reason=not_numeric_or_bad_format`; the bot displayed a readable validation error and kept the calculator available for another attempt or Back.

## 3. Broken external service

19 Aug 23:00:22 - [warn] [function:Prepare NBU request] 2026-08-19T20:00:22.615Z | chat=688306917 | nbu:request_sent | url=https://bank.gov.ua/NBUStatService/v1/THIS_ENDPOINT_IS_BROKEN?json
19 Aug 23:00:22 - [warn] [function:Parse USD + EUR] 2026-08-19T20:00:22.826Z | chat=688306917 | nbu:error | http_status=404 elapsed_ms=211

What happened: The flow intentionally called an invalid NBU endpoint to reproduce a service failure. The `nbu:error` line records the HTTP/transport failure; the user saw a friendly “service temporarily unavailable” message and was returned to the menu.
