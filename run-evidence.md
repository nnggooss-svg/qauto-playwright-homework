# Run evidence

- Tool: Codex, terminal execution
- Command: `npx playwright test tests/add-car.spec.ts --project=chromium`
- Timestamp: `2026-11-19T22:52:06+02:00` — начало серии запусков
- Environment: Windows, Playwright Chromium, https://qauto.forstudy.space
- Actual:
  - Запуск 1, в sandbox: основная попытка (748ms) и автоматический retry #1 (444ms) завершились ошибкой `page.goto: net::ERR_NETWORK_ACCESS_DENIED`. Итог: `1 failed`, код завершения 1.
  - Запуск 2, та же команда вне sandbox: одна успешная попытка (4.1s), без retry. Итог: `1 passed (5.2s)`, код завершения 0.
  - Всего: два запуска команды, три попытки теста. Между запусками тест не изменялся.

| Елемент | Джерело |
|---|---|
| setup | playwright.config.ts; page.goto('/'); гостевой вход |
| locator/action | Предыдущая CLI-инспекция: accessibility snapshots и выполненные действия Guest log in → Add car → Audi → TT → Mileage 12000 → Add |
| assertions | specs/add-car.md и описание ДЗ: URL Garage и видимый Audi TT; обе проверки прошли в Playwright Test |

- FACTS: Последний запуск успешен. Тест и config не изменялись при запуске. HTML report последнего запуска доступен в playwright-report/index.html. Первый запуск создал trace обеих неудачных попыток, но после повторного запуска эти файлы отсутствовали при проверке. Успешный запуск не сохранил trace при настройке retain-on-failure.
- ASSUMPTIONS: Нет дополнительных предположений о требованиях.
- RISKS: Первый запуск ограничен сетью sandbox; его trace после повторного запуска недоступны. Ошибка произошла на page.goto до действий сценария; дефект продукта не установлен.
- HUMAN CORRECTION: Источники требований ограничены specs/add-car.md и описанием ДЗ; отсутствующий cases/qauto/requirements/add-car.md не восстанавливался.
- DECISION: ACCEPT — результат принят пользователем.

## Запуск 1 — сокращённый terminal output

Повторяющиеся фрагменты стека и инструкции открытия trace опущены. Код завершения: 1.

```text
Running 1 test using 1 worker

(node:22788) Warning: The 'NO_COLOR' env is ignored due to the 'FORCE_COLOR' env being set.
(Use `node --trace-warnings ...` to show where the warning was created)
  x  1 [chromium] › tests\add-car.spec.ts:3:5 › guest can add Audi TT to Garage (748ms)
(node:23496) Warning: The 'NO_COLOR' env is ignored due to the 'FORCE_COLOR' env being set.
(Use `node --trace-warnings ...` to show where the warning was created)
  x  2 [chromium] › tests\add-car.spec.ts:3:5 › guest can add Audi TT to Garage (retry #1) (444ms)

    Error: page.goto: net::ERR_NETWORK_ACCESS_DENIED at https://qauto.forstudy.space/
    Call log:
      - navigating to "https://qauto.forstudy.space/", waiting until "load"

        at E:\Курсы AI agent\Playwrite\playwright-starter\tests\add-car.spec.ts:4:14

  1 failed
    [chromium] › tests\add-car.spec.ts:3:5 › guest can add Audi TT to Garage
```

## Запуск 2 — полный terminal output

Код завершения: 0.

```text

Running 1 test using 1 worker

(node:12460) Warning: The 'NO_COLOR' env is ignored due to the 'FORCE_COLOR' env being set.
(Use `node --trace-warnings ...` to show where the warning was created)
  ok 1 [chromium] › tests\add-car.spec.ts:3:5 › guest can add Audi TT to Garage (4.1s)

  1 passed (5.2s)
```
