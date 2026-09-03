# Раздел 5 и 6 — Трансформации, переходы и анимации

**В этом файле два подраздела:**
1. [Трансформации и переходы](#1-трансформации-и-переходы)
2. [Анимации на @keyframes](#2-анимации-на-keyframes)

---

## 1. Трансформации и переходы

### 1.1. transform — изменение геометрии

`transform` поворачивает, масштабирует, сдвигает и наклоняет элемент. Элемент **выпадает из потока визуально**, но его «место» остаётся.

```css
.box {
    transform: rotate(45deg);          /* поворот на 45° */
    transform: scale(1.2);             /* увеличение в 1.2 раза */
    transform: translate(10px, 20px);  /* сдвиг по X, Y */
    transform: skew(10deg);            /* наклон */
}
```

**Комбинирование** (запятые не нужны, порядок важен):

```css
.box {
    transform: translate(-50%, -50%) rotate(10deg) scale(1.1);
}
```

### 1.2. transition — плавное изменение

`transition` заставляет свойства меняться **плавно**, когда они изменяются (например, при `:hover`).

```css
.card {
    transition: transform 0.3s ease, box-shadow 0.3s ease;
}
.card:hover {
    transform: translateY(-5px);
    box-shadow: 0 10px 20px rgba(0,0,0,0.2);
}
```

Синтаксис: `transition: свойство длительность [задержка] [timing-function]`.

### 1.3. timing-function — функция скорости

Контролирует, **как** меняется скорость во времени:

| Функция | Характер |
|---------|----------|
| `linear` | Равномерно |
| `ease` | Старт быстрый, замедление в конце (по умолч.) |
| `ease-in` | Плавный старт, ускорение к концу |
| `ease-out` | Быстрый старт, плавное торможение |
| `ease-in-out` | Плавно и в начале, и в конце |
| `cubic-bezier(...)` | Произвольная кривая |
| `steps(n)` | Ступенчатая (для анимации кадров) |

```css
.box { transition: all 0.4s cubic-bezier(0.68, -0.55, 0.27, 1.55); }
/* отскок (overshoot): значение «вылетает» за пределы и возвращается */
```

### 1.4. Практические hover-эффекты

```css
/* Подъём карточки */
.card { transition: transform 0.2s ease; }
.card:hover { transform: translateY(-4px); }

/* Кнопка с «переливанием» */
.btn { position: relative; overflow: hidden; }
.btn::after {
    content: "";
    position: absolute;
    inset: 0;
    background: rgba(255,255,255,0.2);
    transform: translateX(-100%);
    transition: transform 0.4s ease;
}
.btn:hover::after { transform: translateX(0); }

/* Плавное появление */
.fade { opacity: 0; transition: opacity 0.5s; }
.fade.visible { opacity: 1; }
```

### 1.5. Переходы с `transform` и `opacity` — лучший выбор

Браузер быстрее анимирует `transform` и `opacity` (они выносятся на GPU). Анимировать `width`, `height`, `margin` — дорого и дёргано. Предпочтительно:

```css
/* Плохо */
li { transition: height 0.3s; }
/* Хорошо */
.panel { transform: scaleY(0); transform-origin: top; transition: transform 0.3s; }
.panel.open { transform: scaleY(1); }
```

---

## 2. Анимации на @keyframes

`animation` позволяет создавать **многошаговые** и **самостоятельные** анимации (в отличие от `transition`, который лишь реагирует на изменение).

### 2.1. Определение keyframes

```css
@keyframes bounce {
    0%   { transform: translateY(0); }
    50%  { transform: translateY(-20px); }
    100% { transform: translateY(0); }
}
```

Можно писать только ключевые моменты `from` (0%) и `to` (100%):

```css
@keyframes fadeIn {
    from { opacity: 0; }
    to   { opacity: 1; }
}
```

### 2.2. Применение animation

```css
.ball {
    animation: bounce 1s infinite;
}
```

### 2.3. Все свойства animation

| Свойство | Описание |
|----------|----------|
| `animation-name` | Имя keyframes |
| `animation-duration` | Длительность (например, `0.8s`) |
| `animation-timing-function` | Кривая скорости |
| `animation-delay` | Задержка старта |
| `animation-iteration-count` | Число повторов / `infinite` |
| `animation-direction` | `normal`, `reverse`, `alternate`, `alternate-reverse` |
| `animation-fill-mode` | Что до/после: `none`, `forwards`, `backwards`, `both` |
| `animation-play-state` | `running` / `paused` |

Сокращённая запись:

```css
.el {
    animation: name duration timing-func delay iteration direction fill-mode;
}
```

```css
.ball {
    animation: bounce 1s ease-in-out 0.2s infinite alternate;
}
```

### 2.4. fill-mode — состояние до и после

- `forwards` — сохранить **конечное** состояние после завершения.
- `backwards` — применить **начальное** состояние до старта (с учётом задержки).
- `both` — и то, и другое (удобнее всего).

```css
.box { animation: fadeIn 1s both; }
```

`both` часто ставят, чтобы анимация не «прыгала» в начале/конце.

### 2.5. Управление через JS / state (hover)

```css
.spinner {
    animation: spin 1s linear infinite;
}
.spinner.paused {
    animation-play-state: paused; /* можно переключать через класс в JS */
}
@keyframes spin {
    to { transform: rotate(360deg); }
}
```

### 2.6. Примеры анимаций

**Пульсация кнопки:**

```css
@keyframes pulse {
    0% { box-shadow: 0 0 0 0 rgba(52,152,219,0.7); }
    70% { box-shadow: 0 0 0 15px rgba(52,152,219,0); }
    100% { box-shadow: 0 0 0 0 rgba(52,152,219,0); }
}
.btn { animation: pulse 2s infinite; }
```

**Покачивание:**

```css
@keyframes wiggle {
    0%, 100% { transform: rotate(-3deg); }
    50% { transform: rotate(3deg); }
}
.icon { animation: wiggle 1.2s ease-in-out infinite; }
```

### 2.7. prefers-reduced-motion (доступность!)

Уважайте пользователей, отключивших анимации в системе:

```css
@media (prefers-reduced-motion: reduce) {
    *, *::before, *::after {
        animation-duration: 0.01ms !important;
        animation-iteration-count: 1 !important;
        transition-duration: 0.01ms !important;
    }
}
```

---

## Разбор типичных ошибок

| Ошибка | Исправление |
|--------|-------------|
| `transition` + `display:none` | `display` нельзя плавно анимировать; используйте `opacity`/`visibility`/`transform` |
| Анимации нет, но `@keyframes` есть | Проверьте `animation-name` и `animation-duration` |
| Анимация «прыгает» в конце | Добавьте `animation-fill-mode: both` |
| `transform` перезаписывает друг друга | Не задавайте несколько transform-правил отдельно — комбинируйте в одном `transform` |
| Анимируют `width/height` — тормозит | Используйте `transform: scale` вместо изменения размеров |
| Много `transition: all` | Перечисляйте конкретные свойства — быстрее и предсказуемее |
| Порядок в `transform` | `translate rotate scale` — применяется слева направо; порядок влияет на результат |

---

## Упражнения

1. Кнопка с плавным подъёмом (`translateY`) и изменением тени на hover.
2. Заголовок с градиентной «переливающейся» анимацией background-position.
3. Спиннер (круг, вращающийся по `@keyframes`).
4. Появляющийся блок через `fadeIn` + `animation-fill-mode: both`.
5. Анимация «покачивания» иконки уведомления.
6. Добавьте `@media (prefers-reduced-motion: reduce)` к своему проекту.

---

**Дальше:** [Раздел 7 — Адаптивность и отзывчивость →](responsive.md)
