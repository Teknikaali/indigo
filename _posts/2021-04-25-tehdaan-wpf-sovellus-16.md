---
title: "Tehdään WPF-sovellus - 16 - Pyörennetään kulmia"
image: assets/images/tehdaan-wpf-sovellus/ketuttaako-header.jpg
layout: post
date: 2021-04-25 12:00
tag:
- WPF
category: blog
author: anssikettunen
description: Opas kuinka tehdä Windows-työpöytäsovellus käyttäen WPF, XAML ja C#. Tässä osassa parannetaan sovelluksen ulkoasua.
---

## Elementtien kulmien pyöristys

Elementeillä ei ole oletuksena muuttujaa, jolla kulmien pyöreyteen pystyisi vaikuttamaan. Meidän täytyy siis käyttää toista elementtiä saadaksemme kauniit pyöreät kulmat aikaiseksi.

Tähän tarkoitukseen soveltuu [`Border`-elementti](https://www.wpf-tutorial.com/misc-controls/the-border-control/).

Kääritään painon ja pituuden `Grid`-elementit Bordereilla `MainWindow.xaml`:n puolella ja annetaan Borderille myös oma tyyli `App.xaml`-tiedostossa.

{% include note.html content='Muista siirtää Grid.Row-määritys pituuden pää-Gridiltä uudelle Borderille.' %}

```xml
<!--  Weight  -->
<Border>
    <Grid VerticalAlignment="Center">
        ...
    </Grid>
</Border>

<!--  HEIGHT  -->
<Border Grid.Row="1">
    <Grid VerticalAlignment="Center">
        ...
    </Grid>
</Border>
```
<figcaption>MainWindow.xaml - Border-elementit lisätty painolle ja pituudelle.</figcaption>

Tekemillämme muutoksilla ei ole vielä mitään vaikutusta sovelluksen ulkoasuun, koska Borderit ovat oletuksena vain näkymättömiä elementtejä. Meidän tulee lisätä `Border`-elementille yleinen tyyli `App.xaml`-tiedoston puolelle muiden tyylien joukkoon.
`CornerRadius` määrittää kulmien pyöreyden ja `Background` taustan värin.

{% include note.html content='Voit myös antaa CornerRadiukselle arvon neljällä luvulla. Tällöin pystyt määrittämään tarkasti jokaiselle kulmalle pyöristysmäärän erikseen. Esim. Value="8,8,3,3".' %}

```xml
<Style TargetType="Border">
    <Setter Property="Background" Value="#111428" />
    <Setter Property="CornerRadius" Value="6" />
</Style>
```
<figcaption>App.xaml - Border-elementit tyyli.</figcaption>

Voimme havaita, että eihän tämä nyt aivan suorilta käsin toiminutkaan. Myös painikkeen ja muiden elementtien kulmat pyöristyivät virheellisesti. Haluaisimme vain erikseen määriteltyjen `Border`-elementtien kulmien pyöristyvän:

![][1]{: .center-image }
<figcaption class="caption">Virheellisiä pyöristyksiä</figcaption>

Tässä vaiheessa voimme käydä taas vilkaisemassa miltä elementtien rakenne näyttää Live Visual Treen avulla. Toisaalta voimme olla aika varmoja, että taustalla on aivan sama syy kuin painikkeen värinkin kanssa: kontrollit käyttävät sisäisesti `Border`-elementtiä, jonka tyyliin olemme nyt kajonneet. Rajoitetaan siis muutoksemme ainoastaan lisäämiimme `Border`-elementteihin määrittelemällä näille tyylit suoraan.

Annetaan tyylille `x:Key`-attribuutilla nimi, johon voimme sitten viitata toisaalla:

```diff
-<Style TargetType="Border">
+<Style x:Key="ItemBorder"
+       TargetType="Border">
    <Setter Property="Background" Value="#111428" />
    <Setter Property="CornerRadius" Value="6" />
</Style>
```
<figcaption>App.xaml - Lisätään Border-tyylille eksplisiittinen nimi.</figcaption>

`MainWindow.xaml`-puolella voimme viitata tyyliin antamalla elementin `Style`-attribuutille viittauksen juuri lisäämäämme nimeen `StaticResource`-tyyppisellä bindauksella:

{% include note.html content='En ole aiemmin puhunut paljon mitään WPF:n bindauksista, vaikka ne ovatkin erittäin tärkeä osa WPF:llä tekemistä. Palaamme näihin myöhemmin. Jos haluat jo tässä vaiheessa tietää enemmän, niin [tästä](https://www.wpf-tutorial.com/data-binding/introduction/) on hyvä aloittaa.' %}

```xml
<!--  WEIGHT  -->
<Border Style="{StaticResource ItemBorder}">
```
<figcaption>App.xaml - Border-tyyliin nimellä viittaaminen.</figcaption>

![][2]{: .center-image }
<figcaption class="caption">Vain painolle määritetty Border-tyyli</figcaption>

Lisätään samoilla vauhdeilla sopivaa väljyyttä painon ja pituuden osuuksille asettamalla niiden `Border`-elementtien `Margin`-arvot:

{% include note.html content='Margin- ja Padding-arvoja saattaa joutua säätämään useampaan otteeseen, jotta pääsisi haluamansa lopputuloksen. Jos tapauksessamme käyttäisimme vain määritystä Margin="24", tulisi painon ja pituuden Borderien välille liian suuri väli. Tämän takia määrittelemme erikseen ensimmäisen Borderin Bottom-arvoon 12 ja toisen Borderin Top-arvoon vastaavasti 12 muiden reunojen arvojen ollessa 24.' %}

```xml
<!--  Weight  -->
<Border Margin="24,24,24,12"
        Style="{StaticResource ItemBorder}">
    ...
</Border>

<!--  Height  -->
<Border Grid.Row="1"
        Margin="24,12,24,24"
        Style="{StaticResource ItemBorder}">
    ...
</Border>
```
<figcaption>MainWindow.xaml - Marginien asettaminen Bordereille.</figcaption>

![][3]{: .center-image }
<figcaption class="caption">Tyylitelty sovellus on tyylitelty</figcaption>

Vau, hieno homma! Nyt olemme saaneet sovelluksemme valm.. Eipäs hoppuilla.

Tekemämme tyylittely on toisaalta edistystä, mutta toisaalta taas ei. Niin hullulta kuin se kuulostaakin, niin olemme nyt ottaneet yhden askeleen eteenpäin ja yhden taakse. Mikä saa minut sanomaan näin?

## Hover-efekti parantaa käytettävyyttä

Jos käynnistät ohjelman ja koitat syöttää arvoja, niin ohjelma ei mitenkään avita käyttäjäänsä "kohdistamaan" toimiaan arvojen syötekenttiin. Aiemmin kenttien ympärillä oli selkeät rajat, jotka ehkä hieman ohjasivat käyttäjää oikealle raiteelle.

Voisimme ratkaista ongelman muutamallakin eri tavalla:
1. Lisäämällä Hover-efektin, kun kursori on elementin päällä
2. Lisäämällä pienen tietolaatikon (`ToolTip`), kun kursori on elementin päällä
3. Lisäämällä jonkinlaiset reunat elementille

Valitaan näistä yllättäen sekä ensimmäinen että toinen vaihtoehto. Kolmas rikkoisi sovelluksemme tyylin, joten yritetään selvitä ilman sitä.

Tehdään nämä lisäykset seuraavassa osassa.

[1]: /assets/images/tehdaan-wpf-sovellus/16-01.jpg
[2]: /assets/images/tehdaan-wpf-sovellus/16-02.jpg
[3]: /assets/images/tehdaan-wpf-sovellus/16-03.jpg