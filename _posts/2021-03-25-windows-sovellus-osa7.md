---
hidden: true
title: "Tehdään Windows-työpöytäsovellus"
layout: post
date: 2021-03-25 18:00
headerImage: false
tag:
- WPF
- Opas
star: true
category: blog
author: Anssi Kettunen
description: Opas Windows-työpöytäsovelluksen tekemiseen WPF:llä
---

### Ongelma: Painiketta painaessa ei tapahdu mitään

Haluaisimme kahden asian tapahtuvan, kun painiketta painetaan:
1. Suoritetaan laskutoimitus
2. Näytetään laskutoimituksen tulos

Aivan ensimmäiseksi tarvitsemme käsiimme pituuden ja painon arvot ennen kuin voimme tehdä laskutoimituksen. Lisätään syötekentille nimet, jotta voimme viitata niihin taustakoodissa.

💡 Elementeille voi antaa nimen `Name`-attribuutilla ([tai `x:Name`](https://stackoverflow.com/questions/589874/in-wpf-what-are-the-differences-between-the-xname-and-name-attributes)), mutta tässä on pari hyvää nyrkkisääntöä:
* Nimen voi antaa ainakin interaktiivisille elementeille eli niille, joita käyttäjä voi jollain tavoin käsitellä:
    * Esim. `<TextBox>` ja `<Button>`
* Nimeä ei välttämättä kannata antaa vain ulkoasuun vaikuttaville elementeille:
    * Esim. `<Grid>` ja `<Border>`

`MainWindow.xaml:`

```diff
            <TextBlock Text="Weight" />
-            <TextBox Grid.Row="1"
+            <TextBox Name="WeightInput"
+                     Grid.Row="1"
                     Background="MintCream"
                     Foreground="DarkSalmon" />
            ...
            <TextBlock Text="Height" />
-            <TextBox Grid.Row="1"
+            <TextBox Name="HeightInput"
+                     Grid.Row="1"
                     Background="Cornsilk"
                     Foreground="Chocolate" />
```

Nyt voimme viitata elementteihin `WeightInput` ja `HeightInput` taustakoodin puolella ja hakea niiltä tarvitsemamme arvot.

Tässä kohtaa on hyvä huomata, että tekstilaatikkoon syötetty sisältö on nimensä mukaisesti _tekstiä_. Emme voi suorittaa laskutoimituksia tekstillä, joten meidän on myös muunnettava syöte `double`-tyyppiseksi.

💡 `decimal`, `float` ja `double` eroavat hieman toisistaan, joten [tässä](https://stackoverflow.com/questions/618535/difference-between-decimal-float-and-double-in-net) lisää tietoa aiheesta helposti pureskeltavassa muodossa.

Poimitaan muunnetut syötteet omiin muuttujiinsa, jolloin meidän on helpompi käsitellä niitä:

`MainWindow.xaml.cs:`

```diff
        private void Button_Click(object sender, RoutedEventArgs e)
        {
+            var isWeightValid = double.TryParse(WeightInput.Text, out var weight);
+            var isHeightValid = double.TryParse(HeightInput.Text, out var height);
        }
```

Tässä on mahdutettu paljon koodia samalle riville, joten pidä hatustasi kiinni niin avataan mitä riveillä oikeastaan tapahtuukaan:
* `double.TryParse`-funktio _yrittää_ parsia tekstisyötteestä (`WeightInput.Text`) `double`-tyyppisen tuloksen.
* Jos hoveroit `TryParse`-sanan päällä, niin näet, että funktio palauttaa `bool`-tyyppisen paluaarvon:
    * `isWeightValid` saa arvon `true`, jos parsinta onnistuu, muutoin `false`
* Tulos sijoitetaan uuteen `weight`-muuttujaan käyttämällä `out`-avainsanaan
    * [Tässä](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/out-parameter-modifier) lisää tietoa avainsanan `out` käytöstä
* Rivin voisi kirjoittaa auki myös näin halutessaan:
    ```csharp
    bool weight;
    bool isWeightValid = double.TryParse(WeightInput.Text, out weight);
    ```

Jätämme vielä tässä vaiheessa virheenkäsittelyn erittäin yksinkertaiselle tasolle, jotta saisimme vain tulosta aikaiseksi. Suoritetaan laskutoimitus vain, jos molemmat pituus ja paino ovat ok:

`MainWindow.xaml.cs:`

```diff
        private void Button_Click(object sender, RoutedEventArgs e)
        {
            var isWeightValid = double.TryParse(WeightInput.Text, out var weight);
            var isHeightValid = double.TryParse(HeightInput.Text, out var height);
+
+            if(isWeightValid && isHeightValid)
+            {
+                var bodyMassIndex = weight / Math.Pow(height, 2);
+            }
        }
```

💡 Mielenkiintoista kyllä, että tästä käytetystä [kaavasta](https://fi.wikipedia.org/wiki/Painoindeksi#M%C3%A4%C3%A4ritelm%C3%A4) on ehdotettu [uutta muotoa](https://en.wikipedia.org/wiki/Body_mass_index#Proposed_New_BMI), joka ottaisi paremmin huomioon vääristymät lyhyiden ja pitkien henkilöiden kohdalla.

---

#### Tulosten esittäminen

Tuloksen voimme tuoda näkyviin popup-viestilaatikolla:

`MainWindow.xaml.cs:`

```diff
            if(isHeightValid && isWeightValid)
            {
                var bodyMassIndex = weight / Math.Pow(height, 2);
+                var resultMessage = $"BMI: {bodyMassIndex}";
+                MessageBox.Show(resultMessage);
            }
```

💡 Lisäämällä dollarimerkin ennen lainausmerkkejä voimme kätevästi syöttää arvot suoraan tekstin sisälle: `$"Teksti {arvo}"`. Lisää aiheesta: [$ - string interpolation](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/tokens/interpolated).

Hmm. Jokin tässä ei nyt täsmää.. Kaava _näyttäisi_ olevan ok ja viestin esittäminenkin _näyttäisi_ olevan ihan kunnossa. No, ei välitetä pienestä "jotain saattaa olla pielessä"-ajatuksesta takaraivossamme ja käynnistetään aivan varmasti 100% täysin toimiva ohjelmamme! Olemmehan sen sentään aivan itse koodanneet. Täysin bugivapaata ja priimaa softaa, eikös?

Anna paino kiloina ja pituus senttimetreinä niin kuin säädylliset ihmisolennot tekevät. Oletko valmis ihailemaan tuloksia?

Paina `Calculate`-painiketta.

Sain itse tulokseksi `0,0020902...`. Mutta mitäs ihmettä! Tämä ei missään määrin voi olla oikea tulos, sillä muutoinhan olisin _sairaalloisen alipainoinen_. Huhhuh.

Onko sovelluksessamme _bugi_? Eikai sentään.. Voiko tämä olla?

💡 Voit vilkaista miltä tulosten pitäisi oikeasti näyttää antamillasi arvoilla esim. [laskurini.fi](https://www.laskurini.fi/terveys/painoindeksilaskuri)-palvelun painoindeksilaskurilla.