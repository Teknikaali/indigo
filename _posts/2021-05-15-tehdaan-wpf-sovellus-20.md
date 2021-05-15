---
title: "Tehdään WPF-sovellus - 20 - Näyttävät tulokset"
image: assets/images/tehdaan-wpf-sovellus/ketuttaako-header.jpg
layout: post
date: 2021-05-15 20:30
tag:
- WPF
category: blog
author: anssikettunen
description: Opas kuinka tehdä Windows-työpöytäsovellus käyttäen WPF, XAML ja C#. Tässä osassa muutetaan sovellus mukailemaan MVVM-tyyliä.
---

## Toteutetaan tulokset uutena näkymänä

Lisätään InputViewin tapaan tuloksille oma näkymänsä: ResultView (UserControl).

Aloitetaan jo hyväksi koetulla tavalla, eli mahdollisimman yksinkertaisella ensimmäisellä toteutuksella.
Korvataan oletuksena kontrollilla oleva `Grid` suoraan `Border`-elementillä, jotta saamme tulokset näkymään mukavasti pyöristetyn suorakulmion sisällä. Jätetään myös tuloksen teksti vielä asettamatta ja näytetään vakioteksti vielä toistaiseksi.

```xml
<UserControl ...>
    <Border Margin="24"
            Style="{StaticResource ItemBorder}">
        <Grid VerticalAlignment="Center">
            <Grid.RowDefinitions>
                <RowDefinition />
                <RowDefinition />
            </Grid.RowDefinitions>
            <TextBlock Text="BMI" />
            <TextBlock Grid.Row="1"
                       Text="123" />
        </Grid>
    </Border>
</UserControl>
```
<figcaption>ResultView.xaml</figcaption>

![][1]{: .center-image }
<figcaption class="caption">Design-näkymä - ResultView.xaml.</figcaption>

{% include note.html content='Jätetään jotain herkkua vielä myöhemmäksi, sillä [käyttämämme designin](https://dribbble.com/shots/4585382/attachments/1036693?mode=media) perusteella tulossivuilla voitaisiin näyttää vaikka mitä lisätietoja ja värejä.' %}

Jos haluat vilkaista miltä juuri luomamme tulossivu näyttää sovelluksessa, niin tee näin:

Näytä tulosnäkymä pääikkunan sisällä tekemällä seuraava lisäys `MainWindow.xaml`-tiedostoon juuri ennen painikkeen elementtiä. Tämän jälkeen voit tarkastella tulosta Design-ikkunassa, tai käynnistämällä ohjelman:

```diff
+<local:ResultView/>

<Button Grid.Row="1">
```
<figcaption>MainWindow.xaml</figcaption>

## Käynnistetään sovelluksemme hallitummin

Nykyisellään käynnistämme sovelluksemme `App.xaml`:lla määritetyllä `StartupUri`-arvolla:

```xml
<Application StartupUri="MainWindow.xaml">
```
<figcaption>App.xaml</figcaption>

Tämä toteutus ei anna meille tarpeeksi vapauksia päättää miten haluaisimme sovelluksemme käynnistyvän. Tavoitteenamme olisi, että sovellus voisi onnistuneesti _vetää itsensä ylös kengännauhoistaan vetämällä_ (bootstrapping).

Kolmannen osapuolen MVVM-totetuksissa on yleensä omat tapansa toteuttaa käynnistys mitä hienoimmilla asetuksilla ja kustomisoinneilla, mutta meille riittää vielä kaikkein yksinkertaisin tapa.

Tehdään siis seuraavat muutokset:

1) Poistetaan edellä mainittu `StartupUri` `App.xaml`-tiedostosta:

```diff
 <Application
-             StartupUri="MainWindow.xaml">
```
<figcaption>App.xaml</figcaption>

2) Lisätään `App.xaml.cs`-tiedostoon paikka pääikkunan käynnistykselle ylikirjoittamalla `App.OnStartup`-funktio:

```csharp
public partial class App : Application
{
    protected override void OnStartup(StartupEventArgs e)
    {
        base.OnStartup(e);
    }
}
```
<figcaption>App.xaml.cs</figcaption>

{% include note.html content='Suosittelen käyttämään Visual Studio tarjoamia IntelliSense-vaihtoehtoja hyödykseen lähes aina kun mahdollista. Esimerkiksi kirjoittamalla "ov" VS tarjoaa vaihtoehtoa "override". Tämän jälkeen kun painaa välilyöntiä, niin VS tarjoaa listan ylikirjoitettavista funktioista.' %}

Lisätään uuden `MainWindow`:n alustus ja näyttäminen juuri ylikirjoittamallemme `OnStartup`-funktiolle:
```csharp
protected override void OnStartup(StartupEventArgs e)
{
    base.OnStartup(e);

    var mainWindow = new MainWindow();
    mainWindow.Show();
}
```
<figcaption>App.xaml.cs</figcaption>

Käytännössä mitä teimme, niin ohitimme `StartupUri`:n määrittämän käynnistyslogiikan, jotta pääsisimme käsiksi aikaan ennen pääikkunan käynnistymistä. Näin ikkunan näyttäminen on paremmin hallinnassamme 👍

Tämän muutoksen jälkeen olisi vielä hyvä hetki kokeilla, että sovellus varmasti käynnistyy vielä tekemiemme muutostenkin jälkeen ennen seuraavaan vaiheeseen siirtymistä!

Useamman jakson verran tässä ollaan sitä jo lupailtu, mutta eiköhän aloiteta ihan oikeasti se itse MVVM:n tykittäminen seuraavassa osassa!

[1]: /assets/images/tehdaan-wpf-sovellus/20-01.jpg