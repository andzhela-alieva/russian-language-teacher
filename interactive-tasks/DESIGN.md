# Дизайн-правила интерактивных заданий

Все файлы в `interactive-tasks/` реализуют единый стиль «школьная тетрадь в линейку». Этот документ — эталон: при создании нового задания и при правке существующих нужно точно следовать этим правилам.

---

## 1. CSS-переменные (обязательные, менять нельзя)

```css
:root {
  --paper:    #f3f1e8;   /* фон страницы */
  --rule:     #c9d6e3;   /* линейка / границы */
  --margin:   #e2a3a0;   /* красная поля слева */
  --ink:      #233047;   /* основной текст */
  --ink-soft: #4b5a73;   /* вторичный/приглушённый текст */
  --red:      #b5392f;   /* ошибки, акценты */
  --red-dark: #8c2a22;   /* hover кнопки, тёмный красный */
  --green:    #3f7a4f;   /* правильный ответ */
  --pencil:   #7c7567;   /* подписи, footer */
  --gold:     #b9912f;   /* частично / пропущено */
  --blue:     #2c5f8a;   /* информационные выделения */
}
```

> Все цвета — только через переменные, никаких хардкодных hex-значений в правилах.

---

## 2. Шрифты

```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Caveat:wght@600;700&family=PT+Serif:ital,wght@0,400;0,700;1,400&family=Courier+Prime:wght@400;700&display=swap" rel="stylesheet">
```

| Роль | Шрифт |
|---|---|
| Основной текст | `'PT Serif', serif` |
| Заголовок h1, кнопки `.btn`, метки ошибок/верно | `'Caveat', cursive` |
| Номера вопросов, лейблы, eyebrow, footer, моноширинные акценты | `'Courier Prime', monospace` |

---

## 3. Layout

```css
body {
  margin: 0;
  background: repeating-linear-gradient(var(--paper) 0px, var(--paper) 33px, var(--rule) 34px);
  background-color: var(--paper);
  font-family: 'PT Serif', serif;
  color: var(--ink);
}

.page {
  max-width: 760px;
  margin: 0 auto;
  position: relative;
  padding: 28px 28px 50px 64px;
  min-height: 100vh;
}

.margin-line {
  position: absolute;
  left: 42px;
  top: 0;
  bottom: 0;
  width: 2px;
  background: var(--margin);
}
```

- `max-width`: **760px** (не 720px)
- Левый padding 64px — оставляет место для `.margin-line` на `left: 42px`
- Нижний padding `50px` — отступ перед footer

---

## 4. Шапка (header)

```css
header { margin-bottom: 18px; }

.eyebrow {
  font-family: 'Courier Prime', monospace;
  font-size: 12px;
  letter-spacing: .12em;
  text-transform: uppercase;
  color: var(--pencil);
}

h1 {
  font-family: 'Caveat', cursive;
  font-weight: 700;
  font-size: 40px;
  line-height: 1.05;
  margin: 4px 0 6px;
}

.subtitle {
  color: var(--ink-soft);
  font-style: italic;
  font-size: 15px;
  margin: 0;
}
```

HTML-структура:
```html
<header>
  <div class="eyebrow">Краткое описание задания</div>
  <h1>Заголовок теста</h1>
  <p class="subtitle">Подзаголовок / инструкция.</p>
</header>
```

---

## 5. Индикатор прогресса

```css
.progress { display: flex; gap: 6px; margin: 18px 0 22px; flex-wrap: wrap; }
.clip {
  width: 22px; height: 22px; border-radius: 50%; border: 2px solid var(--rule);
  display: flex; align-items: center; justify-content: center;
  font-family: 'Courier Prime', monospace; font-size: 10px; color: var(--pencil);
  background: rgba(255,255,255,.4);
}
.clip.done   { border-color: var(--green); color: var(--green); background: rgba(63,122,79,.08); }
.clip.active { border-color: var(--red); color: var(--red); box-shadow: 0 0 0 3px rgba(181,57,47,.12); }
```

---

## 6. Карточка вопроса (.card)

```css
.card {
  background: rgba(255,255,255,.55);
  border: 1px solid var(--rule);
  border-radius: 4px;
  padding: 22px 22px 18px;
  margin-bottom: 18px;
}

.qnum  { font-family: 'Courier Prime', monospace; color: var(--red); font-size: 13px; margin-bottom: 6px; }
.qtext { font-size: 18px; line-height: 1.45; margin: 0 0 14px; }
.qtext i { color: var(--blue); font-style: italic; }
.qhint { font-size: 14px; color: var(--ink-soft); margin: -8px 0 14px; }
```

