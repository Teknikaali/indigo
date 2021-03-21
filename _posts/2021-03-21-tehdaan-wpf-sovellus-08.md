---
title: "Tehdään WPF-sovellus - 8 - Viimeistely"
image: assets/images/tehdaan-wpf-sovellus/ketuttaako-header.jpg
layout: post
date: 2021-03-21 17:30
tag:
- WPF
category: blog
author: anssikettunen
description: Opas kuinka tehdä Windows-työpöytäsovellus käyttäen WPF, XAML ja C#.
---

## Ongelma: Laskettu tulos ei näytä oikealta

Tarkastelemalla tulosta tarkemmin voimme havaita, että vain pilkku on väärässä paikassa. Saimme tulokseksi `0,0020902`, kun oikea tulos olisi ollut `20,9`. Katsotaanpa kaavaa vielä uudelleen:

```
Painoindeksi = paino(kg) / pituus(m) / pituus(m)
```

A-haa! Pituus on annettu kaavassa **metreinä**, joten periaatteessa mitään ongelmaa ei ollut. Kyse oli vain käyttäjän tietämättömyydestä, että arvot pitäisi antaa metreinä **senttimetrien** sijaan. Ihmisen pituudesta puhuttaessa käytetään useammin senttimetrejä kuin metrejä, joten muunnetaan sovellus käyttämään tätä yleisempää yksikköä.

Lisätään ensin tekstilaatikoihin yksiköt esille, jotta jatkossa ei jäisi epäselväksi, että missä muodossa arvot pitäisi syöttää.

```diff
-<TextBlock Text="Weight" />
+<TextBlock Text="Weight (kg)" />
```

```diff
-<TextBlock Text="Height" />
+<TextBlock Text="Height (cm)" />
```
<figcaption>MainWindow.xaml</figcaption>

Seuraavaksi meidän tulee korjata yksikkömuunnos taustakoodin puolelta muuntamalla metrit senttimetreiksi. Tehdään muunnos juuri ennen indeksin laskentaa:

```diff
+    height = height / 100;
    var bodyMassIndex = weight / Math.Pow(height, 2);
```
<figcaption>MainWindow.xaml.cs</figcaption>

{% include note.html content="Et voi muokata taustakoodia ohjelman ollessa käynnissä, ellet pysäytä ohjelman suoritusta breakpointilla ennen kohtaa, jota ajattelit muokata." %}

{% include note.html content="`height = height / 100` voi kirjoittaa myös muotoon `height /= 100` ([Compound assignment](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/operators/assignment-operator#compound-assignment))" %}

Nyt ohjelma antaa tulokseksi lähes hyväksyttävän lukeman `2,0902385...`. Meidän ei kuitenkaan tarvitse tietää lukemaa aivan näin tarkasti, joten tiputetaan vielä muutama desimaali pois:

```diff
            var bodyMassIndex = weight / Math.Pow(height, 2);
-            var resultMessage = $"BMI: {bodyMassIndex}";
+            var resultMessage = $"BMI: {bodyMassIndex:N1}";
            MessageBox.Show(resultMessage);
```

{% include note.html content="`N1` rajaa desimaalit sopivasti yhteen. [Tässä](https://docs.microsoft.com/en-us/dotnet/standard/base-types/standard-numeric-format-strings#standard-format-specifiers) eri muotoilumäärittelyt listattuna (Standard format specifiers)." %}

![Valmis sovellus][1]{: .center-image }
<figcaption class="caption">Valmis sovellus</figcaption>

---

## Valmista tuli!

Voit vihdoinkin taputtaa itseäsi olalle ja todeta: "Hieno homma, onnistuin!"

Sovellus tekee nyt kaiken mitä olimme alussa määritelleet, eli:
* Laskee annettujen tietojen pohjalta painoindeksin
* Tulosta voi tarkastella

Olet nyt oppinut minkälaista työpöytäsovelluksen kehittäminen WPF:llä voi olla yksinkertaisimmillaan.

## Jatkokehitys

Tekemisen ei tarvitse päättyä vielä tähän, sillä nyt meillä on käsissämme vasta juuri ja juuri toimiva sovellus.

Tässäpä pari kysymystä, joiden pohjalta voimme lähteä suunnittelemaan jatkokehitystä:
* Mitä tapahtuu, kun painoksi annetaan negatiivinen luku?
* Mitä jos painon haluaisikin antaa paunoissa ja pituuden jaloissa ja tuumissa?
* Tulisiko sukupuolen vaikuttaa tulokseen?
* Pitäisikö käyttäjälle antaa mahdollisuus käyttää myös [ehdotettua uutta laskukaavaa](https://en.wikipedia.org/wiki/Body_mass_index#Proposed_New_BMI) halutessaan?
* Ohjelma on ruma kuin synti, joten miten saisimme tehtyä siitä nätimmän?
* Onko järkevää antaa käyttäjän syöttää myös kirjaimia ja erikoismerkkejä syötekenttiin?
* Onko ohjelman arkkitehtuuri miellyttävä jatkokehitystä ajatellen?
* Eikös sovelluksen tulisi sisältää yksikkötestejä ettemme rikkoisi mitään vahingossa jatkokehittäessämme?

Kysymyksiä on suhteellisen paljon, vaikka kyse onkin vain yhden toiminnallisuuden sovelluksesta. Huhhuh! Tässäpä riittää työmaata vielä seuraavillekin kerroille.

[1]: /assets/images/tehdaan-wpf-sovellus/08-01.png