---
title: "Tehdään WPF-sovellus - 11 - Tyylien määrittely"
image: assets/images/tehdaan-wpf-sovellus/ketuttaako-header.jpg
layout: post
date: 2021-04-04 21:00
tag:
- WPF
category: blog
author: anssikettunen
description: Opas kuinka tehdä Windows-työpöytäsovellus käyttäen WPF, XAML ja C#. Tässä osassa lisätään uudet tyylit eri tyyppisille elementeille.
---

## Esivalmistelut

Vaihdetaan aivan ensimmäisenä ikkunamme tausta vastaamaan esimerkkityylin tummansinistä väriä. Tehdään tämä siksi, ettei meidän tarvitsisi huolehtia mahdollisista taustavärin vuodoista elementtien läpi kun alamme pyöristelemään elementtien muotoja ja lisäämään niiden välille väljyyttä.

Kasvatetaan samalla sovelluksen korkeutta, jotta saamme elementit sijoiteltua sopusuhtaisemmin.

```xml
<Window ...
        Height="500"
        Background="#0A0D22">
```
<figcaption>MainWindow.xaml - Korkeuden kasvattaminen ja taustavärin asettaminen.</figcaption>

{% include note.html content="Elementeille voi antaa esimääritellyn värin (MintCream, DarkSalmon..), tai määrittää värin heksadesimaalina (#0A0D22). Useimmat kuvankäsittelyohjelmat mahdollistavat värinvalitsimella poimitun värin esittämisen heksadesimaalina, josta arvo on yksinkertaista poimia. Tämä tietenkin vain, jos designissa ei ole jo valmiiksi annettu värikoodeja, tai sovelluksella ei ole tehty valmista tyylimäärittelydokumentaatiota, josta nämä tiedot olisi helposti saatavilla." %}

![Taustaväri ja koko asetettu][1]{: .center-image }
<figcaption class="caption">Taustaväri ja koko asetettu.</figcaption>

