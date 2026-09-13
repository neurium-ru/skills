# Рецепты по docx и xlsx

Конкретные приёмы под инструменты документов. Сверяйся с фактическими схемами инструментов — здесь только то, что они реально умеют.

## docx — `create_document` и `edit_document`

Оба работают через Markdown-проекцию документа.

### Структура из Markdown

`create_document` принимает `markdown` и опциональный `style`. Markdown отображается в docx предсказуемо:

- `#`, `##`, `###` → заголовки уровней 1–3.
- Абзацы — обычный текст, пустая строка разделяет.
- `- ` и `1. ` → маркированные и нумерованные списки.
- `**жирный**`, `*курсив*` → инлайновое форматирование.
- Таблицы в Markdown-синтаксисе (`| a | b |`) → таблицы docx.

### Типографика через `style`

Поля `style` (используй умеренно, единообразно на весь документ):

- `fontFamily`, `headingFontFamily` — шрифт тела и заголовков.
- `fontSizePt` (8–18) — базовый кегль.
- `marginInches` (0.4–2) — поля.
- `pageSize` — `A4` или `Letter`.
- `title` — заголовок документа.

### Точечные правки `edit_document` (mode: patch)

`operations` — массив; каждая операция `kind`:

- `replace_text` — заменить `oldText` на `newText` (опц. `replaceAll`).
- `insert_after_text` — вставить `text` после найденного `oldText`.
- `append_markdown` — дописать `markdown` в конец.

Для крупной переработки — `mode: rewrite` с новым `markdown`. Предпочитай `patch`, когда меняешь часть.

## xlsx — `create_workbook` и `edit_workbook`

### Создание книги

`sheets` — массив листов. Лист задаётся:

- `name` (≤31 символ).
- `rows` — массив строк (простой способ залить данные).
- `cells` — точечные ячейки со стилем и формулой: `{ address: "B2", value, formula, numberFormat, style }`.
- `columns` — заголовки колонок.
- `columnWidths` — `{ "A": 20, "B": 12 }`.
- `freezePane` — например `"A2"` (зафиксировать шапку).
- `autoFilter` — `true` или диапазон строкой.

### Формулы

`formula` в ячейке — обычная Excel-формула, с `=` или без: `"SUM(B2:B10)"`, `"=AVERAGE(C:C)"`. Ссылайся на реальные адреса.

### Стиль ячейки (`style`)

- `bold`, `italic`.
- `align` — `left` / `center` / `right`.
- `color` — hex цвета текста, `background` — hex заливки.
- `fontSize`.

### Числовые форматы (`numberFormat`)

Строка формата Excel:

- Деньги: `"#,##0.00"` или `"#,##0.00 ₽"`.
- Проценты: `"0.0%"`.
- Даты: `"dd.mm.yyyy"`.
- Разделитель разрядов: `"#,##0"`.

Всегда задавай формат числовым и денежным колонкам — иначе числа выглядят сыро.

### Правки `edit_workbook`

`operations` с `kind`: `set_cell`, `set_range`, `append_rows`, `add_sheet`, `delete_sheet`, `rename_sheet`, `replace_text`, `set_column_widths`. Импорт csv/tsv в xlsx — тоже через этот инструмент.

## Проверка результата

После любой правки — `read_document` с `renderPages` и просмотр через `view_media`. Для xlsx особенно проверяй, что колонки достаточно широки (числа не превратились в `#####`) и формулы посчитались.
