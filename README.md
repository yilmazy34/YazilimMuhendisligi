# YazilimMuhendisligiProjesi

using System;
using System.Collections.Generic;
using System.Threading;

class YilanOyunu
{
    static int alanGenislik = 40;
    static int alanYukseklik = 20;
    static int puan = 0;
    static bool oyunBitti = false;

    enum Yon { Yukari, Asagi, Sol, Sag };
    static Yon mevcutYon = Yon.Sag;

    static List<(int x, int y)> yilanGovde = new List<(int x, int y)>();
    static (int x, int y) yem;

    static void Main()
    {
        Console.CursorVisible = false;
        Console.SetWindowSize(alanGenislik, alanYukseklik);
        Console.SetBufferSize(alanGenislik, alanYukseklik);
        OyunuBaslat();

        while (!oyunBitti)
        {
            CizimYap();
            TusKontrol();
            OyunMantigi();
            Thread.Sleep(100);
        }

        Console.Clear();
        Console.WriteLine($"Oyun Bitti! Puanınız: {puan}");
        Console.ReadKey();
    }

    static void OyunuBaslat()
    {
        yilanGovde.Clear();
        yilanGovde.Add((alanGenislik / 2, alanYukseklik / 2));
        YeniYemUret();
    }

    static void CizimYap()
    {
        Console.Clear();

        // Kenarlık çiz
        for (int x = 0; x < alanGenislik; x++)
        {
            Console.SetCursorPosition(x, 0); Console.Write("#");
            Console.SetCursorPosition(x, alanYukseklik - 1); Console.Write("#");
        }
        for (int y = 0; y < alanYukseklik; y++)
        {
            Console.SetCursorPosition(0, y); Console.Write("#");
            Console.SetCursorPosition(alanGenislik - 1, y); Console.Write("#");
        }

        // Yılan çiz
        foreach (var parca in yilanGovde)
        {
            Console.SetCursorPosition(parca.x, parca.y);
            Console.Write("O");
        }

        // Yem çiz
        Console.SetCursorPosition(yem.x, yem.y);
        Console.Write("@");

        // Puan yazdır
        Console.SetCursorPosition(2, 0);
        Console.Write($"Puan: {puan}");
    }

    static void TusKontrol()
    {
        if (Console.KeyAvailable)
        {
            ConsoleKey tus = Console.ReadKey(true).Key;

            if (tus == ConsoleKey.UpArrow && mevcutYon != Yon.Asagi) mevcutYon = Yon.Yukari;
            if (tus == ConsoleKey.DownArrow && mevcutYon != Yon.Yukari) mevcutYon = Yon.Asagi;
            if (tus == ConsoleKey.LeftArrow && mevcutYon != Yon.Sag) mevcutYon = Yon.Sol;
            if (tus == ConsoleKey.RightArrow && mevcutYon != Yon.Sol) mevcutYon = Yon.Sag;
        }
    }

    static void OyunMantigi()
    {
        var bas = yilanGovde[0];
        (int x, int y) yeniBas = bas;

        switch (mevcutYon)
        {
            case Yon.Yukari: yeniBas.y--; break;
            case Yon.Asagi: yeniBas.y++; break;
            case Yon.Sol: yeniBas.x--; break;
            case Yon.Sag: yeniBas.x++; break;
        }

        // Duvara çarpma
        if (yeniBas.x <= 0 || yeniBas.x >= alanGenislik - 1 || yeniBas.y <= 0 || yeniBas.y >= alanYukseklik - 1)
        {
            oyunBitti = true;
            return;
        }

        // Kendine çarpma
        if (yilanGovde.Contains(yeniBas))
        {
            oyunBitti = true;
            return;
        }

        yilanGovde.Insert(0, yeniBas);

        // Yem yeme
        if (yeniBas == yem)
        {
            puan += 10;
            YeniYemUret();
        }
        else
        {
            yilanGovde.RemoveAt(yilanGovde.Count - 1); // Kuyruğu kaldır
        }
    }

    static void YeniYemUret()
    {
        Random rastgele = new Random();
        do
        {
            yem.x = rastgele.Next(1, alanGenislik - 2);
            yem.y = rastgele.Next(1, alanYukseklik - 2);
        } while (yilanGovde.Contains(yem));
    }
}

       
