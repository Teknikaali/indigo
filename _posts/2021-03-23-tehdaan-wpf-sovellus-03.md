---
hidden: true
title: "Tehdään WPF-sovellus - 3 - Käyttöliittymän toteutus"
image: assets/images/tehdaan-wpf-sovellus/ketuttaako-header.jpg
layout: post
date: 2021-03-20 12:00
tag:
- WPF
category: blog
author: anssikettunen
description: Opas kuinka tehdä Windows-työpöytäsovellus käyttäen WPF, XAML ja C#.
---

## Otsikon muuttaminen

Keskitytään ensiksi saamaan ohjelman toiminnallisuudet valmiiksi ja sitten voimme hienosäätää ulkoasua selkeämmäksi. Alussa ohjelma tulee näyttämään hyvin alkukantaiselta, mutta suljetaan siltä silmämme ja keskitytään saamaan jotain oikeasti toimivaa aikaiseksi ensiksi. Tyylitellään käyttöliittymästä täysin juhlakelpoinen tapaus joskus myöhemmin (™).

Vaihdetaan aivan ensimmäiseksi sovelluksen otsikko `MainWindow` parempaan. Usein ohjelmointia harjoitetaan käyttäen englanninkielisiä avainsanoja, joten noudatetaan tätä ja nimetään sovelluksemme. Saanen esitellä: `BMI Calculator` 🎉

* `<Window>`-elementin `Title`-attribuutin arvo määrittää pääikkunan otsikossa näytettävän tekstin:

```diff
    mc:Ignorable="d"
-    Title="MainWindow" Height="450" Width="800">
+    Title="BMI Calculator" Height="450" Width="800">
    <Grid>
    
    </Grid>
```
<figcaption>MainWindow.xaml</figcaption>

Attribuutit voidaan asetella myös omille riveilleen tähän tapaan rikkomatta syntaksia:

```xml
<Window Title="BMI Calculator"
        Width="800"
        Height="450">
```

{% include note.html content="Valitun elementin ominaisuuksia voi muokata myös `Properties`-ikkunan kautta (View -> Properties Window tai `F4`). " %}

---

## Ongelma: Pituuden ja painon syöttäminen

Haluaisimme pystyä syöttämään pituuden ja painon arvot laskentaa varten.
Intensiivinen sovelluksen tuijottaminen ei tunnu tuovan lukemia ruudulle, joten tehdään näin:
* Lisätään kaksi tekstinsyöttölaatikkoa, joihin voimme syöttää pituuden ja painon.

{% include note.html content="[Tässä](https://docs.microsoft.com/en-us/dotnet/desktop/wpf/controls/controls-by-category) on listaus WPF:n yleisistä UI-kontrolleista jaettuna kätevästi eri kategorioihin." %}

* `<TextBox>` on hyvä valinta tekstin syöttämiseen.
    * `<Label>` ja `<TextBlock>` ovat hyviä valintoja, kun tekstiä halutaan vain näyttää.

```diff
        Title="BMI Calculator" Height="450" Width="800">
    <Grid>
+        <TextBox />
+        <TextBox />
    </Grid>
```
<figcaption>MainWindow.xaml</figcaption>

Käynnistä ohjelma ja ihaile loistavaa tuotost.. hetkinen. Vain toinen syötekentistä on näkyvissä ja sekin on koko ikkunan kokoinen! On aika ratkaista seuraava ongelmamme.. 🕳🐇

![Tekstinsyöttölaatikot lisätty][1]{: .center-image }
<figcaption class="caption">Tekstinsyöttölaatikot lisätty</figcaption>

Korjataan syötekenttien näkyvyys seuraavaksi.

[1]: /assets/images/tehdaan-wpf-sovellus/03-01.png