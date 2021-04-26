---
title: "Tehdään WPF-sovellus - 17 - Selkeytetään syötekenttiä"
image: assets/images/tehdaan-wpf-sovellus/ketuttaako-header.jpg
layout: post
date: 2021-04-26 19:00
tag:
- WPF
category: blog
author: anssikettunen
description: Opas kuinka tehdä Windows-työpöytäsovellus käyttäen WPF, XAML ja C#. Tässä osassa parannetaan syötekenttien näkyvyyttä.
---

## Kursorilla elementtien korostaminen

Voisimme ratkaista tekstinsyöttökenttien käytettävyysongelman muutamallakin eri tavalla:
1. Lisäämällä Hover-efektin, kun kursori on elementin päällä
2. Lisäämällä pienen tietolaatikon (`ToolTip`), kun kursori on elementin päällä
3. Lisäämällä jonkinlaiset reunat elementille

Valitaan näistä ensimmäinen vaihtoehto, koska toinen toisi vain kahdennettua tietoa käyttäjälle ja kolmas rikkoisi sovelluksemme tyylin.

## Hover-efektin lisääminen triggerillä

Käytetään triggeriä hover-efektin tuottamiseksi. Lisäsimme jo aiemmin vastaavan painikkeelle, joten tämä on suoraviivaista tehdä:

```xml
<Style TargetType="TextBox">
    ...
    <Style.Triggers>
        <Trigger Property="IsMouseOver" Value="True">
            <Setter Property="Background" Value="#31142F" />
        </Trigger>
    </Style.Triggers>
</Style>
```
<figcaption>App.xaml - Triggerin lisäys TextBox-tyylille.</figcaption>

## Tyylittely on valmis ja "loppusanat"

Hienoa! Nyt sovelluksemme näyttää nätiltä ja toimii halutulla tavalla 🎉

Olemme matkan aikana tarkentaneet sovelluksen määrittelyä, parantaneet sen ulkoasua. Toivottavasti olet myös oppinut jotain käyttöliittymän tekemisestä WPF:llä!

Mainitsen tähän "loppuun" vielä tärkeän asian: olemme vasta raapaisseet pintaa. Tässä blogipostaussarjassa ollaan käyty läpi vasta hyvin pintapuolisesti, että mitä sovelluksen tekeminen WPF:llä oikeastaan onkaan.

_Periaatteessa_ sovellus voisi olla valmis tässä tilassa kuin se nyt on, mutta en voisi päästää sinua villiin luontoon vain näiden oppien siivittämän. Ee-hei. En alkuunsakaan. Sinänsä tuntuu pahalta, että sovellus on _periaatteessa_ valmis ja täysin toimiva, mutta jokin asia kalvaa mieltäni: sovelluksen arkkitehtuuri.

En ole maininnut sanallakaan termiä [*MVVM*](https://docs.microsoft.com/en-us/archive/msdn-magazine/2009/february/patterns-wpf-apps-with-the-model-view-viewmodel-design-pattern). Jätän vain tämän linkin tähän ja katsotaan jatkossa, että josko muuntaisimme painoindeksisovelluksemme käyttämään kyseistä designia. En ole urallani vielä törmännyt yhteenkään WPF-sovellukseen, joka ei olisi käyttänyt MVVM:ää (tai jotain sen sovitelmaa) pohjanaan. Komennot, bindaukset ja viewmodelit ovat tekemisen suola MVVM:llä.

Olemme kuitenkin saaneet nyt jotain täysin toimivaa ja käytettävää aikaiseksi, joka on jo saavutus sinänsä!

Jos pääsit tänne asti, niin onnittele itseäsi vielä kertaalleen. Hieno homma! Kettu kuittaa 🦊