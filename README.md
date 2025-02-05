import tkinter as tk
import random

class Game2048:
    def __init__(self, master):
        self.master = master
        self.master.title("2048 Game")
        self.grid = [[0] * 4 for _ in range(4)]
        self.score = 0
        self.canvas = tk.Canvas(master, width=400, height=400, bg="lightgray")
        self.canvas.pack()
        self.score_label = tk.Label(master, text="Score: 0", font=("Arial", 24))
        self.score_label.pack()
        master.bind("<Key>", self.key_press)
        self.start_game()

    def start_game(self):
        self.grid = [[0] * 4 for _ in range(4)]
        self.score = 0
        self.add_new_tile()
        self.add_new_tile()
        self.update_canvas()

    def add_new_tile(self):
        empty_tiles = [(i, j) for i in range(4) for j in range(4) if self.grid[i][j] == 0]
        if empty_tiles:
            i, j = random.choice(empty_tiles)
            self.grid[i][j] = 2 if random.random() < 0.9 else 4

    def update_canvas(self):
        self.canvas.delete("all")
        for i in range(4):
            for j in range(4):
                value = self.grid[i][j]
                x, y = j * 100, i * 100
                self.canvas.create_rectangle(x, y, x + 100, y + 100, fill=self.get_color(value))
                if value:
                    self.canvas.create_text(x + 50, y + 50, text=str(value), font=("Arial", 24))
        self.score_label.config(text=f"Score: {self.score}")

    def get_color(self, value):
        return {
            0: "lightgray", 2: "lightyellow", 4: "lightblue", 8: "lightgreen",
            16: "lightcoral", 32: "salmon", 64: "orange", 128: "gold",
            256: "yellow", 512: "yellowgreen", 1024: "lightpink", 2048: "red"
        }.get(value, "black")

    def key_press(self, event):
        if event.keysym in ["Up", "Down", "Left", "Right"]:
            self.move(event.keysym)
            self.add_new_tile()
            self.update_canvas()

    def move(self, direction):
        if direction in ["Left", "Right"]:
            for i in range(4):
                self.grid[i] = self.merge(self.grid[i][::1 if direction == "Left" else -1])[::1 if direction == "Left" else -1]
        else:
            self.grid = [list(row) for row in zip(*self.grid)]
            for i in range(4):
                self.grid[i] = self.merge(self.grid[i][::1 if direction == "Up" else -1])[::1 if direction == "Up" else -1]
            self.grid = [list(row) for row in zip(*self.grid)]

    def merge(self, row):
        new_row = [num for num in row if num]
        merged_row = []
        skip = False
        for j in range(len(new_row)):
            if skip:
                skip = False
                continue
            if j + 1 < len(new_row) and new_row[j] == new_row[j + 1]:
                merged_row.append(new_row[j] * 2)
                self.score += new_row[j] * 2
                skip = True
            else:
                merged_row.append(new_row[j])
        return merged_row + [0] * (4 - len(merged_row))

if __name__ == "__main__":
    root = tk.Tk()
    Game2048(root)
    root.mainloop()

    
