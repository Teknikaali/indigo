---
title: "Tehdään WPF-sovellus - 28 - Laskuri laskemaan"
image: assets/images/tehdaan-wpf-sovellus/ketuttaako-header.jpg
layout: post
date: 2021-05-28 19:30
tag:
- WPF
category: blog
author: anssikettunen
description: Opas kuinka tehdä Windows-työpöytäsovellus käyttäen WPF, XAML ja C#. Tässä osassa muutetaan sovellus mukailemaan MVVM-tyyliä.
---

## Laskuri omassa luokassaan

Aiemmin laskimme painoindeksin arvon yksinkertaisesti näkymän taustatiedostossa (`xaml.cs`), mutta voimme tehdä tämän nyt ViewModelin puolella.

Lisätään aluksi laskurille oma luokka `Calculator.cs`:

```csharp
using System;

namespace Kettunen.BMICalculator.WPFClient
{
    /// <summary>
    /// Provides a method to calculate a body mass index (BMI) value for a person.
    /// </summary>
    public class Calculator
    {
        /// <summary>
        /// Calculates body mass index (BMI) derived from mass (weight) and height of a person.
        /// </summary>
        /// <param name="weight">Weight in kilograms</param>
        /// <param name="height">Height in meters</param>
        /// <returns>Body mass index value in units of kg/m²</returns>
        public double Calculate(double weight, double height) => weight / Math.Pow(height, 2);
    }
}
```
<figcaption>Calculator.cs</figcaption>

{% include note.html content='Voit lisätä vastaavat xml-tyyppiset selitteet näpäyttämällä kolme kertaa `/`-merkkiä funktion yläpuolella. Visual Studio generoi automaattisesti pohjan funktion, luokan tai propertyn selitteelle. Kun hoveroit kursorilla `Calculate`-sanaa funktiossa, niin Visual Studio näyttää kätevästi tooltipissä kirjoittamasi kommentin. Tämä on hyvä tapa dokumentoida mitä funktion tai propertyn on tarkoitus tehdä.' %}

Otetaan laskuri käyttöön `MainViewModelilla` ja lasketaan tulos juuri ennen kuin siirrytään tulossivulle:

```csharp
    public class MainViewModel : ViewModel
    {
        private readonly Calculator _calculator;

        // ...

        public MainViewModel()
        {
            _calculator = new Calculator();

            // ...
            
            Navigate = new DelegateCommand(x =>
            {
                if (x is InputViewModel input)
                {
                    _resultViewModel.Result = _calculator.Calculate(input.Weight, input.Height / 100);
                    CurrentViewModel = _resultViewModel;

                    // ...
```
<figcaption>MainViewModel.cs</figcaption>

{% include note.html content='Huomioi, että teemme cm -> m -konversion jakamalla `input.Height`-arvon 100:lla ennen kuin syötämme sen laskurille.' %}

Hienoa, nyt meillä on taas täysin toimiv.. Ei. Eikö? No ei 🙈

## Tekstiä voi muotoilla StringFormatilla

Jos annat sovellukselle painoksi "123", pituudeksi "456" ja suoritat laskennan, niin tulokseksi tulee turhan pitkä `5.915281..`.
Voisimme esittää tuloksen maksimissaan kahdella desimaalilla.

Korjataan ongelma tulosnäkymän xaml-puolella asettamalla tuloksen bindaukselle [`StringFormat`](https://docs.microsoft.com/en-us/dotnet/api/system.windows.data.bindingbase.stringformat)-muotoilun seuraavasti:

```xml
<TextBlock Grid.Row="1"
            Text="{Binding Result, StringFormat=0.##}" />
```
<figcaption>ResultView.xaml</figcaption>

Nyt sovelluksemme pitäisi olla aika hyvässä mallissa:

![][1]{: .center-image }
<figcaption class="caption">Sehän toimii!</figcaption>

## Välivaihe

Olemme saaneet tähän asti paljon aikaiseksi. Voit onnitella itseäsi, jos olet päässyt tänne asti ja olet mahdollisesti saanut oman version sovelluksesta toimimaan! 🦊

Nyt voit ottaa pienen hengähdystauon. Sovelluksellemme voidaan tehdä vielä paljonkin asioita, mutta tässäpä muutama, jotka nousevat mieleeni:

* Sovelluksen tasa-arvoistaminen
    * Lisätään valintapainike sukupuolelle
* Sovelluksen muuttaminen testattavammaksi
    * Kirjoitetaan esimmäinen testimme

Eiköhän tässä riitä taas jatkoon hieman purtavaa!

[1]: /assets/images/tehdaan-wpf-sovellus/28-01.gif