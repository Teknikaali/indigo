---
title: "Tehdään WPF-sovellus - 21 - Ystävämme MVVM"
image: assets/images/tehdaan-wpf-sovellus/ketuttaako-header.jpg
layout: post
date: 2021-05-16 15:30
tag:
- WPF
category: blog
author: anssikettunen
description: Opas kuinka tehdä Windows-työpöytäsovellus käyttäen WPF, XAML ja C#. Tässä osassa muutetaan sovellus mukailemaan MVVM-tyyliä.
---

## Pääikkunan ViewModel vastaa navigoinnista

Tavoitteenamme on lisätä jokaisen näkymämme taustalle ViewModel hoitamaan asioita, jotta näkymän itse ei tarvitsisi tietää sovelluksemme taustatoiminnasta oikeastaan mitään.

Aloitetaan luomalla pääikkunallemme oma ViewModel lisäämällä uusi luokka `MainViewModel` omaan tiedostoonsa:

```csharp
namespace Kettunen.BMICalculator.WPFClient
{
    public class MainViewModel
    {

    }
}
```
<figcaption>MainViewModel.cs</figcaption>

{% include note.html content='Muista lisätä `public`-sana luokan esittelyn eteen, jotta se näkyisi muillekin luokille avoimesti.' %}

Tämä luokka tulee hoitamaan pääikkunamme toiminnallisuuden: navigoinnin syötekenttä- ja tulosnäkymän välillä. Ei unohdeta myöskään painikkeen tekstin vaihtamista näkymän muuttuessa.

Jäsennellään luokka ensin näiden tarpeiden perusteella ja lähdetään siitä sitten parantelemaan toteutusta tarpeidemme mukaisesti.

{% include note.html content='Kokeilin muutamaa eri tyyliä toteuttaa sovelluksemme navigointi, mutta valitsemani tapa vaikutti yksinkertaisimmalta tavalta. Ehkä myöhemmin voimme palata tähän ja katsoa, josko jotain voisi tehdä paremmin.' %}

## Painikkeeseen reagoidaan komennolla

WPF:ssä painikkeille on perinteistä, että ne _bindataan_ ViewModelilla olevaan _komentoon_. 

Komennot ovat `ICommand`-rajapinnasta periytettyjä luokkia. Niiden tehtävä on tarjota suoritettava toiminto ja tarkastelu, että voiko kyseistä toimintoa suorittaa nykyisessä tilassa.

{% include note.html content='Kolmannen osapuolen kirjastoissa `ICommand`-rajapinta on yleensä toteutettu `RelayCommand`-, `DelegateCommand`- tai `ReactiveCommand`-nimisillä toteutuksilla. Teemme oman toteutuksen oppimismielessä hetken päästä, kun se tulee oleelliseksi 👌' %}

Lisätään `ICommand`-tyyppinen "Navigate"-property MainViewModelille, joka voidaan bindata Viewin puolella seuraavasti:

```diff
+using System.Windows.Input;

namespace Kettunen.BMICalculator.WPFClient
{
    public class MainViewModel
    {
+        public ICommand Navigate { get; }
    }
}
```
<figcaption>MainViewModel.cs</figcaption>

Meidän tarvitsee kertoa näkymälle ensiksi, että minkä tyyppinen data sillä on taustallaan, eli `DataContext`-propertyssään. Tämä onnistuu käyttämällä `d:DataContext`-attribuuttia pääikkunan esittelyssä:

```xml
<Window ...
        d:DataContext="{d:DesignInstance Type={x:Type local:MainViewModel},
                                         IsDesignTimeCreatable=True}"
        ... >
```
<figcaption>MainWindow.xaml</figcaption>

`d`-nimiavaruudella kerromme, että emme ole oikeasti asettamassa pääikkunan DataContextia, vaan haluamme suunnittelun aikana käyttää kyseisen tyypin tietoja hyödyksemme. Tämä mahdollistaa sen, että VS:n IntelliSense poimii taustalla olevan ViewModelin propertyt ja meidän on helpompi luoda bindauksia näkymän puolella.

