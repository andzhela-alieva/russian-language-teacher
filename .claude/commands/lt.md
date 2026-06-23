# /lt — Проверка текста через LanguageTool

Проверяет переданный текст на орфографические, грамматические и пунктуационные ошибки через бесплатный публичный API LanguageTool.

## Как использовать

```
/lt <текст для проверки>
```

## Что делать

1. Возьми текст из `$ARGUMENTS`. Если пусто — попроси пользователя передать текст.

2. Вызови API через PowerShell:

```powershell
$text = "<ТЕКСТ>"
$encoded = [System.Uri]::EscapeDataString($text)
$body = "text=$encoded&language=ru-RU"

$wc = New-Object System.Net.WebClient
$wc.Encoding = [System.Text.Encoding]::UTF8
$wc.Headers.Add("Content-Type", "application/x-www-form-urlencoded")
$jsonStr = $wc.UploadString("https://api.languagetool.org/v2/check", $body)

$json = $jsonStr | ConvertFrom-Json
```

3. Выведи результаты:
   - Если `$json.matches.Count -eq 0` — напиши "Ошибок не найдено ✓"
   - Иначе — для каждого матча выведи:
     - контекст с выделением проблемного места
     - описание ошибки (`message`)
     - предложения по исправлению (`replacements`)
     - категорию (`rule.category.name`)
   - Сгруппируй по категориям: орфография / грамматика / пунктуация / стиль

4. В конце — краткое резюме: сколько ошибок, готов ли текст к публикации.

## Особенности

- Язык всегда `ru-RU`
- Лимит ~20 000 символов на запрос (бесплатный API)
- API публичный, ключ не нужен
- Использовать `System.Net.WebClient` с `Encoding = UTF8` — иначе русский текст в ответе будет искажён
