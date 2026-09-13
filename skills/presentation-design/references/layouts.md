# Макеты слайдов и тема

Справочник по `create_presentation` / `edit_presentation`. Это то, что инструмент реально принимает — не выдумывай других полей.

## Общая тема (`theme`)

Задаётся один раз на всю презентацию и держит единый стиль:

- `accentColor` — акцентный цвет (hex).
- `background` — фон слайдов.
- `textColor` — цвет основного текста.
- `mutedColor` — приглушённый цвет (подписи, второстепенное).
- `headingFont` — шрифт заголовков.
- `bodyFont` — шрифт тела.

Держи 2–3 цвета. Один акцент, нейтральный фон, читаемый текст.

## Макеты слайда (`layout`)

Выбирай по смыслу слайда:

- `title` — титульный: `title`, `subtitle`.
- `section` — разделитель темы: крупный `title`.
- `bullets` — тезисы: `title` + `bullets` (держи список коротким).
- `two_column` — две колонки: `left` и `right` (`{ title, body, bullets }`).
- `comparison` — сравнение двух вариантов: `left` / `right`.
- `callout` — акцентное утверждение: `title` + `body`.
- `process` — шаги: `steps` (`[{ title, body }]`).
- `image` — крупное изображение: `image { path, caption }`.
- `chart` — график: `chart { type, categories, series }`, где `type` — `bar`, `bar_horizontal`, `line`, `pie`, `doughnut`, `area`.
- `blank` — пустой холст.

Общие поля слайда: `title`, `subtitle`, `body`, `bullets`, `notes` (заметки докладчика — выноси туда детали, чтобы не грузить слайд).

## Изображения

Поле `image.path` — путь к файлу в рабочей папке. Сгенерируй иллюстрацию через `generate_image` (в облачном компьютере она сохранится в `/workspace/artifacts/images`) и укажи её путь. `caption` — короткая подпись.

## Графики

Для `chart`-слайда данные передаются структурно (`categories` + `series`), инструмент рисует график сам — не вставляй график картинкой, если данные табличные.

## Правки (`edit_presentation`)

`operations` с `kind`: `replace_text`, `append_slides`, `delete_slides`, `reorder_slides`, `set_notes`. Целься операциями точечно (`index`, `indexes`, `find`), а не пересобирай деку целиком.
