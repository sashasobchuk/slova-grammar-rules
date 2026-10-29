# slova-grammar-rules

Дефолтні каталоги правил граматики для застосунку Slova. Один файл на мову,
назва файла — код мови (`en.json`, `de.json`, `es.json`, `ja.json`).

Код — тільки базовий, без суфіксів письма чи регіону: застосунок обрізає код
мови по дефісу, тож `zh-Hans` і `zh-Hant` обидва просять `zh.json`.

Застосунок читає файли напряму з цього репозиторія:

```
https://raw.githubusercontent.com/sashasobchuk/slova-grammar-rules/main/<lang>.json
```

Якщо файла для мови немає (404), застосунок просто не заливає дефолтних правил
для неї — це не помилка.

## Формат

```jsonc
{
  "lang": "en",      // код мови, має збігатися з назвою файла
  "version": 1,      // ціле число; підніміть, коли додали або змінили правила
  "groups": [{ "id": "en.group.level", "name": "level", "description": "…" }],
  "tags":   [{ "id": "en.tag.a1", "name": "a1", "description": "…", "groupIds": ["en.group.level"] }],
  "rules":  [{
    "id": "en.rule.present_perfect_since_for",
    "title": "…",
    "description": "…",      // для людини, Markdown
    "llmDescription": "…",   // 1–2 речення для LLM, без Markdown
    "examples": [{ "wrong": "…", "correct": "…", "note": "…" }],
    "tagIds": ["en.tag.a1"]
  }]
}
```

### Правила щодо id

- id — стабільний slug з префіксом мови (`en.rule.…`, `de.tag.…`).
- id **ніколи не змінюються**: до них привʼязана статистика юзера
  (`grammar_interactions`) і звʼязки правил з тегами.
- Перейменувати правило — міняйте `title`, не `id`.

### version

Застосунок запамʼятовує, яку `version` мови вже залив. Коли в репозиторії
`version` більша — досіває нові id, не перезаписуючи правок юзера. Тому:
підняли `version` → нові правила доїдуть на пристрої; забули підняти → ні.

## Як наповнювати новою мовою

Промпт для генерації повного каталогу однією відповіддю LLM —
[GENERATE_DEFAULTS_PROMPT.md](GENERATE_DEFAULTS_PROMPT.md).
