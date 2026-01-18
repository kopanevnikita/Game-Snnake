# Game-Snnake
# I wrote a snake game in Python, and it has a very simple interface. 

# I will add to it.

# It will open on any computer that has Python

# Almost any version of Python will do.

# The control is done using the arrows

# The author is Nikita Kopanev

    from tkinter import Tk, Canvas
    from random import randint
    
    
    
    class SnakeGame:
        """Игра «Змейка» на tkinter."""
    
        def __init__(self, canvas):
            self.canvas = canvas
            self.cell_size = 10  # Размер одной ячейки (пикселей)
            self.field_size = 30  # Поле 30×30 ячеек
    
            # Начальная позиция змеи (одна ячейка в центре)
            self.snake_coords = [[14, 14]]
            # Позиция яблока
            self.apple_coords = self._generate_apple()
    
            # Векторы движения по направлениям
            self.directions = {
                "Up": (0, -1),
                "Down": (0, 1),
                "Left": (-1, 0),
                "Right": (1, 0)
            }
            self.current_direction = self.directions["Right"]
    
            # Фокусировка на canvas для приёма клавиш
            self.canvas.focus_set()
            self.canvas.bind("<KeyPress>", self._handle_keypress)
    
            # Запуск игрового цикла
            self._game_loop()
    
        def _generate_apple(self):
            """Генерирует новую позицию яблока, не совпадающую с телом змеи."""
            while True:
                x = randint(0, self.field_size - 1)
                y = randint(0, self.field_size - 1)
                if [x, y] not in self.snake_coords:
                    return [x, y]
    
        def _handle_keypress(self, event):
            """Обрабатывает нажатие клавиш управления."""
            if event.keysym in self.directions:
                self.current_direction = self.directions[event.keysym]
    
        def _wrap_coordinate(self, coord):
            """
            «Замыкает» координату: при выходе за границы поля переносит на противоположную сторону.
            0 → 29, 30 → 0 и т. д.
            """
            return coord % self.field_size
    
        def _draw(self):
            """Отрисовывает игровое поле: яблоко и змею."""
            self.canvas.delete("all")  # Очищаем холст
    
            # Рисуем яблоко
            x_apple, y_apple = self.apple_coords
            self.canvas.create_rectangle(
                x_apple * self.cell_size,
                y_apple * self.cell_size,
                (x_apple + 1) * self.cell_size,
                (y_apple + 1) * self.cell_size,
                fill="red",
                width=0
            )
    
            # Рисуем змею
            for x, y in self.snake_coords:
                self.canvas.create_rectangle(
                    x * self.cell_size,
                    y * self.cell_size,
                    (x + 1) * self.cell_size,
                    (y + 1) * self.cell_size,
                    fill="green",
                    width=0
                )
    
        def _game_loop(self):
            """Основной игровой цикл: движение, проверка столкновений, перерисовка."""
            self._draw()
    
            # Получаем текущую голову змеи
            head_x, head_y = self.snake_coords[0]
            dx, dy = self.current_direction
    
            # Вычисляем новую позицию головы
            new_head_x = self._wrap_coordinate(head_x + dx)
            new_head_y = self._wrap_coordinate(head_y + dy)
    
            # Проверяем, съела ли змея яблоко
            if [new_head_x, new_head_y] == self.apple_coords:
                self.apple_coords = self._generate_apple()  # Новое яблоко
                # Змея растёт: добавляем новую голову, хвост не удаляем
                self.snake_coords.insert(0, [new_head_x, new_head_y])
            # Проверяем столкновение с собственным телом
            elif [new_head_x, new_head_y] in self.snake_coords:
                self.snake_coords = []  # Игра окончена (очищаем змею)
            else:
                # Двигаем змею: новая голова, удаляем хвост
                self.snake_coords.insert(0, [new_head_x, new_head_y])
                self.snake_coords.pop()
    
            # Планируем следующий кадр через 100 мс
            self.canvas.after(100, self._game_loop)



    # Создание окна и запуск игры
    root = Tk()
    root.title("Змейка")

    canvas = Canvas(root, width=300, height=300, bg="black")
    canvas.pack()

    game = SnakeGame(canvas)

    root.mainloop()

    game = SnakeGame(canvas)

    root.mainloop()

    # Проект: Змейка (Tkinter)

## 1. Основные компоненты

