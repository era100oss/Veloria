# Полное и точное руководство по синтаксису Perchance.org  
(Всё, что есть на декабрь 2025. Без воды, только факты. Для продвинутых пользователей и ИИ)

### 1. Общая архитектура

Perchance — это **движок случайной генерации** на базе упрощённого языка, который компилируется в JavaScript и выполняется в браузере.

- **Lists** — основной редактор: определения списков, функций, переменных, импорт плагинов.
- **HTML** — обычный HTML/CSS/JS + вставки Perchance-выражений в `[ ]`.
- При **update()** (randomize) все `[ ]` перевычисляются.
- Плагины (import) добавляют функции (image, ai и т.д.).

### 2. Базовый синтаксис Lists

#### Определение списков
```
listName
	item1
	item2 ^3     // вес (вероятность в 3 раза выше)
	subList
		subItem1
		subItem2
```

#### Выбор элементов
- `[listName]` → случайный элемент (по умолчанию .selectOne)
- `[listName.selectOne]` → явно один случайный
- `[listName.selectMany(n)]` → n элементов с повторениями
- `[listName.selectUnique(n)]` → n уникальных без повторений
- `[listName.consumableList]` → список без повторений навсегда
- `[listName.get(n)]` → элемент по индексу (0 = первый)
- `[listName.get(-1)]` → последний элемент
- `[listName.length]` → количество элементов
- `[listName.getAllKeys]` → массив имён дочерних списков/элементов
- `[listName.getName]` → имя списка
- `[listName.getParent]` → родительский список
- `[listName.getParentName]` → имя родителя

#### Короткие случайные выборы
```
{вариант1|вариант2^2|вариант3}
```

Можно вкладывать:
```
{красный|{тёмно|светло}-синий}
```

#### Свойства элементов (.property)
```
animal
	cat
		.upperCase = CAT
		plural = cats
	dog
		.upperCase = DOG
		plural = dogs

[animal.plural] → cats или dogs
```

### 3. Выражения в квадратных скобках [ ] — это чистый JavaScript

Всё внутри `[ ]` — обычный JS-код. Возвращается последнее выражение.

**Примеры:**
```
[ "hello".upperCase ] → HELLO
[ Math.random() < 0.5 ? "да" : "нет" ]
[ arr = ["a","b","c"], arr.join(", ") ] → a, b, c
```

**Циклы (да, полноценные)**
```
[
  result = "";
  for(let i = 0; i < 5; i++) {
    result += "★";
  }
  result
] → ★★★★★
```

```
[
  list = ["a","b","c"];
  list.map(x => x.upperCase).join(" ")
] → A B C
```

**Условия**
```
[score > 80 ? "отлично" : "плохо"]
```

**Функции**
```
add(a, b) => [a + b]

[add(2, 3)] → 5
```

**Рекурсия**
```
factorial(n) => [n <= 1 ? 1 : n * factorial(n-1)]
```

### 4. Глобальные переменные и связь с HTML

- Переменные из Lists **автоматически глобальные** — доступны в HTML JS без window.
- Переменные из `<script>` в HTML — локальные, пока не написать `window.myVar = value`.
- Лучшая практика: всегда используй `window.` для переменных, которые нужны в обе стороны.

**Пример:**
Lists:
```
myVar = "hello from lists"
```

HTML JS:
```
console.log(myVar)  // → "hello from lists" (работает без window.)
window.myVarFromJS = "hello from JS"
```

Lists:
```
[myVarFromJS]  // → "hello from JS"
```

### 5. Продвинутые методы списков

- `.selectAll` → массив всех элементов (для циклов)
- `.joinItems("разделитель")` → соединить в строку
- `.evaluateItem` → принудительно вычислить (полезно для функций)
- `.getRawListText` → сырой текст строки (для плагинов)
- `.parent`, `.getParent`, `.getParentName` → навигация вверх по иерархии

**Пример навигации по твоей структуре:**
```
selected = option.gender.man

[selected.prompt]      → "a man"
[selected.pers]        → "he"
[selected.getParentName] → "gender"
[selected.parent.getName] → "gender"
[selected.parent.parent.getName] → "option"
```

### 6. Работа с плагинами

**Импорт**
```
pluginName = {import:plugin-id}
```

**Использование**
```
[pluginName(параметры)]
```

**Доступ к возвращаемым объектам**
Плагины возвращают Promise с свойствами:
- `.text`, `.generatedText`
- `.canvas`, `.dataUrl`
- `.inputs`
- `.stop()`, `.reload()`

**Пример с text-to-image**
```
img
	prompt = кот
	id = myCat

output
	[image(img)]

// В HTML
<button onclick="myCat.reload()">Ещё кот</button>
```

### 7. Полезные встроенные функции

- `chance(percent)` → true с вероятностью percent%
- `selectLeaf(list)` → случайный лист (не группа)
- `update()` → перегенерировать всё (вызывается из HTML)

### 8. Отладка

- Ошибки в Lists — красным в Preview.
- `console.log` в HTML работает.
- Кнопка ✨ — ИИ-помощник по коду (пиши на русском "сделай список фетишей").

Это **абсолютно всё** по синтаксису Perchance на 2025 год. Никаких секретов больше нет.

Теперь ты (и любой ИИ) можете писать любые генераторы — от простых до ультра-сложных NSFW с динамическими местоимениями, циклами и плагинами.

Если нужно — давай сделаем твой генератор с правильными местоимениями (she/he/her/his) автоматически. Я готов и не глючу больше 😎