HTML-структура внутри карточки:
```html
<div class="card">
  <div class="qnum">Задание N из M</div>
  <p class="qtext">Текст вопроса.</p>
  <!-- тело вопроса -->
  <div class="feedback" id="fb"></div>
  <div class="nav"> … </div>
</div>
```

---

## 7. Компоненты вопросов

### 7.1 Пунктуация (punct)

```css
.ptext {
  background: rgba(255,255,255,.5); border: 1px dashed var(--rule); border-radius: 4px;
  padding: 16px 18px; margin-bottom: 14px; font-size: 16.5px; line-height: 2.1;
}
.slot { display: inline-block; min-width: 10px; text-align: center; cursor: pointer;
  color: var(--rule); font-weight: 700; font-size: 17px; user-select: none; transition: color .12s; }
.slot.selected { color: var(--ink); }
.slot.ok       { color: var(--green); cursor: default; }
.slot.wrong    { color: var(--red); text-decoration: line-through; cursor: default; }
.slot.missed   { color: var(--gold); cursor: default; }
```

Скоринг: `earned = correctCount − floor(wrongCount / 2)`, минимум 0.

### 7.2 Выбор одного ответа (mcq)

```css
.opt {
  display: block; width: 100%; text-align: left; background: transparent;
  border: 1.5px solid var(--rule); border-radius: 3px; padding: 10px 14px; margin-bottom: 9px;
  font-family: 'PT Serif', serif; font-size: 16px; color: var(--ink);
  cursor: pointer; transition: border-color .15s, background .15s; position: relative;
}
.opt:hover             { border-color: var(--ink-soft); }
.opt:disabled          { cursor: default; }
.opt.correct           { border-color: var(--green); background: rgba(63,122,79,.08); }
.opt.wrong             { border-color: var(--red); background: rgba(181,57,47,.07); }
.opt.correct::after    { content: "✓"; position: absolute; right: 14px; top: 50%;
  transform: translateY(-50%) rotate(-6deg); color: var(--green); font-family: 'Caveat', cursive; font-size: 24px; font-weight: 700; }
.opt.wrong::after      { content: "✗"; position: absolute; right: 14px; top: 50%;
  transform: translateY(-50%) rotate(6deg); color: var(--red); font-family: 'Caveat', cursive; font-size: 24px; font-weight: 700; }
```

### 7.3 Сопоставление (match)

```css
.mrow    { display: flex; align-items: center; justify-content: space-between; gap: 10px;
  flex-wrap: wrap; padding: 10px 0; border-bottom: 1px dashed var(--rule); }
.mrow:last-child { border-bottom: none; }
.mphrase { font-size: 16px; font-style: italic; color: var(--ink); flex: 1 1 180px; }
.mbtns   { display: flex; gap: 6px; flex-wrap: wrap; }
.mbtn    { font-family: 'Courier Prime', monospace; font-size: 12.5px;
  border: 1.5px solid var(--rule); background: transparent; color: var(--ink-soft);
  padding: 5px 9px; border-radius: 3px; cursor: pointer; }
.mbtn.picked { border-color: var(--ink); color: var(--ink); background: rgba(35,48,71,.06); }
.mbtn.ok     { border-color: var(--green); color: var(--green); background: rgba(63,122,79,.1); }
.mbtn.wrong  { border-color: var(--red); color: var(--red); background: rgba(181,57,47,.08); }
.mnote       { font-size: 12.5px; color: var(--gold); width: 100%; margin-top: 4px; display: none; }
.mnote.show  { display: block; }
```

### 7.4 Преобразование (transform, самопроверка)

```css
.trow      { padding: 14px 0; border-bottom: 1px dashed var(--rule); }
.trow:last-child { border-bottom: none; }
.tphrase   { font-size: 16px; margin-bottom: 8px; }
.ttype     { font-family: 'Courier Prime', monospace; font-size: 12px; color: var(--ink-soft); }
.ttype.target { color: var(--blue); font-weight: 700; }
.tinput    { width: 100%; font-family: 'PT Serif', serif; font-size: 15.5px; color: var(--ink);
  background: rgba(255,255,255,.6); border: 1.5px solid var(--rule); border-radius: 3px;
  padding: 8px 12px; margin-bottom: 8px; }
.tinput:disabled { opacity: .7; }
.reveal-sample { font-family: 'Courier Prime', monospace; font-size: 12.5px; background: transparent;
  border: 1px solid var(--gold); color: var(--gold); border-radius: 3px; padding: 5px 10px; cursor: pointer; }
.reveal-sample:hover { background: rgba(185,145,47,.08); }
.sample      { font-size: 14.5px; font-style: italic; color: var(--ink-soft); display: none; }
.sample.show { display: inline; }
.gbtn        { font-family: 'Courier Prime', monospace; font-size: 12.5px;
  border: 1.5px solid var(--rule); background: transparent; color: var(--ink-soft);
  padding: 5px 10px; border-radius: 3px; cursor: pointer; }
.gbtn.ok.picked  { border-color: var(--green); color: var(--green); background: rgba(63,122,79,.1); }
.gbtn.bad.picked { border-color: var(--red); color: var(--red); background: rgba(181,57,47,.08); }
.gbtn:disabled   { cursor: default; }
```