### 1.1. Главный класс `SnakeGame`
Отвечает за логику игры, отрисовку и управление.

**Атрибуты:**
- `canvas` — холст для рисования (экземпляр `Canvas`).
- `cell_size` — размер одной ячейки поля в пикселях (`10`).
- `field_size` — размер поля в ячейках (`30×30`).
- `snake_coords` — список координат сегментов змеи (начинается с `[[14, 14]]`).
- `apple_coords` — координаты яблока (`[x, y]`).
- `directions` — словарь векторов движения по клавишам (`Up`, `Down`, `Left`, `Right`).
- `current_direction` — текущее направление движения змеи.

**Методы:**
- `__init__(self, canvas)` — инициализация игры.
- `_generate_apple(self)` — генерация новой позиции яблока.
- `_handle_keypress(self, event)` — обработка нажатий клавиш.
- `_wrap_coordinate(self, coord)` — «замыкание» координат (переход через границы поля).
- `_draw(self)` — отрисовка поля, яблока и змеи.
- `_game_loop(self)` — основной игровой цикл (движение, проверка условий, перерисовка).

---

## 2. Логика работы

### 2.1. Инициализация
- Создаётся окно (`Tk`) и холст (`Canvas`).
- Устанавливается фокус на холст для приёма клавиш.
- Запускается игровой цикл `_game_loop()`.

### 2.2. Игровой цикл (`_game_loop`)
1. **Отрисовка** (`_draw`):
   - Очищается холст.
   - Рисуется яблоко (красный квадрат).
   - Рисуется змея (зелёные квадраты).
2. **Движение головы**:
   - Вычисляются новые координаты головы с учётом направления.
   - Применяется `_wrap_coordinate` для «телепортации» через границы.
3. **Проверка условий**:
   - Если голова на яблоке → яблоко перегенерируется, змея растёт.
   - Если голова в теле змеи → игра завершается (змея очищается).
   - Иначе → добавляется новая голова, удаляется хвост.
4. **Повтор цикла** через 100 мс (`after`).

### 2.3. Управление
- Клавиши `Up`, `Down`, `Left`, `Right` меняют направление (`current_direction`).
- Обработчик `_handle_keypress` реагирует только на допустимые клавиши.

### 2.4. Генерация яблока
- Метод `_generate_apple` ищет случайную позицию, не занятую змеёй.
- Используется цикл `while True` с проверкой `if [x, y] not in self.snake_coords`.

---

## 3. Графика и интерфейс

### 3.1. Холст (`Canvas`)
- Размер: `300×300` пикселей (30 ячеек × 10 px).
- Фон: чёрный (`bg="black"`).
- Отрисовка через `create_rectangle` (квадраты 10×10 px).

### 3.2. Цвета
- Змея: зелёный (`"green"`).
- Яблоко: красный (`"red"`).
- Фон: чёрный (`"black"`).

---

## 4. Потенциальные расширения

### 4.1. Счётчик очков
- Добавить атрибут `self.score`.
- Увеличивать счёт при поедании яблока.
- Отображать счёт на холсте (например, через `create_text`).

### 4.2. Экран «Game Over»
- При столкновении с телом:
  - Очистить холст.
  - Вывести текст «GAME OVER» и финальный счёт.
  - Предложить перезапуск по клавише `R`.

### 4.3. Уровни сложности
- Уменьшать задержку (`after`) с ростом счёта.
- Добавлять препятствия на поле.

### 4.4. Сохранение рекорда
- Записывать лучший результат в файл (`json` или `txt`).
- Отображать рекорд на экране.

### 4.5. Улучшенная графика
- Использовать изображения (через `PhotoImage`) вместо цветных квадратов.
- Добавить анимацию движения.

---

## 5. Запуск проекта

**Требования:**
- Python 3.x.
- Библиотека `tkinter` (встроена в Python).

**Инструкция:**
1. Сохранить код в файл `snake.py`.
2. Запустить: `python snake.py`.
3. Управлять змейкой стрелками клавиатуры.

---

## 6. Диаграмма классов (упрощённо)

SnakeGame
├── init
├── _generate_apple
├── _handle_keypress
├── _wrap_coordinate
├── _draw
└── _game_loop

Атрибуты:
├── canvas
├── cell_size
├── field_size
├── snake_coords
├── apple_coords
├── directions
└── current_direction
