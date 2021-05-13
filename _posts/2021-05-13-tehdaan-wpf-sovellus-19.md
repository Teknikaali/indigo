---
title: "Tehdään WPF-sovellus - 19 - Pohja MVVM-toteutukselle"
image: assets/images/tehdaan-wpf-sovellus/ketuttaako-header.jpg
layout: post
date: 2021-05-13 20:00
tag:
- WPF
category: blog
author: anssikettunen
description: Opas kuinka tehdä Windows-työpöytäsovellus käyttäen WPF, XAML ja C#. Tässä osassa muutetaan sovellus mukailemaan MVVM-tyyliä.
---

## Suunitellaan tulosnäkymä uudelleen

Jotta MVVM:stä olisi edes hieman oikeaa hyötyä, voisimme lisätä sovellukseemme paremman tulosten näyttämisen näkymän. Nykyinen tulosten esitystapa on muutenkin luvattoman karun näkönen verrattuna sovelluksemme uuteen uhkeaan tyyliin.

![][1]{: .center-image }
<figcaption class="caption">Sovellus näyttää tulokset harmaassa viestilaatikossa. Hyi.</figcaption>

Lisätään siis sovelluksen tyyliä kunnioittava tulosten esityssivu, joka näytetään klikkaamalla "CALCULATE"-painiketta. Varmistetaan myös, että tulosten näkymästä pääsee palaamaan takaisin aloitusnäkymään syöttämään uusia arvoja "BACK"-painikkeella.

![][2]{: .center-image }
<figcaption class="caption">Tulokset voitaisiin näyttää näin.</figcaption>

## Jaetaan sovellus osiin

Jaetaan näkymä samalla myös loogisiin osiin. Tässä kohtaa on hyvä pähkäillä, että "kenelle" näkymillä olevat elementit tulisi kuulua:
* Pitäisikö painikkeen toiminnon olla näytettävän sisällön vastuulla?
* Kuinka moneen osaan näkymät pitäisi jakaa? Onko yksi tarpeeksi, vai pitäisikö pilkkoa vieläkin pienempiin osiin?
* Mikä toiminnallisuus jää pääikkunan huoleksi?
* Kuinka tietämättömiä näkymien pitäisi olla toisistaan? Tarvitseeko tulosnäkymän tietää syötekenttien näkymästä mitään?

Ratkaisemme ongelman jakamalla näkymän selkeästi kahteen eri osaan: ylempi alue on sisällölle ja alempi pääikkunalle ja toiminto-/navigointipainikkeelle.

{% include note.html content='En pitäisi aivan hulluna ajatuksena, jos painikkeen toiminnallisuuden antaisi sisällön tehtäväksi pääikkunan sijaan näin pienessä sovelluksessa.' %}

![][3]{: .center-image }
<figcaption class="caption">Sovellus jaettu loogisiin osiin.</figcaption>

{% include note.html content='ViewModel-tyyppisten luokkien nimien perään on yleistä lisätä joko sana "ViewModel" kokonaisuudessaan (esim. MainViewModel), tai sitten "VM" (esim. MainVM). Samaa tyyliä on hyvä noudattaa läpi ohjelman.' %}

## Sovelluksen purkaminen osiin on suoraviivainen toimenpide

Rakensimme sovelluksemme näkymän jo suhteellisen erillisistä komponenteista, joten pilkkomisen pitäisi mennä suht kivuttomasti. 

{% include note.html content='Pieni varoituksen sana tähän väliin ennen kuin aloitamme: Sovellus tulee olemaan hieman vajavaisessa tilassa, kunnes olemme saaneet operaation päätökseen. Älä anna tämän häiritä liikaa. Kyllä se siitä! Tämä on itseasiassa yksi niistä vaiheista, jossa on hyvä saada työskennellä rauhassa häiriöttä.' %}

Aloitetaan brutaali pilkkominen luomalla seuraaville palasille omat Viewit ja ViewModelit:
* Sovelluksen pääikkunan näkymä (MainWindow <-> MainViewModel)
* Painon ja pituuden syöttämisen näkymä (InputView <-> InputViewModel)
* Tuloksen näyttämisen näkymä (ResultView <-> ResultViewModel)

Lisätään ensin uudet näkymät ja järjestellään niiden sisällöt kohdilleen.
Toteutetaan näkymät `UserControl`-tyyppisinä luokkina.

