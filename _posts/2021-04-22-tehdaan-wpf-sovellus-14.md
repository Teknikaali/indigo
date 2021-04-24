---
title: "Tehdään WPF-sovellus - 14 - Elementit järjestykseen"
image: assets/images/tehdaan-wpf-sovellus/ketuttaako-header.jpg
layout: post
date: 2021-04-22 19:00
tag:
- WPF
category: blog
author: anssikettunen
description: Opas kuinka tehdä Windows-työpöytäsovellus käyttäen WPF, XAML ja C#. Tässä osassa parannetaan elementtien asettelua.
---

## Ruudukkojen koot ovat määriteltävissä suhteellisesti

`Grid`-elementti on siitä kätevä kaveri, että sen asettelua on helppo hallita vaikka sovelluksen ikkunan kokokin muuttuisi. Hyvänä nyrkkisääntönä on hyvä välttää kovakoodattuja pikseliarvoja elementtien kokojen suhteen. `RowDefinition` ja `ColumnDefinition` sallivat `Width`- ja `Height`-arvoihinsa raa'an luvun (esim. `32`) lisäksi myös suhteelliset arvot `Auto` ja `*`, joita tulemmekin jatkossa käyttämään hyödyksi. [Tässä](https://www.wpf-tutorial.com/panels/grid-rows-and-columns/) tiivistetty katsaus aiheesta.

{% include note.html content='Jos kaksi eri riviä ovat Height-arvoiltaan "*" ja "2*", niin tämä tarkoittaa sitä, että ensimmäinen rivi vie tilaa 1/3 käytettävissä olevasta tilasta ja toinen rivi 2/3. "Auto" kertoo riville, että sen tulisi käyttää vain niin paljon tilaa kuin sen sisällä olevat elementit siltä vaativat. ' %}

Seuraavassa kuvassa on näkyvissä tavoittelemamme lopputulos kun ruudukkojen koot on määritelty:

![][1]{: .center-image }
<figcaption class="caption">Tavoittelemamme ruudukon määrittely</figcaption>

## Pääruudukon korkeuksien suhteiden määrittäminen

Aloitetaan pohjalta, josta voimme sitten työstää tiemme pienempien/sisempien elementtien määrittelyyn. Lisätään siis pääruudukolle uudet `Height`-arvot. Edellisen kuvan perusteella pitäisi riittää kunhan lisäämme kahdelle ensimmäiselle riville arvot `2*`:

```xml
<Grid>
    <Grid.RowDefinitions>
        <RowDefinition Height="2*" />
        <RowDefinition Height="2*" />
        <RowDefinition />
    </Grid.RowDefinitions>
```
<figcaption>MainWindow.xaml - Height-arvot lisätty pääruudukolle.</figcaption>

![][2]{: .center-image }
<figcaption class="caption">Pääruudukon korkeudet määritetty</figcaption>

No mutta sehän oli mukavaa!

## Sisemmät elementit kuntoon

Samoilla vauhdeilla voimme korjata painon ja pituuden antamalla niiden ruudukoille korkeudeksi `Auto`.
`Auto` siksi, että haluamme näiden rivien vievän vain niin paljon tilaa kuin niiden sisältämät elementit vaativat. Asetetaan myös ruudukon pystysuuntainen tasaus keskitetyksi, jotta saamme viimeistellyn lopputuloksen. Muutetaan myös tekstit kokonaan isoilla kirjaimilla kirjoitetuiksi.

{% include note.html content='Annoin painon ja pituuden ruudukoille myös kommentit "Weight" ja "Height", jotta tiedostossa olisi helpompi navigoida nopeasti sitä silmäillessä.' %}

```xml
<!--  Weight  -->
<Grid VerticalAlignment="Center">
    <Grid.RowDefinitions>
        <RowDefinition Height="Auto" />
        <RowDefinition Height="Auto" />
    </Grid.RowDefinitions>
    <TextBlock Text="WEIGHT" />
    <TextBox Name="WeightInput"
                Grid.Row="1"
                d:Text="123" />
</Grid>

<!--  Height  -->
<Grid Grid.Row="1"
        VerticalAlignment="Center">
    <Grid.RowDefinitions>
        <RowDefinition Height="Auto" />
        <RowDefinition Height="Auto" />
    </Grid.RowDefinitions>
    <TextBlock Text="HEIGHT" />
    <TextBox Name="HeightInput"
                Grid.Row="1"
                d:Text="456" />
</Grid>
```
<figcaption>MainWindow.xaml - Height-arvot lisätty painon ja pituuden ruudukoille.</figcaption>

{% include note.html content='Yksi mainitsemisen arvoinen liitännäinen Visual Studioon on [XAML Styler](https://marketplace.visualstudio.com/items?itemName=TeamXavalon.XAMLStyler). Yksi hyvä ominaisuus XAML Stylerissä on automaattisen tyylittelyn konfiguraation tallentaminen tiedostoon. Tämän tiedoston voi jakaa muiden tiimiläisten kanssa yhtenevän kirjoitustyylin säilyttämiseksi 👍 Suosittelen käymään keskustelun tyylieroista lämpimän juoman ja hyvän naposteltavan kera. Jokaisella on oma mielipiteensä välilyöntien/tabien lukumääristä ja miten elementit ja niiden attribuutit tulisi sijoitella tiedostossa 🙈' %}

Lopputuloksen pitäisi näyttää tältä:

![][3]{: .center-image }
<figcaption class="caption">Painon ja pituuden ruudukkojen koot määritetty</figcaption>

Seuraavassa osassa lisätään yksiköt takaisin paikoilleen.

[1]: /assets/images/tehdaan-wpf-sovellus/14-01.jpg
[2]: /assets/images/tehdaan-wpf-sovellus/14-02.jpg
[3]: /assets/images/tehdaan-wpf-sovellus/14-03.jpg