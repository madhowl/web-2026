# Раздел 3 и 4 — Оформление форм и продвинутая типографика

**В этом файле два связанных подраздела:**
1. [Оформление форм](#1-оформление-форм)
2. [Продвинутая типографика](#2-продвинутая-типографика)

---

## 1. Оформление форм

### 1.1. Сброс браузерных стилей форм

Встроенные стили форм различаются между браузерами. Для начала зададим единый вид:

```css
input,
textarea,
select,
button {
    font: inherit;            /* наследовать шрифт от страницы */
    box-sizing: border-box;   /* размеры с учётом padding/border */
    appearance: none;         /* убрать «нативную» внешность (осторожно с checkbox/radio) */
}
```

### 1.2. Стилизация поля ввода

```css
.field {
    display: block;
    width: 100%;
    padding: 12px 16px;
    border: 2px solid #ccc;
    border-radius: 8px;
    background: #fff;
    transition: border-color 0.2s, box-shadow 0.2s;
}

.field:focus {
    outline: none;               /* убрать синюю обводку по умолчанию (но см. 1.4!) */
    border-color: #3498db;
    box-shadow: 0 0 0 3px rgba(52, 152, 219, 0.2);
}
```

### 1.3. Псевдоклассы состояния формы

| Псевдокласс | Значение |
|-------------|----------|
| `:focus` | Поле в фокусе (активен, готов к вводу) |
| `:focus-visible` | Фокус, видимый при навигации с клавиатуры (⚠️ важно для a11y) |
| `:hover` | Наведение мыши |
| `:disabled` | Заблокированное поле |
| `:placeholder-shown` | Поле, где плейсхолдер виден (пустое) |
| `:invalid` | Неверно заполнено |
| `:valid` | Заполнено верно |
| `:checked` | Отмеченный чекбокс/радио |
| `:required` | Обязательное поле |

```css
input:required {
    border-left: 4px solid #f39c12;
}
input:invalid:not(:placeholder-shown) {
    border-color: #e74c3c;      /* ошибка, если введено и неверно */
}
input:valid {
    border-color: #27ae60;
}
```

> ⚠️ **`:placeholder-shown`** фильтрует пустые поля, чтобы не «ругаться» на ещё не заполненные.

### 1.4. Стилизация `:focus-visible` (доступность)

Не удаляйте обводку полностью! Вместо `outline: none` используйте собственный стиль фокуса. `:focus-visible` применяется только когда фокус идёт с клавиатуры (а не мышью):

```css
:focus-visible {
    outline: 3px solid #3498db;
    outline-offset: 2px;
}
```

### 1.5. Кастомные чекбоксы и радиокнопки

«Нативные» чекбоксы сложно стилизовать. Приём: скрыть нативный и рисовать свой через `::before`/`::after`.

```html
<label class="check">
    <input type="checkbox">
    <span class="check-box"></span>
    Согласен с условиями
</label>
```

```css
.check input {
    position: absolute;
    opacity: 0;               /* скрыть, но оставить в DOM (для доступности) */
    pointer-events: none;
}
.check {
    display: inline-flex;
    align-items: center;
    gap: 8px;
    cursor: pointer;
}
.check-box {
    width: 20px;
    height: 20px;
    border: 2px solid #999;
    border-radius: 4px;
    transition: background 0.2s, border-color 0.2s;
}
.check input:checked + .check-box {
    background: #3498db;
    border-color: #3498db;
}
```

Для галочки внутри можно использовать `::after` с `content: "✓"` и `opacity`.

> Скрывать чекбокс через `display: none` плохо для доступности — используйте `opacity: 0`, чтобы поле оставалось фокусируемым.

### 1.6. Стилизация `<select>`

`appearance: none` убирает стрелку; её рисуют через фон или `::after`.

```css
.select-wrap { position: relative; }
.select-wrap::after {
    content: "▾";
    position: absolute;
    right: 12px;
    top: 50%;
    transform: translateY(-50%);
    pointer-events: none;
}
select {
    appearance: none;
    width: 100%;
    padding: 12px;
    border: 2px solid #ccc;
    border-radius: 8px;
    background: #fff;
}
```

### 1.7. Кнопки

```css
.btn {
    padding: 12px 24px;
    border: none;
    border-radius: 8px;
    background: #3498db;
    color: #fff;
    cursor: pointer;
    transition: background 0.2s, transform 0.1s;
}
.btn:hover { background: #2980b9; }
.btn:active { transform: scale(0.98); }
.btn:disabled { opacity: 0.5; cursor: not-allowed; }
```

---

### Разбор типичных ошибок при стилизации форм

| Ошибка | Исправление |
|--------|-------------|
| `outline: none` без замены | Замените на собственный `:focus-visible` стиль |
| Скрыли checkbox `display:none` | Проблемы с доступностью — используйте `opacity: 0` |
| `font` у форм не совпадает с сайтом | `font: inherit` на input/textarea/select/button |
| `:invalid` «орет» на пустых полях | Комбинируйте с `:not(:placeholder-shown)` |
| Размеры «плывут» | `box-sizing: border-box` для полей |

---

## 2. Продвинутая типографика

### 2.1. Подключение веб-шрифтов через @font-face

`@font-face` подключает локальные файлы шрифтов:

```css
@font-face {
    font-family: "MyFont";
    src: url("myfont.woff2") format("woff2");
    font-weight: 400;
    font-display: swap; /* показать системный, пока грузится (лучший UX) */
}
@font-face {
    font-family: "MyFont";
    src: url("myfont-bold.woff2") format("woff2");
    font-weight: 700;
}
```

Затем:

```css
body { font-family: "MyFont", sans-serif; }
h1 { font-weight: 700; }
```

### 2.2. Google Fonts (быстрый способ)

```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Roboto:wght@400;700&display=swap" rel="stylesheet">
```

```css
body { font-family: 'Roboto', sans-serif; }
```

### 2.3. Модульная шкала (modular scale)

Используйте **логичную шкалу** размеров, а не «на глаз». Классическая — с коэффициентом 1.25 (major third):

```
16px → 20px → 25px → 31px → 39px → 49px ...
```

```css
:root {
    --fs-sm: 0.875rem;
    --fs-base: 1rem;
    --fs-md: 1.25rem;
    --fs-lg: 1.563rem;
    --fs-xl: 1.953rem;
    --fs-xxl: 2.441rem;
}
```

### 2.4. Fluid-типографика через clamp()

Чтобы размер плавно рос с экраном:

```css
h1 { font-size: clamp(1.8rem, 4vw, 3.2rem); }
```

### 2.5. Кернинг, межбуквенный интервал

```css
h1 {
    font-kerning: normal;
    letter-spacing: -0.02em;  /* чуть плотнее для больших заголовков */
}
.small-caps { font-variant: small-caps; }
```

### 2.6. Горизонтальные отступы и ритм

Единый вертикальный ритм — залог аккуратного текста:

```css
p { margin: 0 0 1em 0; }      /* единый отступ после каждого абзаца */
h1, h2, h3 { line-height: 1.2; }
body { line-height: 1.6; }
```

### 2.7. Эффектный текст

- **Градиент текста (текст-картинка):**

```css
.gradient-text {
    background: linear-gradient(90deg, #f39c12, #e74c3c);
    -webkit-background-clip: text;
    background-clip: text;
    color: transparent;
}
```

- **Тень текста:**

```css
.hero-title {
    text-shadow: 2px 2px 4px rgba(0,0,0,0.5);
}
```

- **Сглаженные шрифты:**

```css
body { -webkit-font-smoothing: antialiased; }
```

---

### Разбор типичных ошибок в типографике

| Ошибка | Исправление |
|--------|-------------|
| Размеры «на глаз», нет системы | Модульная шкала / переменные `--fs-*` |
| Слишком длинная строка | Ограничьте `max-width`, например `65ch` (ширина в символах) |
| Заголовки слишком большие, текст теряется | Согласуйте scale и line-height (1.1–1.3 для заголовков) |
| `px` на шрифтах в адаптиве | Используйте `rem` и `clamp()` |
| Не подключён `font-display: swap` | Риск «прыжка» шрифта; добавьте swap |
| Забыли запасное семейство | Всегда `, sans-serif` или `, serif` в конце |

Хороший пример «дыхания» текстов:

```css
article { max-width: 65ch; line-height: 1.7; }
article h2 { margin-top: 2em; font-size: var(--fs-lg); }
```

---

## Упражнения

### Формы
1. Стилизуйте поле ввода с `:focus` подсветкой и тенью.
2. Сделайте кастомный чекбокс с галочкой через `::after`.
3. Добавьте `:invalid` / `:valid` стили для обязательного `email`-поля.
4. Уберите стрелку нативного `<select>` и нарисуйте свою.

### Типографика
5. Подключите Google Font и задайте его странице.
6. Создайте `--fs-*` переменные по модульной шкале.
7. Сделайте заголовок с градиентной заливкой текста.
8. Примените `clamp()` к заголовку и основному тексту.

---

**Дальше:** [Раздел 5 — Трансформации и переходы →](animation.md)
