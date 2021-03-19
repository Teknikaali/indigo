---
hidden: true
title: "Tehdään Windows-työpöytäsovellus - Osa 1 - Alkupalat"
layout: post
date: 2021-03-19 18:00
headerImage: false
tag:
- WPF
star: true
category: blog
author: anssikettunen
description: Opas Windows-työpöytäsovelluksen tekemiseen WPF:llä
---

## Alkupalat

Jos haluat pienen maistiaisen Windows-työpöytäsovellusten luonnin maailmasta C#:lla ja WPF:llä, on tämä erinomainen mahdollisuus juuri sinulle!

Olen yrittänyt pitää asiat mahdollisimman yksinkertaisella tasolla ja selittää vastaan tulevat uudet konseptit korkealla tasolla paneutumatta niihin liian syvällisesti.

Teen sen olettamuksen, että olet ehkä kokeillut jo jotain ohjelmointiin liittyvää ja hallitset tietokoneen käytön.

Tässä luotua sovellusta voidaan käyttää hyvänä pohjana jatkokehitykselle myöhemmin. Tarkoitus on, että saisit tämän avulla tehtyä jotain valmista alusta loppuun!

Koodimuutokset on merkattu näin:

```diff
Tätä riviä ei muokata.
- Tätä riviä muokataan.
+ Näin riviä on muokattu.
```

Ennen muokkausta:
```
Tätä riviä ei muokata.
Tämä riviä muokataan.
```

Muokkauksen jälkeen:
```
Tätä riviä ei muokata.
Näin riviä on muokattu.
```

### WPF - Windows Presentation Framework

Toteutamme sovelluksemme WPF-käyttöliittymän [XAML](https://docs.microsoft.com/en-us/dotnet/desktop/wpf/advanced/xaml-overview) -markup-kielellä ja lisäämme mausteeksi sekaan hyppysellisen C#:ia.

Teemme tämän esimerkkisovelluksen UI edellä, eli keskitymme ensin käyttöliittymän muovaamiseen ja sitten vasta toiminnallisuuden toteuttamiseen. WPF:llä pystyy helposti toteuttamaan ohjelman toiminnallisuuden erillään käyttöliittymästä ja tähän tulisikin pyrkiä. Tämä mahdollistaa vaikka sen, että yksi henkilö voi keskittyä täysin hienoon ulkoasuun ja käytettävyyteen ja toinen voi keskittyä täysin tekemään itse toiminnallista osuutta ohjelmasta.

Eiköhän aloiteta. Onnea matkaan! 👍

---

## Määrittely

Ensimmäisenä pitää määritellä mitä haluamme sovelluksen pääpiirteittäin tekevän. 

💡 Sovelluksia on helpompi luoda, kun on määritellyt selkeät tavoitteet ja rajat sovellukselle ja itselleen.

Sovellus voisi vaikkapa antaa vastauksen kysymykseen "Mikä on painoindeksi annetulle pituudelle ja painolle?". Yksinkertaisuuden nimissä luomme siis painoindeksin laskemiseen käytettävän sovelluksen seuraavin vaatimuksin:
* Sovellus laskee antamieni tietojen pohjalta painoindeksin
* Voin tarkastella tuloksia

Näitä määrittelyjä voidaan myös tarkentaa tai laajentaa myöhemmin, kun opimme sovelluksesta ja tarpeistamme enemmän. Ei oteta liian suurta haukkausta heti alkuunsa.

## Esivaatimukset

* Lataa ja asenna [Visual Studio 2019 (Community)](https://visualstudio.microsoft.com/downloads/)
    * Varmista asennuksen `Workloads`-kohdassa, että `.NET desktop development` on valittuna
* Käy vilkaisemassa [painoindeksin määritelmä](https://fi.wikipedia.org/wiki/Painoindeksi)
* Varaa itsellesi hetki aikaa
    * Ehkä myös lämmin kupponen juotavaa
    * Mahdollisesti iso pehmeä tyyny on myös hyvä varuste (oman harkinnan mukaan)

💡 Voit myös pilkkoa tekemisen useammalle eri kerralle. Kaikkea ei ole pakko rykäistä kerralla valmiiksi. Olen sijoittanut tämänkin virkkeen alapuolella näkyviä jakajia mahdollisesti hyviin taukokohtiin.