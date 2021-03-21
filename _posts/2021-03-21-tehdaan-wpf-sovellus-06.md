---
title: "Tehdään WPF-sovellus - 6 - Tulosten näyttäminen"
image: assets/images/tehdaan-wpf-sovellus/ketuttaako-header.jpg
layout: post
date: 2021-03-21 17:28
tag:
- WPF
category: blog
author: anssikettunen
description: Opas kuinka tehdä Windows-työpöytäsovellus käyttäen WPF, XAML ja C#.
---

## Ongelma: Tulosta ei näy

Ohjelma on kyllä oikeastaan ihan täysin hyödytön ilman itse tulosten laskentaa. Onhan se ihan mukava syötellä arvoja näihin (vähintäänkin) komeisiin kenttiin, mutta pidemmän päälle homma voi käydä vähän tylsäksi..

Voisimme ratkaista ongelman muutamallakin eri tavalla, mutta valitaan näistä yksi mieluisin tällä kertaa:
1. Lisätään tulokselle oma kenttänsä, joka päivittyy heti kun pituuden tai painon arvo muuttuu
2. Lisätään painike, jota painamalla ohjelma laskee tuloksen ja näyttää sen viestilaatikossa
3. Laskeudutaan lattialle, kääriydytään sikiöasentoon ja huudetaan tuskissamme "Miksi pientä ihmisen lasta kiusataan tämmöisillä ongelmilla?"
    * _Jos varasit itsellesi ison pehmeän tyynyn, niin tämä on oikea aika ottaa se esille_

Valitaan näistä 2. vaihtoehto, koska se on yksinkertaisin tapa edetä. Voimme palata myös 1. vaihtoehtoon myöhemmin, mutta tämän saavuttamiseksi täytyy nähdä enemmän vaivaa.

{% include note.html content="Jos valitsit vaihtoehdon 3, niin suosittelen lämpimästi nousemaan lattialta takaisin koneen ääreen - heti vain kun siltä tuntuu. Kyllä tästä selvitään." %}

Aloitetaan ongelman ratkaiseminen lisäämällä uusi rivi laskentapainikkeelle pääruudukolle ja itse painike.

```diff
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition />
            <RowDefinition />
+            <RowDefinition />
        </Grid.RowDefinitions>
```

```diff
            <TextBox Grid.Row="1"
                     Background="Cornsilk"
                     Foreground="Chocolate" />
        </Grid>

+        <Button Grid.Row="2"
+                Content="Calculate" />
```
<figcaption>MainWindow.xaml</figcaption>

![Laskentapainikke lisätty][1]{: .center-image }
<figcaption class="caption">Laskentapainike lisätty</figcaption>

Ohjelma on nyt valmis ja voit taputtaa itseäsi selk.. hetkinen seis! Eikö vieläkään? Ei aivan vielä.

Emme ole kertoneet ohjelmalle vielä ollenkaan, että mitä painikkeen painamisesta pitäisi tapahtua!

Käyttöliittymä voi reagoida elementtien erilaisiin tapahtumiin määrittämällämme tavalla. Painikkeilla on `Click`-tapahtuma, jota voimme kuunnella lisäämällä `Button`-elementille tapahtumakäsittelijän:

{% include note.html content='Kun olet kirjoittanut "Click", paina `Tab`, jolloin Visual Studio täydentää koodin automaattisesti. Seuraavaksi VS ehdottaa `<New Event Handler>`. Valitse se, jotta tapahtumakäsittelijä lisätään puolestasi automaattisesti määrittelytiedoston taustatiedostoon ("code-behind").' %}

```diff
        <Button Grid.Row="2"
+                Click="Button_Click"
                Content="Calculate" />
```
<figcaption>MainWindow.xaml</figcaption>

Käy kurkkaamassa taustatiedostoa `MainWindow.xaml.cs`. Sinne on tapahtumakäsittelijän lisäyksen yhteydessä lisätty automaattisesti seuraava funktio:

```csharp
private void Button_Click(object sender, RoutedEventArgs e)
{

}
```
<figcaption>MainWindow.xaml.cs</figcaption>

Tämä funktio suoritetaan, kun painiketta painetaan.

{% include note.html content="Voit myös kirjoittaa nämä käsin ilman automatiikan avuja" %}

{% include note.html content="WPF:ssä on suositeltu tapa tehdä painikkeen painamiseen reagointi komentojen avulla hyödyntäen hieman erilaista lähestymistapaa toiminnallisuuden sitomiseksi käyttöliittymään, mutta edetään vielä yksinkertaisuus edellä. Tarkoitus ei ole kuitenkaan tukahduttaa itseämme informaatiähkyllä!" %}

Laitetaan debuggaamiselle pysäytyspiste ensimmäisen aaltosulkeen kohdalle klikkaamalla tiedoston vasemmassa reunassa olevaa saraketta. Punainen täplä (🔴 breakpoint) kertoo, että debugatessamme tähän kohtaan pysähdytään.

* Suoritetaan ohjelma painamalla `F5`
* Klikataan hienoa laskentapainikettamme
* Ohjelman suoritus pysähtyy code-behind-tiedostossa valitsemaamme kohtaan
    * `F5` jatkaa ohjelman suoritusta ja `F10`:llä voit astua yhden koodirivin eteenpäin. Voit poistaa breakpointin klikkaamalla sitä.
* Lopeta lopuksi ohjelman suoritus (`Shift`+`F5`, `Debug->Stop Debugging` tai VS:n yläreunasta 🟥-painike)

![Painiketta on painettu ja ohjelman suoritus on pysäytetty][2]{: .center-image }
<figcaption class="caption">Painiketta on painettu ja ohjelman suoritus on pysäytetty</figcaption>

Nyt voimme olla varmoja, että käyttöliittymämme toimii ainakin tähän asti oikein. Painike ei vieläkään kyllä tee yhtikäs mitään mielenkiintoista, joten on aika korjata asia!

Seuraavaksi laitetaan painike tekemään töitä.

[1]: /assets/images/tehdaan-wpf-sovellus/06-01.png
[2]: /assets/images/tehdaan-wpf-sovellus/06-02.png