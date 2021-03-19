---
hidden: true
title: "Tehdään Windows-työpöytäsovellus"
layout: post
date: 2021-03-22 18:00
headerImage: false
tag:
- WPF
- Opas
star: true
category: blog
author: Anssi Kettunen
description: Opas Windows-työpöytäsovelluksen tekemiseen WPF:llä
---

### Ongelma: Vain toinen tekstilaatikoista näkyy

Meidän täytyy määritellä `Grid` tarkemmin, jotta elementit eivät kasaantuisi sen sisällä päällekkäin.

Tarvitsemme rivejä. Paljon rivejä. Lisää rivejä! ..eli kaksi uutta riviä.

Ennen kuin käymme rivien kimppuun, niin lisätään laatikoille uniikit tekstin ja taustan värit, jotta voisimme havaita muutokset helpommin.

#### Värien lisäys 🎨

* Elementin taustan ja tekstin värit annetaan käyttämällä `Background`- ja `Foreground`-arvoja.
* Voit käyttää esimerkiksi näitä ehkä jopa herkulliseltakin kuulostavia värejä:

```xml
<TextBox Background="MintCream"
         Foreground="DarkSalmon" />
<TextBox Grid.Row="1"
         Background="Cornsilk"
         Foreground="Chocolate" />
```

💡 Jos valitessasi väriä Visual Studio ei tarjoakaan värilistaa, niin klikkaa kursorisi lainausmerkkien sisälle ja paina `Ctrl`+`Space` ja lista ilmestyy uudelleen.

💡 Huomaa myös, että niin kauan kunnes elementti suljetaan `/>`-merkillä, voit syöttää arvoja elementille omille riveilleen kuten yllä on tehty.

Aloitetaan kahden uuden rivin määrittely lisäämällä ruudukon `Grid.RowDefinitions`-kokoelman sisälle kaksi uutta rivin määrittelyä (`RowDefinition`):

`MainWindow.xaml:`

```diff
...
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
...
```

Nyt kun suoritat ohjelman uudelleen, niin et näe mitään muutoksia edelliseen. Miksi?

Jätä ohjelma toistaiseksi vielä käyntiin ja tehdään tarvittavat korjaukset hyödyntämällä WPF:n hienoa `XAML Edit & Continue`-ominaisuutta. Tämän avulla voit muokata _käynnissä olevaa_ sovellusta reaaliaikaisesti. Muokkausten välissä ei ole siis tarvetta sulkea ja käynnistää sovellustamme aina uudelleen (tietyin rajoittein). Vau!

Ongelmamme johtuu siitä, että oletusarvoisesti `Grid`:n sisälle lisätyt elementit sijoitetaan ruudukon ensimmäiselle riville (rivi "0"). Meillä on nyt siis kaksi elementtiä, jotka molemmat kilpailevat näkyvyydestään samalla rivillä.

Aika tukalaa.

Ratkaisuna voimme määrittää erikseen millä rivillä ja sarakkeella haluamme elementtiemme sijaitsevan. Asetetaan siis `Grid.Row`-arvo alemmalle `TextBox`:lle:

`MainWindow.xaml:`

```diff
...
        </Grid.RowDefinitions>
        <TextBox Background="MintCream"
                 Foreground="DarkSalmon" />
-        <TextBox Background="Cornsilk"
+        <TextBox Grid.Row="1"
                 Background="Cornsilk"
                 Foreground="Chocolate" />
    </Grid>
...
```

Nyt laatikot näkyvät nätisti päällekkäin, mutta meillä on jälleen uusi ongelma ratkaistavana:
Mistä tiedämme kumpaan syötämme pituuden ja kumpaan painon?