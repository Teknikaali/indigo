---
title: "Tehdään WPF-sovellus - 12 - Tekstilaatikko nätiksi"
image: assets/images/tehdaan-wpf-sovellus/ketuttaako-header.jpg
layout: post
date: 2021-04-04 22:00
tag:
- WPF
category: blog
author: anssikettunen
description: Opas kuinka tehdä Windows-työpöytäsovellus käyttäen WPF, XAML ja C#. Tässä osassa lisätään uudet tyylit eri tyyppisille elementeille.
---

## Tyylitellään TextBlock

`TextBlock` on yksinkertaisempi elementti tyylitellä ja opimme edellisessä osassa jo kaiken mitä tarvitsemme tämän elementin tyylittelyyn.

Meidän ei tarvitsee edes muuttaa taustaa läpinäkyväksi, koska `TextBlock` on jo läpinäkyvä valmiiksi. Voimme siis keskittyä vain asettamaan fontin, tekstin värin ja tekstin asemoinnin keskelle:

```xml
<Style TargetType="TextBlock">
        <Setter Property="FontFamily" Value="Calibri" />
        <Setter Property="FontSize" Value="24" />
        <Setter Property="Foreground" Value="#868892" />
        <Setter Property="TextAlignment" Value="Center" />
</Style>
```
<figcaption>App.xaml - TextBlock-tyyli.</figcaption>

{% include note.html content="Tässä vaiheessa ei kannata huolehtia useammassa eri tyylissä määritetyistä samoista väri- tai fonttiarvoista. Voimme katsoa lopuksi, että voisimmeko yhdistää jotain tyylien ominaisuuksia duplikaattikoodin poistamiseksi." %}

Poistetaan tässä vaiheessa myös painon ja pituuden tekstilaatikoihin kirjoitetut yksiköt, koska tulemme siirtämään ne myöhemmin syötekentän vierelle omana `TextBlock`-elementtinään:

```diff
-<TextBlock Text="Weight (kg)" />
+<TextBlock Text="Weight" />
 ...
-<TextBlock Text="Height (cm)" />
+<TextBlock Text="Height" />
```
<figcaption>MainWindow.xaml - Yksikköjen poistaminen.</figcaption>

![TextBlockin tyylittelyn jälkeen.][1]{: .center-image }
<figcaption class="caption">TextBlock tyylittelyn jälkeen.</figcaption>

Painike voisi jäädä ennalleen, mutta koska olemme jo tyylittelyn aloittaneet, niin viekäämme tyylittely loppuun asti jokaisen elementin kohdalla.

Saatat huomata, että tekstilaatikoiden ja syötekenttien sijoittelu suhteessa toisiinsa on hieman ilmavaa. Katsotaan sijoittelu kuntoon myöhemmin, jotta voisimme pysyä samassa kontekstissa (tyylitettelyssä) mahdollisimman pitkään.

Seuraavassa osassa tartumme tyylittelykynsinemme `Button`-elementin kimppuun!

[1]: /assets/images/tehdaan-wpf-sovellus/12-01.jpg