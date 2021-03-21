---
hidden: true
title: "Tehdään WPF-sovellus - 2 - Sovelluksen runko"
image: assets/images/tehdaan-wpf-sovellus/ketuttaako-header.jpg
layout: post
date: 2021-03-20 11:00
tag:
- WPF
category: blog
author: anssikettunen
description: Opas kuinka tehdä Windows-työpöytäsovellus käyttäen WPF, XAML ja C#.
---

## Luodaan uusi projekti

Visual Studion käynnistyttyä sinua tervehtii ikkuna, jolla voit valita sovelluksen projektille tyypin. Luodaan uusi `WPF(.NET)`-työpöytäsovellus. Annetaan projektille nimeksi _Sukunimi.BMICalculator.WPFClient_ ja solutionille vastaavasti _Sukunimi.BMICalculator_ (esim. _Kettunen.BMICalculator_) tähän tapaan:

1. Valitse `Create a new project`
2. Syötä hakukenttään "WPF"
3. Valitse tuloksista `WPF App (.NET)` ja paina `Next`
4. Anna uudelle projektille ja solutionille nimet:
    * Project: Sukunimi.BMICalculator.WPFClient (esim. _Kettunen.BMICalculator.WPFClient_)
    * Solution: Sukunimi.BMICalculator
5. Voit valita projektille haluamasi kansion, mutta oletusarvo tässä kohtaa on ihan ok
6. Paina `Create`

Kun projekti on luotu näet edessäsi XAML:lla kirjoitetun sovelluksen pääikkunan määrittelytiedoston `MainWindow.xaml`:


```xml
<Window x:Class="Kettunen.BMICalculator.WpfClient.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:local="clr-namespace:Kettunen.BMICalculator.WpfClient"
        mc:Ignorable="d"
        Title="MainWindow" Height="450" Width="800">
    <Grid>
        
    </Grid>
</Window>
```
<figcaption>MainWindow.xaml</figcaption>

Tässä on paljon määrittelyä, mutta suurin osa on XAML:n "boilerplatea", eli koodia, jota voidaan käyttää useassa sovelluksessa tekemättä juurikaan suuria muutoksia.

Toinen automaattisesti omaan välilehteensä avautuva tiedosto on XAML-sivun taustakoodi (code-behind) `MainWindow.xaml.cs`:

```csharp
namespace Kettunen.BMICalculator.WpfClient
{
    /// <summary>
    /// Interaction logic for MainWindow.xaml
    /// </summary>
    public partial class MainWindow : Window
    {
        public MainWindow()
        {
            InitializeComponent();
        }
    }
}
```
<figcaption>MainWindow.xaml.cs</figcaption>

Taustatiedostoon voimme kirjoittaa näkymälle ominaisia toiminnallisuuksia, joita ei välttämättä XAML-syntaksilla saada toteutettua helposti. Periaatteessa tänne _ei olisi tarvetta_ kirjoittaa koskaan juuri mitään, mutta teemme poikkeuksen yksinkertaisuuden nimissä. Palaamme tähän tiedostoon myöhemmin, mutta keskitytään nyt aluksi XAML-määrittelytiedostoon `MainWindow.xaml`.

{% include note_start.html content='WPF-sovelluksen käyttöliittymä koostuu _elementeistä_:
* WPF-käyttöliittymät kirjoitetaan [XAML-kielellä](https://docs.microsoft.com/en-us/dotnet/desktop/wpf/fundamentals/xaml).
* `<Window>`-elementti kertoo, että tämä on ikkuna.
* Elementit voivat pitää sisällään yhden tai useamman elementin.
* Oletuksena pääikkunan sisällä on ruudukko `Grid`, jonka sisälle voidaan lisätä lisää elementtejä.
* Elementit tulee avata näin `<Grid>` ja sulkea näin `</Grid>`.
    * Tietyissä tilanteissa elementti voi sulkea itse itsensä: `<Grid />`.
* Elementtejä voi määritellä tarkemmin antamalla niille arvoja atribuuttien avulla
    * Esim. `<Grid Width="50">` asettaa ruudukon leveydeksi 50.' %}
{% include note_end.html %}

## Ensimmäinen sovelluksemme

Annetaan tilaa vielä hieman perinteiden kunnioittamiselle ja lämmitellään koodinystyröitämme ennen itse sovelluksen luomista lisäämällä väliaikaisesti legendaarinen ["Hello, World!"-teksti](https://en.wikipedia.org/wiki/%22Hello,_World!%22_program) näkyville. Lisätään `TextBlock`-elementti:


```diff
    <Grid>
+        <TextBlock Text="Hello, World!" />
    </Grid>
```
<figcaption>MainWindow.xaml</figcaption>

Nyt olet valmis ihailemaan uutta silmiä hivelevää sovellustasi. Käännetään ja käynnistetään sovellus.

* Valitse ylhäältä Debug-valikosta `Start Debugging`, tai paina `F5` näppäimistöltä 

Hieno homma ja aivan mieletöntä! Ensimmäinen sovelluksesi on valmi.. hetkinen. Tämä on vasta sovelluksen runko. Sulje ohjelma vielä toistaiseksi. Nyt on aika pistää kädet saveen!

ps. Voit poistaa lisäämämme väliaikaisen `TextBlock`-elementin tässä vaiheessa.


```diff
    <Grid>
-        <TextBlock Text="Hello, World!" />
    </Grid>
```
<figcaption>MainWindow.xaml</figcaption>

Aloitetaan varsinaisen sovelluksen käyttöliittymän tekeminen seuraavaksi.