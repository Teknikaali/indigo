---
title: "Tehdään WPF-sovellus - 1 - Alkupalat"
image: assets/images/tehdaan-wpf-sovellus/ketuttaako-header.jpg
layout: post
date: 2021-03-21 17:23
tag:
- WPF
star: true
category: blog
author: anssikettunen
description: Opas kuinka tehdä Windows-työpöytäsovellus käyttäen WPF, XAML ja C#.
---

![Otsikko ja kuva ketusta][1]

## Alkuhöpinät

Jos haluat pienen maistiaisen Windows-työpöytäsovellusten luonnin maailmasta C#:lla ja WPF:llä, on tämä erinomainen mahdollisuus juuri sinulle!

Tässä luotua sovellusta voidaan käyttää hyvänä pohjana jatkokehitykselle myöhemmin. Tarkoitus on, että saisit tämän avulla tehtyä jotain valmista alusta loppuun.

Olen yrittänyt pitää asiat mahdollisimman yksinkertaisella tasolla ja selittää vastaan tulevat uudet konseptit korkealla tasolla paneutumatta niihin liian syvällisesti.

Teen sen olettamuksen, että olet ehkä kokeillut jo jotain ohjelmointiin liittyvää ja hallitset tietokoneen käytön.

## WPF - Windows Presentation Framework

Toteutamme sovelluksemme [WPF](https://docs.microsoft.com/en-us/visualstudio/designers/getting-started-with-wpf)-käyttöliittymän [XAML](https://docs.microsoft.com/en-us/dotnet/desktop/wpf/advanced/xaml-overview) -kielellä ja herätämme toiminnallisuuden henkiin [C#](https://docs.microsoft.com/en-us/dotnet/csharp/tour-of-csharp/):lla.

Opimme kaikista näistä kolmesta enemmän vähitellen mitä pidemmälle pääsemme itse tekemisen kanssa.

Teemme tämän esimerkkisovelluksen UI edellä, eli keskitymme ensin käyttöliittymän muovaamiseen ja vasta sitten toiminnallisuuden toteuttamiseen. WPF:llä pystyy helposti toteuttamaan ohjelman toiminnallisuuden erillään käyttöliittymästä. Tämä mahdollistaa vaikkapa sen, että yksi henkilö voi keskittyä leipomaan hienoa ulkoasua sovellukselle ja toinen taas tekemään itse toiminnallista osuutta.

Olen esittänyt koodiin tehtävät muutokset `git diff`-tyylisesti näin:

```diff
Tätä riviä ei muokata.
- Tätä riviä muokataan.
+ Näin riviä on muokattu.
```

Eiköhän aloiteta. Onnea matkaan! 👍

---

## Määrittely

Ensimmäisenä määrittelemme mitä haluamme sovelluksen pääpiirteittäin tekevän. 

{% include note.html content="Sovelluksia on mielekkäämpää luoda, kun on ensin määritellyt selkeät tavoitteet ja rajat sovellukselle ja itselleen." %}

Sovellus voisi vaikkapa antaa vastauksen kysymykseen:
- "Mikä on painoindeksi annetulle pituudelle ja painolle?"

Yksinkertaisuuden nimissä luomme siis painoindeksin laskemiseen käytettävän sovelluksen seuraavin vaatimuksin:
* Sovellus laskee antamieni tietojen pohjalta painoindeksin
* Voin tarkastella tuloksia

Näitä määrittelyjä voidaan myös tarkentaa tai laajentaa myöhemmin, kun opimme sovelluksesta ja tarpeistamme enemmän. Ei oteta liian suurta haukkausta heti alkuunsa.

## Esivaatimukset

* Lataa ja asenna [Visual Studio 2019 (Community)](https://visualstudio.microsoft.com/downloads/)
    * Varmista asennuksen `Workloads`-kohdassa, että `.NET desktop development` on valittuna
* Käy vilkaisemassa [painoindeksin määritelmä](https://fi.wikipedia.org/wiki/Painoindeksi)
* Varaa itsellesi hetki aikaa
    * Ehkä myös lämmin kupponen juotavaa
    * Mahdollisesti iso pehmeä tyyny on myös hyvä varuste (oman harkinnan mukaan ja riippuen siitä mitä valintoja teet myöhemmin)

{% include note.html content="Voit myös pilkkoa tekemisen useammalle eri kerralle. Kaikkea ei ole pakko rykäistä kerralla valmiiksi. Esimerkiksi yksi postaus kerrallaan voi olla sopiva tahti edetä." %}

Muotoillaan seuraavaksi sovelluksen runko valmiiksi.

Pääset siirtymään vaiheiden välillä alapuolella olevista linkeistä 👇

[1]: /assets/images/tehdaan-wpf-sovellus/ketuttaako-header.jpg