---

## 8. Обратная связь (.feedback)

```css
.feedback      { margin-top: 12px; padding: 10px 14px; border-radius: 3px; border: 1px solid transparent;
  font-size: 15px; line-height: 1.5; display: none; }
.feedback.show { display: block; }
.feedback.ok   { background: rgba(63,122,79,.08); color: var(--green);    border-color: var(--green); }
.feedback.bad  { background: rgba(181,57,47,.07); color: var(--red-dark); border-color: var(--red); }
.feedback.mid  { background: rgba(185,145,47,.08); color: var(--red-dark); border-color: var(--gold); }
```

Три состояния: `ok` (всё верно), `bad` (ошибка), `mid` (частично верно). Граница — полная (`border: 1px solid`), не боковая полоска.

---

## 9. Навигация и кнопки

```css
.nav { display: flex; justify-content: space-between; margin-top: 10px; align-items: center; }

.btn {
  font-family: 'Caveat', cursive; font-weight: 700; font-size: 19px;
  background: var(--ink); color: var(--paper);
  border: none; border-radius: 3px; padding: 7px 20px 9px;
  cursor: pointer; transform: rotate(-0.4deg);
}
.btn:hover    { background: var(--red-dark); }
.btn:disabled { opacity: .35; cursor: default; }
.btn.ghost    { background: transparent; color: var(--ink); border: 1.5px solid var(--ink); }
```

- `.nav` использует `justify-content: space-between` — кнопка «Проверить» слева, «Далее →» справа.
- Если кнопка одна (только «Далее →»), добавить `style="justify-content:flex-end;"`.
- Лёгкий наклон `rotate(-0.4deg)` — обязателен, создаёт рукописный характер.

---

## 10. Экран результата

```css
.result      { text-align: center; padding: 30px 10px; }
.stamp       { display: inline-flex; align-items: center; justify-content: center;
  width: 130px; height: 130px; border-radius: 50%;
  border: 4px solid var(--red); color: var(--red);
  font-family: 'Caveat', cursive; font-weight: 700; font-size: 64px;
  transform: rotate(-8deg); margin: 10px auto 18px; position: relative; }
.stamp::before { content: ""; position: absolute; inset: -10px;
  border: 1px dashed var(--red); border-radius: 50%; opacity: .5; }
.result-text { font-size: 17px; color: var(--ink-soft); max-width: 480px; margin: 0 auto; }
.score-line  { font-family: 'Courier Prime', monospace; color: var(--pencil);
  font-size: 13px; margin-bottom: 4px; }
```

Оценки: ≥90% → 5, ≥75% → 4, ≥55% → 3, <55% → 2.

---

## 11. Footer

```css
footer {
  text-align: center; color: var(--pencil); font-size: 12px;
  margin-top: 26px; font-family: 'Courier Prime', monospace;
}
```

---

## 12. Вспомогательные классы

```css
.hidden { display: none; }
```

---

## Расхождения между существующими файлами (нужно привести к эталону)

| Файл | Параметр | Текущее значение | Эталон |
|---|---|---|---|
| `test_szhatie_teksta.html` | `--blue` в `:root` | **отсутствует** | добавить `--blue: #2c5f8a;` |
| `test_szhatie_teksta.html` | `.page max-width` | `720px` | `760px` |
| `test_szhatie_teksta.html` | `h1 font-size` | `44px` | `40px` |
| `test_szhatie_teksta.html` | `.qtext font-size` | `19px` | `18px` |
| `test_szhatie_teksta.html` | `.btn` font-size | `20px` | `19px` |
| `test_szhatie_teksta.html` | `.btn` padding | `8px 22px 10px` | `7px 20px 9px` |
| `test_szhatie_teksta.html` | `.nav` justify-content | `flex-end` (глобально) | `space-between` по умолчанию; `flex-end` только если кнопка одна |
| `test_szhatie_teksta.html` | `header margin-bottom` | `22px` | `18px` |
| `test_szhatie_teksta.html` | `.page` padding-bottom | `28px` | `50px` |
| `test_szhatie_teksta.html` | `.feedback.mid` | **отсутствует** | добавить (gold-вариант для частичного результата) |
