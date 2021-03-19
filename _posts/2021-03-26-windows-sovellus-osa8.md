---
hidden: true
title: "Tehdään Windows-työpöytäsovellus - Osa 8 - Viimeistely"
layout: post
date: 2021-03-26 18:00
headerImage: false
tag:
- WPF
category: blog
author: anssikettunen
description: Opas Windows-työpöytäsovelluksen tekemiseen WPF:llä
---

### Ongelma: Laskettu tulos ei näytä oikealta

Tarkastelemalla tulosta tarkemmin voimme havaita, että vain pilkku on väärässä paikassa. Saimme tulokseksi `0,0020902`, kun oikea tulos olisi ollut `20,9`. Katsotaanpa kaavaa vielä uudelleen:

```
Painoindeksi = paino(kg) / pituus(m) / pituus(m)
```

A-haa! Pituus on annettu kaavassa metreinä, joten periaatteessa mitään ongelmaa ei ollut. Kyse oli vain käyttäjän tietämättömyydestä, että arvot pitäisi antaa metreinä. Ihmisen pituudesta puhuttaessa on käytetään useammin senttimetrejä kuin metrejä, joten muunnetaan sovellus käyttämään tätä yleisempää yksikköä.

Lisätään ensin tekstilaatikoihin yksiköt esille, jotta jatkossa ei tulisi epäselvää missä muodossa arvot pitää syöttää.

`MainWindow.xaml:`

```diff
-<TextBlock Text="Weight" />
+<TextBlock Text="Weight (kg)" />
```

```diff
-<TextBlock Text="Height" />
+<TextBlock Text="Height (cm)" />
```

Seuraavaksi meidän tulee korjata yksikkömuunnos taustakoodin puolelta muuntamalla metrit senttimetreiksi:
`MainWindow.xaml.cs:`

```diff
if(isWeightValid && isHeightValid)
            {
+                height = height / 100;
                var bodyMassIndex = weight / Math.Pow(height, 2);
```

💡 `height = height / 100` voi kirjoittaa myös muotoon `height /= 100` ([Compound assignment](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/operators/assignment-operator#compound-assignment))

Nyt ohjelma antaa tulokseksi lähes hyväksyttävän lukeman `2,0902385...`. Meidän ei kuitenkaan tarvitse tietää lukemaa aivan näin tarkasti, joten tiputetaan vielä muutama desimaali pois:

```diff
            var bodyMassIndex = weight / Math.Pow(height, 2);
-            var resultMessage = $"BMI: {bodyMassIndex}";
+            var resultMessage = $"BMI: {bodyMassIndex:N1}";
            MessageBox.Show(resultMessage);
```

💡 `N1` rajaa desimaalit sopivasti yhteen. [Tässä](https://docs.microsoft.com/en-us/dotnet/standard/base-types/standard-numeric-format-strings#standard-format-specifiers) eri muotoilumäärittelyt listattuna (Standard format specifiers).

---

## Valmista tuli!

Voit tässä vaiheessa taputtaa itseäsi olalle ja todeta: "Hieno homma, onnistuin!"

Sovellus tekee nyt kaiken mitä olimme alussa määritelleet, eli:
* Laskee annettujen tietojen pohjalta painoindeksin
* Tulosta voi tarkastella

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