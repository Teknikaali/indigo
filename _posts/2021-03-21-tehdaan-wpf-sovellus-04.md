---
title: "Tehdään WPF-sovellus - 4 - Värit"
image: assets/images/tehdaan-wpf-sovellus/04-header.jpg
layout: post
date: 2021-03-21 17:26
tag:
- WPF
category: blog
author: anssikettunen
description: Opas kuinka tehdä Windows-työpöytäsovellus käyttäen WPF, XAML ja C#.
---

## Ongelma: Vain toinen tekstilaatikoista näkyy

Meidän täytyy määritellä `Grid` tarkemmin, jotta elementit eivät kasaantuisi sen sisällä päällekkäin.

Tarvitsemme rivejä. Paljon rivejä. Lisää rivejä! ..eli kaksi uutta riviä.

Ennen kuin käymme rivien kimppuun, niin lisätään laatikoille uniikit tekstin ja taustan värit, jotta voisimme havaita muutokset helpommin.

## Värien lisäys 🎨

* Elementin taustan ja tekstin värit annetaan käyttämällä `Background`- ja `Foreground`-arvoja.
* Voit käyttää esimerkiksi näitä ehkä jopa herkulliseltakin kuulostavia värejä:

```xml
<TextBox Background="MintCream"
         Foreground="DarkSalmon" />
<TextBox Grid.Row="1"
         Background="Cornsilk"
         Foreground="Chocolate" />
```
<figcaption>MainWindow.xaml</figcaption>

{% include note.html content="Jos valitessasi väriä Visual Studio ei tarjoakaan värilistaa, niin klikkaa kursorisi lainausmerkkien sisälle ja paina `Ctrl`+`Space` ja lista ilmestyy uudelleen." %}

{% include note.html content="Huomaa myös, että niin kauan kunnes elementti suljetaan `/>`-merkillä, voit syöttää arvoja elementille omille riveilleen kuten yllä on tehty." %}

Aloitetaan kahden uuden rivin määrittely lisäämällä ruudukon `Grid.RowDefinitions`-kokoelman sisälle kaksi uutta rivin määrittelyä `RowDefinition`:

```diff
    <Grid>
+        <Grid.RowDefinitions>
+            <RowDefinition />
+            <RowDefinition />
+        </Grid.RowDefinitions>
        <TextBox Background="MintCream"
                 Foreground="DarkSalmon" />
        <TextBox Grid.Row="1"
                 Background="Cornsilk"
                 Foreground="Chocolate" />
    </Grid>
```
<figcaption>MainWindow.xaml</figcaption>

Nyt kun suoritat ohjelman uudelleen, niin et näe mitään muutoksia edelliseen. Miksi?

![Vain yksi tekstinsyöttölaatikko on näkyvissä][1]{: .center-image }
<figcaption class="caption">Vain yksi tekstinsyöttölaatikko on näkyvissä</figcaption>

Jätä ohjelma toistaiseksi vielä käyntiin ja tehdään tarvittavat korjaukset hyödyntämällä WPF:n hienoa `XAML Edit & Continue`-ominaisuutta. Tämän avulla voit muokata _käynnissä olevaa_ sovellusta reaaliaikaisesti. Muokkausten välissä ei ole siis tarvetta sulkea ja käynnistää sovellustamme aina uudelleen (tietyin rajoittein). Vau!

Ongelmamme johtuu siitä, että oletusarvoisesti `Grid`:n sisälle lisätyt elementit sijoitetaan ruudukon ensimmäiselle riville (rivi "0"). Meillä on nyt siis kaksi elementtiä, jotka molemmat kilpailevat näkyvyydestään samalla rivillä.

Aika tukalaa.

Ratkaisuna voimme määrittää erikseen millä rivillä ja sarakkeella haluamme elementtiemme sijaitsevan. Asetetaan siis `Grid.Row`-arvo alemmalle tekstinsyöttölaatikolle:

```diff
        </Grid.RowDefinitions>
        <TextBox Background="MintCream"
                 Foreground="DarkSalmon" />
-        <TextBox Background="Cornsilk"
+        <TextBox Grid.Row="1"
                 Background="Cornsilk"
                 Foreground="Chocolate" />
    </Grid>
```
<figcaption>MainWindow.xaml</figcaption>

Nyt laatikot näkyvät nätisti päällekkäin, mutta meillä on jälleen uusi ongelma ratkaistavana:
Mistä tiedämme kumpaan syötämme pituuden ja kumpaan painon?

![Molemmat tekstinsyöttölaatikot ovat näkyvissä][2]{: .center-image }
<figcaption class="caption">Molemmat tekstinsyöttölaatikot ovat näkyvissä</figcaption>

Selkeytetään sovelluksen asettelua seuraavaksi.

[1]: /assets/images/tehdaan-wpf-sovellus/04-01.png
[2]: /assets/images/tehdaan-wpf-sovellus/04-02.png