{% include note.html content='Uuden `UserControl` lisääminen: `Project -> Add User Control (WPF)...` tai `Solution Explorer -> Projektin kontekstivalikko -> Add ->  User Control (WPF)...`' %}

1) Annetaan uudelle syötekenttien näkymälle nimeksi `InputView`

2) Tehdään ensimmäinen leikkauksemme siirtämällä `MainWindow`:lta painon ja pituuden elementtikokonaisuudet `InputViewille`

3) Lisätään `InputViewin` ruudukolle kaksi `RowDefinition`-elementtiä, jotka jäivät jälkeen pääikkunan määrittelyyn

Tässä listauksessa lyhennelmä `InputView.xaml` sisällöstä operaation jälkeen:

```xml
<UserControl>
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition />
            <RowDefinition />
        </Grid.RowDefinitions>

        <!--  Weight  -->
        <Border>
            <!-- ... -->
        </Border>

        <!--  Height  -->
        <Border>
            <!-- ... -->
        </Border>
    </Grid>
</UserControl>
```
<figcaption>InputView.xaml</figcaption>

`InputView.xaml`-tiedoston Designer-näkymän pitäisi näyttää onnistuneen palastelun jälkeen tältä:

![][4]{: .center-image }
<figcaption class="caption">InputView.xaml Designer.</figcaption>

Emme myöskään tarvitse pääikkunan näkymällä enää ruudukolla kolmea riviä, joten voimme pudottaa yhden pois ja säätää tilankäytön vastaamaan muutoksia.

Pääikkunan `MainWindow.xaml`-tiedoston tulisi näyttää tältä:

```xml
<Window>
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="4*" />
            <RowDefinition />
        </Grid.RowDefinitions>

        <Button Grid.Row="1"
                Click="Button_Click">
            <TextBlock Style="{x:Null}"
                       Text="CALCULATE" />
        </Button>

    </Grid>
</Window>
```
<figcaption>MainWindow.xaml</figcaption>

Huomaa erityisesti seuraavat asiat:
* Yksi `RowDefinition`-elementti on poistettu
* Ensimmäisen rivin `Height` on muutettu 2 -> 4
* Painikkeen `Grid.Row` on muutettu 2 -> 1

## Korjataan virheet poistamalla koodia

Visual Studion pitäisi tässä vaiheessa antaa kaksi virheilmoitusta `WeightInput`- ja `HeightInput`-kenttien puuttumisesta.

{% include note.html content='Jos virheilmoituksia ei näy, niin käännä ohjelma uudelleen (`Build -> Rebuild Solution`).' %}

{% include note.html content='Sinänsä emme enää tarvitsisi nimettyjä elementtejä ollenkaan, mutta eipä niistä haittaakaan ole. Jätetään syötekenttien nimet rauhaan tällä erää.' %}

Korjataan ongelma tylysti vain poistamalla pääikkunan `MainWindow.xaml.cs`-taustatiedostosta koko `Button_Click`-tapahtuman käsittely ja tehdään sama itse näkymän puolella.

```diff
-<Button Grid.Row="1"
-        Click="Button_Click">
+<Button Grid.Row="1">
```
<figcaption>MainWindow.xaml</figcaption>

{% include note.html content='Tiedostossa olevat turhat harmaalla värillä merkatut nimiavaruudet voit putsata kätevästi oikean napin kontekstivalikosta löytyvällä `Remove and Sort Usings`-komennolla.' %}

![][5]{: .center-image }
<figcaption class="caption">Tyhjä pääikkuna - MainWindow.xaml.</figcaption>

Seuraavassa osassa lisätään tulosten näyttämiseksi uusi näkymä (ResultView) ja tunkataan sovellus muodostamaan ja käynnistämään pääikkunansa tarpeisiimme sopivammalla tavalla.

Kohta pääsemme luomaan ensimmäiset ViewModelimme!

[1]: /assets/images/tehdaan-wpf-sovellus/19-01.jpg
[2]: /assets/images/tehdaan-wpf-sovellus/19-02.jpg
[3]: /assets/images/tehdaan-wpf-sovellus/19-03.jpg
[4]: /assets/images/tehdaan-wpf-sovellus/19-04.jpg
[5]: /assets/images/tehdaan-wpf-sovellus/19-05.jpg