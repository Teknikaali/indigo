---
title: "Tehdään WPF-sovellus - 10 - Uusi tyyli"
image: assets/images/tehdaan-wpf-sovellus/ketuttaako-header.jpg
layout: post
date: 2021-04-04 20:00
tag:
- WPF
category: blog
author: anssikettunen
description: Opas kuinka tehdä Windows-työpöytäsovellus käyttäen WPF, XAML ja C#. Tässä osassa pohjustetaan paikka tyyleille.
---

## Tyylit keventävät ylläpitotaakkaa

Aiemmin määrittelimme värit yksitellen jokaiselle elementille. Jos (ja kun) jatkossa haluaisimme vaihtaa elementtien värejä, tai ulkoasua yleisesti, niin joutuisimme muuttamaan jokaisen elementin väriarvot yksitellen. Tämä olisi turhankin työlästä pidemmän päälle, joten varaudutaan jatkon design-muutoksille käyttämällä tyylejä avuksemme!

{% include note.html content="XAML:n tyylittely on vähän sama asia kuin mitä CSS on HTML:lle." %}

## Tyylien määrittäminen

Olemme aiemmin määritelleet suoraan pituuden syöttökenttämme elementille haluavamme sen näyttävän maissisilkkisen suklaiselta:

```xml
<TextBox Name="HeightInput"
         Grid.Row="1"
         Background="Cornsilk"
         Foreground="Chocolate" />
```

Voimme esittää saman käyttämällä lokaalia tyyliä, eli antamalla `TextBox.Style`:lle `<Style>`-elementin:

```xml
<TextBox Name="HeightInput"
         Grid.Row="1">
    <TextBox.Style>
        <Style TargetType="TextBox">
            <Setter Property="Background" Value="Cornsilk"/>
            <Setter Property="Foreground" Value="Chocolate"/>
        </Style>
    </TextBox.Style>
</TextBox>
```

<figcaption>Tyyli on määritetty lokaalisti kontrollille</figcaption>

{% include note.html content="TargetType kertoo, että tätä tyyliä käytetään kaikille annetun tyypin kontrolleille tyylin _scopessa_." %}

## Lokaalista yleisemmäksi

Tyyli on nyt määritelty _lokaalisti_, eli se ei pääse vaikuttamaan toisiin `TextBox`-kontrolleihin, kuten oikeasti haluaisimme.

Ratkaistaan ongelma siirtämällä tyyli pois `TextBox`:n sisäisestä scopesta koko ikkunan scopeen. Tällöin kaikki ikkunalla olevat `TextBox`-kontrollit tyylitellään samalla tyylillä ja meiltä säästyy ylläpitotyötä, nice! 👍

Tyyli tulee lisätä ylemmän elementin `Resources`-kokoelmaan, jotta sen vaikutukset "valuisivat" myös alempiin elementteihin:

```xml
<Window>
    <Window.Resources>
        <Style TargetType="TextBox">
            <Setter Property="Background" Value="Cornsilk" />
            <Setter Property="Foreground" Value="Chocolate" />
        </Style>
    </Window.Resources>
```
<figcaption>Tyyli on määritetty ikkunalle.</figcaption>

Tekstilaatikkomme näyttää nyt tältä yksinkertaisuudessaan:

```xml
<TextBox Name="HeightInput"
         Grid.Row="1" />
```
<figcaption>Tekstilaatikon värejä ei määritellä enää erikseen.</figcaption>


Mutta mutta.. Kaikki näyttää samalta kuin ennenkin. Miksei ylempi laatikko värjääntynyt myös suklaiseksi?

![Mikään ei ole muuttunut][1]{: .center-image }
<figcaption class="caption">Mikään ei ole muuttunut.</figcaption>

## Yleisemmästä yleisemmäksi

Tämä on erittäin hyvä esimerkki siitä miten tyylejä voi _ylikirjoittaa_ tarvittaessa. Tyylin arvojen ylikirjoittaminen on kätevä ominaisuus, jos haluaa tehdä esimerkiksi jostain yksittäisestä elementistä jollain tavalla hieman muista vastaavista elementeistä eroavan.

Poistetaan kuitenkin tässä vaiheessa myös ylemmän laatikon värimäärittelyt, jotta saisimme sovelluksemme näyttämään yhtenäisemmältä ja olisimme askeleen lähempänä totuutta:

```diff
 <TextBox Name="WeightInput"
-         Grid.Row="1"
+         Grid.Row="1" />
-         Background="MintCream"
-         Foreground="DarkSalmon" />
```
<figcaption>MainWindow.xaml - värien poistaminen elementiltä.</figcaption>

Nyt ylempi ja alempi tekstilaatikko näyttävät samoilta, mahtavaa!

![Tekstilaatikot][2]{: .center-image }
<figcaption class="caption">Tekstilaatikot.</figcaption>

## Näkymiä tulevaisuudesta

Näen tulevaisuuteen ja siellä näyttää siltä, että `MainWindow.xaml`-tiedoston koko tulee hieman kasvamaan nykyisestä. Tehdään tässä vaiheessa siis vielä pieni ähkyä ehkäisevä liike:

Siirretään tyylimäärittelyt ikkunalta _sovelluksen laajuisiksi tyyleiksi_.

{% include note.html content="Sovelluksen laajuinen tyyli vaikuttaa koko sovellukseen ikkunasta riippumatta. Sovelluksella voi olla siis useita `Window`-tyyppisiä ikkunoita, jotka kaikki käyttävät samoja sovellukselle määritettyjä tyylejä ilman sen erityisempää lisätyötä." %}

Sovelluskohtaiset tyylit määritellään `App.xaml`-tiedostossa. Poistetaan siis `MainWindow.xaml`:n `Window.Resources`-elementti ja siirretään tyyli `App.xaml`-tiedostoon.

```diff
-<Window.Resources>
-    <Style TargetType="TextBox">
-        <Setter Property="Background" Value="Cornsilk" />
-        <Setter Property="Foreground" Value="Chocolate" />
-    </Style>
-</Window.Resources>
```
<figcaption>MainWindow.xaml - tyylin siirtäminen App.xaml-tiedostoon.</figcaption>

{% include note.html content='Solution Explorer -ikkunan avulla on helppo navigoida projektin tiedostojen välillä. Tilanteesta riippuen `Ctrl+Shift+T`- tai `Ctrl+T`-näppäinyhdistelmien takana oleva "Go to .."-toiminnallisuus voi olla ihan näppärä vaihtoehto myös. Itse käytän kyseisiä pikanäppäimiä usein 👍' %}

```diff
 <Application.Resources>
+    <Style TargetType="TextBox">
+        <Setter Property="Background" Value="Cornsilk" />
+        <Setter Property="Foreground" Value="Chocolate" />
+    </Style>
 </Application.Resources>
```
<figcaption>App.xaml - siirretty tyyli.</figcaption>

Nyt meillä on selkeä pohja, jonka päälle voimme soveltaa aiemmin esittelemääni designia. Seuraavassa osassa tyylitellään `TextBox`, `TextBlock` ja `Button`.

[1]: /assets/images/tehdaan-wpf-sovellus/10-01.jpg
[2]: /assets/images/tehdaan-wpf-sovellus/10-02.jpg