Jos ihmettelet, että miksemme lisänneet myös ikkunan taustaväriä Style-elementillä, niin ihmettelin itsekin samaa.. Jotkin yksinkertaisilta vaikuttavat asiat voivat olla yllättävänkin haastavia toteuttaa WPF:llä, mutta useimmiten näille omituisuuksille löytyy kuitenkin [ihan järkeenkäypä selitys](https://stackoverflow.com/questions/6902126/why-doesnt-style-setting-of-the-window-background-work).

Teksteistä ei saa mitään selvää enää, mutta ei anneta sen häiritä. Tulemme korjaamaan tilanteen, kun tyylittelemme `TextBlock`-elementin myöhemmin.

## Tyylitellään TextBox

Aloitetaan vaihtamalla tekstinsyöttölaatikon tausta läpinäkyväksi ja teksti valkoiseksi. Tyylin tulisi näyttää nyt tältä:

```xml
<Style TargetType="TextBox">
    <Setter Property="Background" Value="Transparent" />
    <Setter Property="Foreground" Value="White" />
</Style>
```
<figcaption>App.xaml - TextBox-tyylin värit vaihdettu.</figcaption>

Mistä tiedämme, että miltä syötekenttämme uusi tekstin väri näyttäisi ilman, että käynnistäisimme sovelluksen ja kirjoittaisimme jotain kenttään? Ratkaisu on "Design Time Data".

## WPF ja Design Time Data

Joskus suunnitelmia on vaikea havainnollistaa ilman esimerkkidataa. "Design time"-arvot, eli ehkä jotenkin suomennettuna "suunnittelunaikaiset arvot", ovat käteviä silloin, kun haluamme tarkastella miltä sovelluksen tyylit näyttäisivät käynnistämättä ohjelmaa ja syöttämättä kenttiin aina manuaalisesti arvoja. Tällä ominaisuudella voi säästää arvokasta suunnittelu- ja sommitteluaikaa pidemmällä aikavälillä 👍

[Tässä](https://docs.microsoft.com/en-us/visualstudio/xaml-tools/xaml-designtime-data) lisää aiheesta.

Voimme siis yksinkertaisesti käyttää `d:`:tä minkä tahansa attribuutin edessä näyttääksemme sille annetun arvon vain XAML Designerissä ilman, että arvo vaikuttaisi mitenkään ohjelmaan suorituksen aikana.

Lisätään siis tekstinsyöttökentille mielivaltaiset suunnittelunaikaiset arvot `123` ja `456`:

```xml
<TextBox Name="WeightInput"
        Grid.Row="1"
        d:Text="123" />
...
<TextBox Name="HeightInput"
        Grid.Row="1"
        d:Text="456" />
```
<figcaption>MainWindow.xaml - Suunnittelunaikaiset arvot.</figcaption>

![Suunnittelunaikaiset arvot näkyvillä XAML Designerissa][2]{: .center-image }
<figcaption class="caption">Suunnittelunaikaiset arvot näkyvillä XAML Designerissa.</figcaption>

## Jatketaan TextBoxin tyylittelyä

XAML Designer ei välttämättä kerro aina koko tarinaa siitä, että miltä sovellus _käytännössä_ näyttää, kun sitä käytetään oikeasti. Esimerkiksi tekstinsyöttökursori (caret) hukkuu taustaan. Tämäntyyppiset ongelmat eivät tule esille vain XAML Designerissä tyyliä tarkastelemalla, vaan vaativat usein ohjelman käyttämistä (tai vain erinomaisen ulkomuistin).

{% include note.html content="Sinänsä minulla ei ole henkilökohtaisesti mitään XAML Designeria vastaan, mutta suurimmaksi osaksi tapaan työskennellä pelkän XAML-koodinäkymän avulla ja pitämällä ohjelman käynnissä siinä sivussa. Etenkin silloin, kun ikkunalla näytetään hierarkisesti monen asian pohjalta rakentuvaa tietoa, saattaa olla yksinkertaisempaa luoda esimerkkidata käynnissä olevalla ohjelmalla kuin käsin Design Datan kautta. Toki tässä päästään siihen kysymykseen, että miksei käyttöliittymää ja sovellusta olisi rakennettu niin, että tähän ongelmaan ei törmättäisi alkuunsakaan.. Hmm. Jokainen tyylillään! 👍" %}

Päivitetään tekstinsyöttölaatikon kursori, fontti ja tekstin asemointi, jonka jälkeen `TextBox`-tyyli näyttää tältä:

```xml
<Style TargetType="TextBox">
    <Setter Property="Background" Value="Transparent" />
    <Setter Property="BorderThickness" Value="0" />
    <Setter Property="CaretBrush" Value="White" />
    <Setter Property="FontFamily" Value="Calibri" />
    <Setter Property="FontSize" Value="48" />
    <Setter Property="Foreground" Value="White" />
    <Setter Property="TextAlignment" Value="Center" />
</Style>
```
<figcaption>App.xaml - TextBox-tyyli kokonaisuudessaan.</figcaption>

Joissain tapauksissa on hyvä käyttää `F4`-painikkeella avautuvaa Properties-ikkunaa. Esimerkiksi en ainakaan itse muista ulkoa kuin miltä Comic Sans ja muutama muu fontti näyttävät. On siis helpompi vain valita käytettävä fontti alasvetovalikosta, joka näyttää kätevästi esimerkin kaikista asennetuista fonteista:

![Fontin ominaisuudet Properties-ikkunassa.][3]{: .center-image }
<figcaption class="caption">Fontin ominaisuudet Properties-ikkunassa.</figcaption>

{% include note.html content="Kirjoitushetkellä Visual Studion Properties-ikkuna toimii hieman vaivalloisesti: Tyylin arvoksi täytyy ensin antaa jokin oikea arvo ennen kuin sitä voi vaihtaa Properties-ikkunan alasvetovalikon kautta." %}

Nyt ikkunamme alkaa näyttämään jo hieman paremmalta, vaikka meillä onkin vielä jonkin verran matkaa edessämme saavuttaaksemme tavoitteemme nätimmästä sovelluksesta! 👍

![Tyylitellyt TextBox-elementit.][4]{: .center-image }
<figcaption class="caption">Tyylitellyt TextBox-elementit.</figcaption>

Tyylitellään seuraavassa osassa `TextBlock`-elementit kuntoon.

[1]: /assets/images/tehdaan-wpf-sovellus/11-01.jpg
[2]: /assets/images/tehdaan-wpf-sovellus/11-02.jpg
[3]: /assets/images/tehdaan-wpf-sovellus/11-03.jpg
[4]: /assets/images/tehdaan-wpf-sovellus/11-04.jpg