`IsDesignTimeCreatable=true` kertoo sen, että kun Visual Studion designer-näkymä lataa näkymän, niin se voi luoda kyseisestä ViewModelista taustalla uuden instanssin käyttämällä luokan oletusrakentajaa. Tässä tapauksessa designer kutsuu `new MainViewModel()` avautuessaan ja asettaa tämän instanssin DataContextikseen.

{% include note.html content='Jos näet `d`-symbolin alla aaltoviivoja, voit hoveroida sitä ja katsoa, että mikä on vialla. Tässä vaiheessa on hyvä kääntää sovellus, jotta MainViewModel osataan lukea designerin puolella.' %}

Nyt meidän on helpompi tehdä painikkeen bindaus komentoon:

```xml
<Button Grid.Row="1"
        Command="{Binding Navigate}">
    <TextBlock Style="{x:Null}"
                Text="CALCULATE" />
</Button>
```
<figcaption>MainWindow.xaml</figcaption>

Nyt jos klikkaat kursorin `Navigate`-tekstin päälle ja painat `F12`, niin editorin pitäisi viedä sinut suoraan ViewModelin puolelle. Jes, toimii!

## Painikkeelle vaihtuva teksti bindaamalla

Haluamme, että painikkeen teksti vaihtuu sanaan "BACK", kun tulosten näkymä näytetään.
Bindataan siis painikkeen sisällä oleva tekstilaatikon teksti vastaavasti kuin bindasimme komennon painikkeelle:

```diff
<TextBlock Style="{x:Null}"
-           Text="CALCULATE" />
+           Text="{Binding NavigateText}" />
```
<figcaption>MainWindow.xaml</figcaption>

Mutta voi kurjaa. Bindaus ei onnnistukaan! Hoveroimalla näemme, että mikä mättää:

![][1]{: .center-image }
<figcaption class="caption">Bindaus ei onnistu - MainWindow.xaml.</figcaption>

Tämä oli kyllä ihan odotettuakin, sillä emme ole lisänneet vielä `NavigateText`-propertyä ViewModelin puolelle. Tekeminen on aikalailla Viewin ja ViewModelin välillä hyppimistä.

Annetaan nappulan tekstin olla vielä pelkkä "CALCULATE" tässä vaiheessa. Lisätään property ViewModelille:

```csharp
public class MainViewModel
{
    public ICommand Navigate { get; }

    public string NavigateText => "CALCULATE";
}
```
<figcaption>MainViewModel.cs</figcaption>

{% include note.html content='`=>`-symbolia käytettäessä puhutaan _lambdasta_. Käytännössä sillä korvataan tässä turhan verboosi `get`-avainsanan käyttö. Lisää aiheesta [täällä](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/operators/lambda-expressions).' %}

Mutta voi kurjuuden kurjuus! Nyt kun käynnistämme sovelluksemme, niin vastassa onkin tyhjääkin tyhjempi painikkeen teksti:

![][2]{: .center-image }
<figcaption class="caption">Tyhjääkin tyhjempi painike.</figcaption>

Tämä johtuu siitä, että pääikkunamme `DataContext` on vielä täysin tyhjä. Korjatkaamme ongelma!

Koska olemme olleet nokkelia sovelluksemme käynnistyksen kanssa, niin voimme käydä vain luomassa uuden `MainViewModelin` instanssin `OnStartup`-funktiossamme ja asettamassa tämän ViewModelin pääikkunan DataContextiksi tähän tapaan:

```csharp
var mainViewModel = new MainViewModel();
var mainWindow = new MainWindow
{
    DataContext = mainViewModel
};
mainWindow.Show();
```
<figcaption>App.xaml.cs</figcaption>

Noin! Nyt painikkeellakin näkyy teksti mukavasti.

Seuraavassa osassa luodaan ViewModeleillemme kantaluokka, joka helpottaa huomattavasti propertyjen ja näkymän päivittymisen kanssa.


[1]: /assets/images/tehdaan-wpf-sovellus/21-01.jpg
[2]: /assets/images/tehdaan-wpf-sovellus/21-02.jpg