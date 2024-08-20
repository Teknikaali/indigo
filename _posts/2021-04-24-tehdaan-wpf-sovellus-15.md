---
title: "Tehdään WPF-sovellus - 15 - Yksiköt paikoilleen"
image: assets/images/tehdaan-wpf-sovellus/ketuttaako-header.jpg
layout: post
date: 2021-04-24 11:00
tag:
- WPF
category: blog
author: anssikettunen
description: Opas kuinka tehdä Windows-työpöytäsovellus käyttäen WPF, XAML ja C#. Tässä osassa parannetaan elementtien asettelua ja lisätään yksiköt takaisin paikoilleen.
---

## Yksikköjen asettelu vaatii uuden ruudukon

Käyttöliittymän asettelua tehdessä on hyvä muistaa, että yksinkertaisten asioiden kanssa on helppo työskennellä. Keep it simple.
Aiemmissa osissa olemme käyttäneet `Grid`-elementtiä, joten jatketaan hyväksi todetulla linjalla.
Saadaksemme yksiköt nätisti syötekenttien vierelle meidän tulee lisätä uusi `Grid`. Lisätään uudelle ruudukolle 3 saraketta, joista yhden varaamme syötekentälle ja yhden yksikölle (ensimmäinen saa jäädä tyhjäksi).

Tämä tarkoittaa sitä, että kiedomme `TextBox`-elementin uudella `Grid`-elementillä ja määrittelemme kolme uutta saraketta uudelle ruudukolle.

Eli muutamme painon osalta tämän:

```xml
<TextBox Name="WeightInput"
         Grid.Row="1"
         d:Text="123" />
```

Tähän:

```xml
<Grid Grid.Row="1">
    <Grid.ColumnDefinitions>
        <ColumnDefinition Width="*" />
        <ColumnDefinition Width="*" />
        <ColumnDefinition Width="*" />
    </Grid.ColumnDefinitions>
    <TextBox Name="WeightInput"
             Grid.Column="1"
             Margin="12,0"
             d:Text="123"
             TextAlignment="Center" />
    <TextBlock Grid.Column="2"
               HorizontalAlignment="Left"
               VerticalAlignment="Center"
               Text="kg" />
</Grid>
```

Suoritetaan sovellus ja kokeillaan syöttää iso lukema painolle:

![][1]{: .center-image }
<figcaption class="caption">Isot lukemat jäävät virheellisesti piiloon</figcaption>

Heti kolmannen numeron jälkeen syötekenttä alkaa käyttäytymään hankalasti, joten rajataan numeroiden lukumäärä johonkin kohtuulliseen.

Päätetään tässä vaiheessa, että teemme sovellusta _ihmisten_ painoindeksien laskentaan. Pari hakutulosta myöhemmin voimme todeta, että yksikään ihminen ei ole (vielä eikä toivottavasti) painanut yli tuhatta kiloa, joten voimme rajata painon syötekentän 3-numeroiseksi arvoksi.

{% include note.html content='[Lista painavimmista ihmisistä.](https://en.wikipedia.org/wiki/List_of_heaviest_people)' %}

Voimme tehdä saman rajoituksen myös pituudelle, sillä kirjoitushetkellä pisin ihminen ei ole vielä ylittänyt 300 cm lukemaa.

{% include note.html content='[Lista pisimmistä ihmisistä.](https://en.wikipedia.org/wiki/List_of_tallest_people).' %}

Sivuhuomiona pensasnorsu voi painaa yli 10 000 kg ja kirahvi voi olla korkeudeltaan yli 600 cm. Valitettavasti joudumme jättämään nämä eläinkunnan edustajat sovelluksemme toiminnan ulkopuolelle. Ei mulla muuta. Jatketaan.

Syötekentän pituuden voi rajata yksinkertaisesti antamalla sille `MaxLength`-arvon:

```xml
<TextBox Name="WeightInput"
         MaxLength="3"
```
<figcaption>MainWindow.xaml - Painon rajaaminen 3-numeroiseksi.</figcaption>

Tehdään samat temput pituudelle:

```xml
<!--  HEIGHT  -->
<Grid Grid.Row="1"
      VerticalAlignment="Center">
    <Grid.RowDefinitions>
        <RowDefinition Height="Auto" />
        <RowDefinition Height="Auto" />
    </Grid.RowDefinitions>
    <TextBlock Text="HEIGHT" />
    <Grid Grid.Row="1">
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>
        <TextBox Name="HeightInput"
                 Grid.Column="1"
                 Margin="12,0"
                 d:Text="456"
                 MaxLength="3" />
        <TextBlock Grid.Column="2"
                   HorizontalAlignment="Left"
                   VerticalAlignment="Center"
                   Text="cm" />
    </Grid>
</Grid>
```
<figcaption>MainWindow.xaml - Pituuden asemointi.</figcaption>

Lopputuloksen pitäisi näyttää tältä:

![][2]{: .center-image }
<figcaption class="caption">Paino ja pituus asemoitu</figcaption>

Seuraavassa osassa lisätään aiemmin kuvissa taustalla näkyneet vaaleammat pyöreäkulmaiset laatikot erottelemaan painon ja pituuden alueet toisistaan.

[1]: /assets/images/tehdaan-wpf-sovellus/15-01.jpg
[2]: /assets/images/tehdaan-wpf-sovellus/15-02.jpg
