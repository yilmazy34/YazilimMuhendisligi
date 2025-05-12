# YazilimMuhendisligiProjesi
using System;
using System.Collections.Generic;
using System.Threading;

class Program
{
    static int width = 40;
    static int height = 20;
    static int score = 0;
    static bool gameOver = false;

    enum Direction { Up, Down, Left, Right };
    static Direction dir = Direction.Right;

    static List<(int x, int y)> snake = new List<(int x, int y)>();
    static (int x, int y) food;

    static void Main()
    {
        Console.CursorVisible = false;
        Console.SetWindowSize(width, height);
        Console.SetBufferSize(width, height);
        InitializeGame();

        while (!gameOver)
        {
            Draw();
            Input();
            Logic();
            Thread.Sleep(100);
        }

        Console.Clear();
        Console.WriteLine($"Game Over! Your score is: {score}");
        Console.ReadKey();
    }

    static void InitializeGame()
    {
        snake.Clear();
        snake.Add((width / 2, height / 2));
        GenerateFood();
    }

    static void Draw()
    {
        Console.Clear();

        // Draw borders
        for (int x = 0; x < width; x++)
        {
            Console.SetCursorPosition(x, 0); Console.Write("#");
            Console.SetCursorPosition(x, height - 1); Console.Write("#");
        }
        for (int y = 0; y < height; y++)
        {
            Console.SetCursorPosition(0, y); Console.Write("#");
            Console.SetCursorPosition(width - 1, y); Console.Write("#");
        }

        // Draw snake
        foreach (var s in snake)
        {
            Console.SetCursorPosition(s.x, s.y);
            Console.Write("O");
        }

        // Draw food
        Console.SetCursorPosition(food.x, food.y);
        Console.Write("@");

        // Draw score
        Console.SetCursorPosition(2, 0);
        Console.Write($"Score: {score}");
    }

    static void Input()
    {
        if (Console.KeyAvailable)
        {
            ConsoleKey key = Console.ReadKey(true).Key;

            if (key == ConsoleKey.UpArrow && dir != Direction.Down) dir = Direction.Up;
            if (key == ConsoleKey.DownArrow && dir != Direction.Up) dir = Direction.Down;
            if (key == ConsoleKey.LeftArrow && dir != Direction.Right) dir = Direction.Left;
            if (key == ConsoleKey.RightArrow && dir != Direction.Left) dir = Direction.Right;
        }
    }

    static void Logic()
    {
        var head = snake[0];
        (int x, int y) newHead = head;

        switch (dir)
        {
            case Direction.Up: newHead.y--; break;
            case Direction.Down: newHead.y++; break;
            case Direction.Left: newHead.x--; break;
            case Direction.Right: newHead.x++; break;
        }

        // Collision with wall
        if (newHead.x <= 0 || newHead.x >= width - 1 || newHead.y <= 0 || newHead.y >= height - 1)
        {
            gameOver = true;
            return;
        }

        // Collision with self
        if (snake.Contains(newHead))
        {
            gameOver = true;
            return;
        }

        snake.Insert(0, newHead);

        // Eat food
        if (newHead == food)
        {
            score += 10;
            GenerateFood();
        }
        else
        {
            snake.RemoveAt(snake.Count - 1); // remove tail
        }
    }

    static void GenerateFood()
    {
        Random rand = new Random();
        do
        {
            food.x = rand.Next(1, width - 2);
            food.y = rand.Next(1, height - 2);
        } while (snake.Contains(food));
    }
}
