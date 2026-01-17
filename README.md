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

root = Tk()
root.title("Змейка")

canvas = Canvas(root, width=300, height=300, bg="black")
canvas.pack()

game = SnakeGame(canvas)

root.mainloop()
