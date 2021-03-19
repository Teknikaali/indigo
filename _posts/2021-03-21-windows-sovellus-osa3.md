---
hidden: true
title: "Tehdään Windows-työpöytäsovellus"
layout: post
date: 2021-03-21 18:00
headerImage: false
tag:
- WPF
- Opas
star: true
category: blog
author: Anssi Kettunen
description: Opas Windows-työpöytäsovelluksen tekemiseen WPF:llä
---

## 5 - Käyttöliittymän toteutus

Keskitytään ensiksi saamaan ohjelman toiminnallisuudet valmiiksi ja sitten voimme hienosäätää ulkoasua selkeämmäksi. Alussa ohjelma tulee näyttämään hyvin alkukantaiselta, mutta suljetaan siltä silmämme ja keskitytään saamaan jotain oikeasti toimivaa aikaiseksi ensiksi. Tyylitellään käyttöliittymästä täysin juhlakelpoinen tapaus joskus myöhemmin.. 😉

Vaihdetaan aivan ensimmäiseksi sovelluksen otsikko `MainWindow` parempaan. Pääsääntöisesti ohjelmointi tapahtuu käyttäen englanninkielisiä avainsanoja, joten noudatetaan tätä ja nimetään sovelluksemme. Saanen esitellä: `BMI Calculator` 🎉.

* `<Window>`-elementin `Title`-attribuutin arvo määrittää pääikkunan otsikossa näytettävän tekstin:

`MainWindow.xaml:`

```diff
    ...
    mc:Ignorable="d"
-    Title="MainWindow" Height="450" Width="800">
+    Title="BMI Calculator" Height="450" Width="800">
    <Grid>
    
    </Grid>
...
```

Attribuutit voidaan asetella myös omille riveilleen tähän tapaan rikkomatta syntaksia:
```xml
<Window ...
        Title="BMI Calculator"
        Width="800"
        Height="450">
```

---

### Ongelma: Pituuden ja painon syöttäminen

Haluamme pystyä syöttämään pituuden ja painon arvot laskentaa varten.
Intensiivinen sovelluksen tuijottaminen ei tuo lukemia ruudulle, joten tehdään näin:
* Lisätään kaksi tekstinsyöttölaatikkoa, joihin voimme syöttää pituuden ja painon.

💡 [Tässä](https://docs.microsoft.com/en-us/dotnet/desktop/wpf/controls/controls-by-category) on listaus WPF:n yleisistä UI-kontrolleista jaettuna kätevästi eri kategorioihin.

#### Tekstinsyöttölaatikoiden lisäys

* `<TextBox>` on hyvä valinta tekstin syöttämiseen.
    * `<Label>` ja `<TextBlock>` ovat hyviä valintoja, kun tekstiä halutaan vain näyttää.

`MainWindow.xaml:`

```diff
        ...
        Title="BMI Calculator" Height="450" Width="800">
    <Grid>
+        <TextBox />
+        <TextBox />
    </Grid>
...
```

Käynnistä ohjelma ja ihaile loistavaa tuotost.. hetkinen. Vain toinen syötekentistä on näkyvissä ja sekin on koko ikkunan kokoinen! On aika ratkaista seuraava ongelmamme.. 🕳🐇