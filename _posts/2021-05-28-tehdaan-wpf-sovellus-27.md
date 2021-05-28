---
title: "Tehdään WPF-sovellus - 27 - Komennon suoritettavuus"
image: assets/images/tehdaan-wpf-sovellus/ketuttaako-header.jpg
layout: post
date: 2021-05-28 19:00
tag:
- WPF
category: blog
author: anssikettunen
description: Opas kuinka tehdä Windows-työpöytäsovellus käyttäen WPF, XAML ja C#. Tässä osassa muutetaan sovellus mukailemaan MVVM-tyyliä.
---

## Komennon suorituksen estäminen CanExecutella

`Navigate`-komentomme suostuu suorittamaan toimintonsa oli painoksi ja pituudeksi syötetty mitä vain.
Estetään ainakin tyhjillä arvoilla eteneminen laskentaan. Tämä onnistuu helpoiten muuttamalla nykyistä `canExecute`-funktiota.

`_ => true` kertoo, että komento voidaan suorittaa aina. Muutetaan discard (`_`) oikeaksi muuttujaksi `x` ja tarkastellaan painon ja pituuden kenttiin syötettyjä arvoja:

```csharp
x =>
{
    if (x is InputViewModel input)
    {
        return input.Height > 0 && input.Weight > 0;
    }
    else
    {
        return true;
    }
});
```
<figcaption>MainViewModel.cs</figcaption>

`x` kuvaa tässä tapauksessa komennon saamaa parametria.

Kun olemme syötekenttien näkymässä ja pituus ja paino ovat enemmän kuin 0, niin voimme antaa laskentapainiketta painamalla käyttäjän edetä seuraavaan näkymään.

`else`-kohdassa voimme palauttaa `true`, koska tällöin olemme tarkastelemassa tuloksia ja haluamme aina voida palata takaisin `BACK`-painikkeella.

Painike pysyy kuitenkin muutostemme jälkeen aina disabloituna. Miksi?

## UpdateSourceTrigger määrittää milloin arvo päivitetään

Emme ole bindanneet `InputView`:n `HeightInput`- ja `WeightInput`-kenttiä ViewModeliinsa. Tehdään se seuraavaksi:

```xml
<TextBox Name="HeightInput"
        ...
        Text="{Binding Height, UpdateSourceTrigger=PropertyChanged}" />
```
<figcaption>InputView.xaml</figcaption>

Tee sama `WeightInput`-kentälle.

Asetamme bindingille myös `UpdateSourceTrigger=PropertyChanged`, koska oletuksena `Text`:lle asetettu bindaus päivittäisi ViewModelin arvon vasta, kun se menettää fokuksensa.
Voit kokeilla tätä ensin määrittämättä `UpdateSourceTriggeriä`. Käytännössä siis ilman tätä määritystä painike ei aktivoidu heti, kun molemmat arvot on annettu, vaan sitten vasta, kun fokuksen klikkaa toisesta syötekentästä toiseen.

Nyt kun kokeilet syöttää arvot, niin sovellus päästää siirtymään tarkastelemaan tuloksia (vielä "0") ja palaamaan takaisin.

Sovellus myös muistaa aiemmin annetut arvot, joilloin ei tarvitse muuttaa esim. pituutta. Jes!

Seuraavassa osassa lisätään laskuri oman luokkansa kautta ja parannetaan tulosten näkymää vielä hieman.