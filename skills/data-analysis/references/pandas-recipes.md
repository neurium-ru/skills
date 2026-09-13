# Рецепты pandas: профилирование и чистка

Запускай через `execute_command`. Это ориентир, а не догма — адаптируй под конкретный датасет. Пути в примерах — облачные (`/workspace/...`); на машине пользователя подставляй реальные пути его файлов.

## Загрузка

```python
import pandas as pd

# CSV: не угадывай разделитель молча — проверь первые строки файла
df = pd.read_csv("/workspace/attachments/data.csv")
# xlsx
df = pd.read_excel("/workspace/attachments/data.xlsx", sheet_name=0)
# JSON (записи)
df = pd.read_json("/workspace/attachments/data.json")
```

Если чтение падает на кодировке или разделителе — сначала посмотри сырые строки через `read_file`, потом задай `sep`, `encoding`, `decimal` явно.

## Профилирование (делать до анализа)

```python
print(df.shape)                       # строки, колонки
print(df.dtypes)                      # типы
print(df.isna().sum())                # пропуски по колонкам
print((df.isna().mean() * 100).round(1))  # доля пропусков, %
print(df.duplicated().sum())          # дубликаты строк
print(df.describe(include="all"))     # сводка по числам и категориям

# Категориальные: уникальные и топ значений
for col in df.select_dtypes(include="object").columns:
    print(col, df[col].nunique(), df[col].value_counts().head(5).to_dict())
```

### Выбросы (IQR)

```python
def outliers_iqr(s):
    q1, q3 = s.quantile(0.25), s.quantile(0.75)
    iqr = q3 - q1
    lo, hi = q1 - 1.5 * iqr, q3 + 1.5 * iqr
    return s[(s < lo) | (s > hi)]

for col in df.select_dtypes(include="number").columns:
    n = len(outliers_iqr(df[col].dropna()))
    if n:
        print(f"{col}: {n} потенциальных выбросов")
```

## Чистка (каждый шаг — озвучить пользователю)

```python
before = len(df)

# Типы: даты и числа
df["date"] = pd.to_datetime(df["date"], errors="coerce")
df["amount"] = pd.to_numeric(df["amount"], errors="coerce")

# Дубликаты
dups = df.duplicated().sum()
df = df.drop_duplicates()

# Пропуски — решай осознанно: удалить, заполнить или оставить
# df = df.dropna(subset=["amount"])      # удалить, если ключевой признак пуст
# df["region"] = df["region"].fillna("не указан")

print(f"Было {before}, стало {len(df)}; удалено дубликатов: {dups}")
```

Всегда сообщай пользователю: сколько строк удалено/изменено и почему.

## Графики (сохранять и проверять через view_media)

```python
import matplotlib
matplotlib.use("Agg")
import matplotlib.pyplot as plt

fig, ax = plt.subplots(figsize=(10, 6))
df.groupby("region")["amount"].sum().sort_values().plot.barh(ax=ax)
ax.set_xlabel("Сумма")
ax.set_ylabel("Регион")
ax.set_title("Сумма по регионам")
fig.tight_layout()   # чтобы метки не обрезались
fig.savefig("/workspace/artifacts/images/amount-by-region.png", dpi=150)
```

После сохранения — `view_media` по пути png: проверь подписи осей, легенду, что метки не обрезаны и тип графика уместен.
