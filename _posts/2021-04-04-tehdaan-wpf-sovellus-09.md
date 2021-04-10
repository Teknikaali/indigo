---
title: "Tehdään WPF-sovellus - 9 - Karkkia silmille"
image: assets/images/tehdaan-wpf-sovellus/ketuttaako-header.jpg
layout: post
date: 2021-04-04 20:00
tag:
- WPF
category: blog
author: anssikettunen
description: Opas kuinka tehdä Windows-työpöytäsovellus käyttäen WPF, XAML ja C#. Tässä osassa tyylitellään sovellus näyttämään hienolta.
---

Edellisissä osissa saimme sovelluksen siihen kuntoon, että sitä pystyi jo käyttämään painoindeksin laskentaan.

Tämmöinen rujo ulkoasu ei kuitenkaan ole kovinkaan houkutteleva, joten on aika korjata asia lisäämällä sovellukselle nätti ulkoasu.

Tehdään sovellukselle seuraavanlainen muodonmuutos:

![Kuva muodonmuutoksesta][1]{: .center-image }
<figcaption class="caption">Ennen ja jälkeen.</figcaption>

En ole itse kaksinenkaan värien ja tyylien tuntija, joten lainasin hieman soveltaen [Ruben Vaaltin suunnittelemaa painoindeksilaskurin tyyliä](https://dribbble.com/shots/4585382-Simple-BMI-Calculator). Voit halutessasi suunnitella mieleisesi tyylin!

Tyylin toteuttamiseksi käydään läpi seuraavia asioita:
* Tyylien määrittäminen
    * Miten ja missä tyylejä voi määritellä
* Triggerit
    * Elementin värin vaihtaminen kursorin siirtyessä elementin päälle
* Suunnittelun aikainen esimerkkidatan hyödyntäminen
    * Helpottaa tyylittelyä, kun näkee etukäteen mitä syötekentässä voisi lukea
* Ruudukkojen jakamissäännöt
    * Järkevöittää elementtien asemointia

Voin jo melkein tässä vaiheessa todeta, että matkalla tulee muutama möykky vastaan. Ei huolta, koska kyllä niistäkin selvitään!

Aloitetaan seuraavassa osassa varsinaisen tyylin tekeminen.

[1]: /assets/images/tehdaan-wpf-sovellus/09-01.jpg