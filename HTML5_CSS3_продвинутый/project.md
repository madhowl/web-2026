# Раздел 12 и 13 — Продвинутые селекторы и итоговый проект

**В этом файле два подраздела:**
1. [Продвинутые селекторы](#1-продвинутые-селекторы)
2. [Итоговый практический проект](#2-итоговый-практический-проект)

---

## 1. Продвинутые селекторы

### 1.1. Селекторы атрибутов

`[атрибут]` — элемент с данным атрибутом:

```css
input[type="text"] { ... }          /* точное совпадение значения */
a[href^="https"] { ... }            /* атрибут НАЧИНАЕТСЯ с... (^=) */
img[src$=".png"] { ... }            /* ЗАКАНЧИВАЕТСЯ на... ($=) */
a[href*="example"] { ... }          /* СОДЕРЖИТ подстроку (*=) */
[class~="featured"] { ... }         /* есть слово в списке классов */
```

Пример:

```css
a[href^="https://"]::after {
    content: " ↗";
}
```

### 1.2. Структурные псевдоклассы

```css
li:first-child { ... }              /* первый ребёнок */
li:last-child { ... }               /* последний */
li:nth-child(2) { ... }             /* n-ый */
li:nth-child(odd) { ... }           /* нечётные */
li:nth-child(even) { ... }          /* чётные */
li:nth-child(3n+1) { ... }          /* каждый 3-й, начиная с 1-го */
li:only-child { ... }               /* единственный ребёнок */
```

### 1.3. Отрицание `:not()`

```css
p:not(.skip) { ... }                /* все p, кроме .skip */
input:not(:placeholder-shown) { ... } /* поля, где что-то введено */
```

Разница `:first-child` и `:first-of-type`:
- `:first-child` — первый **среди всех** детей.
- `:first-of-type` — первый **своего тега**.

```css
p:first-child { }  /* p, который является первым ребёнком */
p:first-of-type { } /* первый p среди всех элементов (даже если их разных типов много) */
```

### 1.4. Современные группы: `:is()`, `:where()`, `:has()`

**`:is()`** — группа с максимумом специфичности из перечисленных:

```css
:is(h1, h2, h3) { color: #333; }
/* эквивалентно h1, h2, h3 */
```

Полезно с вложенностью:

```css
.card :is(h1, h2, h3) { margin-top: 0; }
```

**`:where()`** — как `:is()`, но специфичность **всегда нулевая** (легко переопределить):

```css
:where(.header, .footer) a { color: blue; }
/* проще переопределить ниже, т.к. специфичность 0 */
```

**`:has()`** — «у кого есть...» (родительские селекторы):

```css
.card:has(img) { border: 1px solid #ddd; }   /* карточка, содержащая картинку */
form:has(input:invalid) { opacity: 0.5; }    /* форма с неверным полем */
.nav li:has(a.active) { font-weight: bold; } /* пункт меню с активной ссылкой */
```

> `:has()` поддерживается современными браузерами и очень удобен для состояний, ранее требовавших JS.

### 1.5. Псевдоэлементы и фокус

```css
::selection { background: #3498db; color: #fff; }   /* выделенный текст */
:focus-visible { outline: 3px solid #3498db; }     /* видимый фокус с клавиатуры */
```

---

## 2. Итоговый практический проект

### Проект: «Сайт-визитка студии веб-дизайна»

Соберём многосекционный адаптивный лендинг, объединяя всё изученное: **Grid + Flexbox, BEM, переменные, формы, анимации, доступность**.

### 2.1. Структура файлов

```
studio/
├── index.html
├── css/
│   ├── variables.css
│   ├── reset.css
│   ├── base.css
│   ├── components.css
│   └── main.css
└── assets/ (изображения)
```

### 2.2. Содержимое страницы

Секции (`section`):
1. Шапка с логотипом и навигацией (Flexbox).
2. Hero-баннер с заголовком, подзаголовком и CTA-кнопкой (Grid, градиент, анимация появления).
3. «Услуги» — карточки (Grid `auto-fit`, каждая в BEM).
4. «Портфолио» — галерея (Grid, hover-зум через `transform`).
5. «Тарифы» — карточки с модификатором `--popular`.
6. Форма обратной связи (стилизованная форма, валидация, доступность).
7. Подвал (Flexbox).

### 2.3. Ключевые фрагменты CSS

**Переменные (variables.css):**

```css
:root {
    --primary: #5b21b6;
    --primary-dark: #4c1d95;
    --bg: #ffffff;
    --text: #1f2937;
    --muted: #6b7280;
    --radius: 12px;
    --space-3: 12px;
    --space-6: 24px;
    --shadow: 0 4px 12px rgba(0,0,0,0.08);
}
```

**Reset + base (reset.css, base.css):**

```css
*, *::before, *::after { box-sizing: border-box; }
body, h1, h2, h3, p { margin: 0; }
img { max-width: 100%; display: block; }
body { font-family: 'Roboto', sans-serif; color: var(--text); line-height: 1.6; }
```

**Hero (components.css):**

```css
.hero {
    display: grid;
    grid-template-columns: 1fr 1fr;
    align-items: center;
    gap: var(--space-6);
    padding: 64px 16px;
    background: linear-gradient(135deg, var(--primary), #8b5cf6);
    color: #fff;
}
.hero__title { font-size: clamp(2rem, 5vw, 3rem); line-height: 1.2; }
.hero__text { margin: var(--space-3) 0; }

.hero__button {
    background: #fff;
    color: var(--primary);
    border: none;
    padding: 12px 24px;
    border-radius: 999px;
    font-weight: 700;
    cursor: pointer;
    transition: transform 0.2s, box-shadow 0.2s;
}
.hero__button:hover {
    transform: translateY(-2px);
    box-shadow: 0 8px 20px rgba(0,0,0,0.2);
}
```

**Услуги — Grid (main.css):**

```css
.services { max-width: 1200px; margin: 0 auto; padding: 64px 16px; }
.services__grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(240px, 1fr));
    gap: var(--space-6);
}
.service {
    background: #fff;
    border: 1px solid #e5e7eb;
    border-radius: var(--radius);
    box-shadow: var(--shadow);
    padding: var(--space-6);
    transition: transform 0.25s;
}
.service:hover { transform: translateY(-4px); }
```

**Портфолио — галерея (components.css):**

```css
.portfolio__grid {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: var(--space-3);
}
.portfolio__item {
    overflow: hidden;
    border-radius: var(--radius);
    aspect-ratio: 4 / 3;
}
.portfolio__item img {
    width: 100%;
    height: 100%;
    object-fit: cover;
    transition: transform 0.4s;
}
.portfolio__item:hover img { transform: scale(1.1); }
```

**Адаптив (media в main.css):**

```css
@media (max-width: 768px) {
    .hero { grid-template-columns: 1fr; text-align: center; }
    .portfolio__grid { grid-template-columns: repeat(2, 1fr); }
}
@media (max-width: 480px) {
    .portfolio__grid { grid-template-columns: 1fr; }
}
```

**Анимация появления Hero (base.css):**

```css
@keyframes fadeInUp {
    from { opacity: 0; transform: translateY(20px); }
    to   { opacity: 1; transform: translateY(0); }
}
.hero__content { animation: fadeInUp 0.8s ease both; }
```

**Доступность (base.css):**

```css
:focus-visible { outline: 3px solid var(--primary); outline-offset: 2px; }
.skip-link { position: absolute; left: -9999px; }
.skip-link:focus { position: fixed; top: 8px; left: 8px; background: #fff; padding: 8px; z-index: 100; }

@media (prefers-reduced-motion: reduce) {
    *, *::before, *::after {
        animation-duration: 0.01ms !important;
        transition-duration: 0.01ms !important;
    }
}
```

### 2.4. Форма (BEM + валидация)

```html
<form class="form" action="#" method="post">
    <label class="form__label" for="name">Имя</label>
    <input class="form__input" type="text" id="name" name="name" required>

    <label class="form__label" for="email">Email</label>
    <input class="form__input" type="email" id="email" name="email" required>

    <label class="form__label" for="msg">Сообщение</label>
    <textarea class="form__input" id="msg" name="msg" rows="4"></textarea>

    <button class="form__submit" type="submit">Отправить</button>
</form>
```

```css
.form { max-width: 480px; display: grid; gap: var(--space-3); }
.form__label { font-weight: 600; }
.form__input {
    padding: 12px 14px;
    border: 2px solid #e5e7eb;
    border-radius: 8px;
    transition: border-color 0.2s, box-shadow 0.2s;
}
.form__input:focus { border-color: var(--primary); outline: none; box-shadow: 0 0 0 3px rgba(91,33,182,0.15); }
.form__input:invalid:not(:placeholder-shown) { border-color: #dc2626; }
.form__submit {
    background: var(--primary);
    color: #fff;
    border: none;
    padding: 12px;
    border-radius: 8px;
    cursor: pointer;
    transition: background 0.2s;
}
.form__submit:hover { background: var(--primary-dark); }
```

### 2.5. Чек-лист завершения проекта

- [ ] Общий каркас на **Grid** с `grid-template-areas` или колонками.
- [ ] Содержимое внутри ячеек выровнено через **Flexbox**.
- [ ] Все классы — по **BEM** (блок/элемент/модификатор).
- [ ] Цвета, отступы, радиусы — через **CSS-переменные**.
- [ ] **Адаптив**: hero и галерея перестраиваются на мобильном.
- [ ] Типографика через `clamp()` / переменные.
- [ ] **Анимации**: появление hero, hover на карточках, зумы в галерее.
- [ ] **Доступность**: skip-link, `:focus-visible`, `alt`, `label`, `prefers-reduced-motion`.
- [ ] **Lighthouse** проходит аудит без критических проблем.

### 2.6. Советы по сдаче проекта

1. Прогоните через **Lighthouse** (вкладка DevTools) и исправьте проблемы a11y и производительности.
2. Проверьте вёрстку на 375 / 768 / 1024 / 1440px.
3. Убедитесь, что при навигации с клавиатуры всё достигается и виден фокус.
4. Проконтролируйте контраст текста (минимум 4.5:1).

---

## Финальная самопроверка по всему продвинутому курсу

1. Когда выбирать Grid, а когда Flexbox?
2. Что делает `repeat(auto-fit, minmax(200px, 1fr))`?
3. Как объявить и использовать CSS-переменную? Зачем `:root`?
4. Что такое `clamp()` и где он полезен?
5. Зачем `box-sizing: border-box` глобально?
6. Как сделать кастомный чекбокс, сохранив доступность?
7. Разница `:focus` и `:focus-visible`?
8. Как анимировать появление без «прыжков»?
9. Для чего `animation-fill-mode: both`?
10. Что такое BEM: блок, элемент, модификатор?
11. Зачем `prefers-reduced-motion`?
12. Что делает `:has()`? Приведите пример.
13. Разница `srcset` и `<picture>`?
14. Что такое skip-link?

> Если вы уверенно отвечаете на большинство вопросов и собрали проект из раздела 13 — продвинутый уровень освоен. 🎓

---

**Начало курса:** [Раздел 1 — CSS Grid →](grid.md) | [Оглавление →](index.md)
