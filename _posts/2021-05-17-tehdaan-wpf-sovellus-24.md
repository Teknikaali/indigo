---
title: "Tehdään WPF-sovellus - 24 - Lisää ViewModeleita"
image: assets/images/tehdaan-wpf-sovellus/ketuttaako-header.jpg
layout: post
date: 2021-05-17 20:00
tag:
- WPF
category: blog
author: anssikettunen
description: Opas kuinka tehdä Windows-työpöytäsovellus käyttäen WPF, XAML ja C#. Tässä osassa muutetaan sovellus mukailemaan MVVM-tyyliä.
---

## Infra ei häiritse ViewModelien luontia

Olemme tehneet nyt suurimman osan taustatyöstä, jotta pääsisimme toteuttamaan näkymien taustalla olevia ViewModel-luokkia kätevästi huolehtimatta niitä ympäröivästä infrastruktuurista.

Emme voi kovin paljoa tehdä pääikkunan ViewModelin puolella ennen kuin meillä on navigoitavien näkymien ViewModelit käsissämme.

Aloitetaan siis lisäämällä `InputViewModel` ja `ResultViewModel`, jotka perivät ViewModel-kantaluokkamme.

### InputViewModel

InputViewModel on pääikkunalla näytettävä painon ja pituuden syötekenttien näkymä.
Tarvitsemme siis näitä syötekenttiä vastaavat propertyt tälle ViewModelille.

Määritellään ensin luokka uuteen tiedostoon:

```csharp
using Kettunen.BMICalculator.WPFClient.MVVM;

namespace Kettunen.BMICalculator.WPFClient
{
    public class InputViewModel : ViewModel
    {

    }
}

```
<figcaption>InputViewModel.cs</figcaption>

Lisätään tämän jälkeen backing fieldit ja itse property tähän tapaan sekä painolle että pituudelle:

```csharp
private double _weight;
public double Weight
{
    get => _weight;
    set => SetProperty(ref _weight, value);
}
```
<figcaption>InputViewModel.cs</figcaption>

{% include note.html content='Huomioi tässä miten käytämme `ViewModel`-kantaluokan `SetProperty`-funktiota hyödyksi setterissä.' %}

Tee sama `Height`-arvolle ja lopputuloksen pitäisi olla tämä:

```csharp
using Kettunen.BMICalculator.WPFClient.MVVM;

namespace Kettunen.BMICalculator.WPFClient
{
    public class InputViewModel : ViewModel
    {
        private double _weight;
        public double Weight
        {
            get => _weight;
            set => SetProperty(ref _weight, value);
        }

        private double _height;
        public double Height
        {
            get => _height;
            set => SetProperty(ref _height, value);
        }
    }
}
```
<figcaption>InputViewModel.cs</figcaption>

### ResultViewModel

ResultViewModel ei paljoa vaadi, joten näytän sen vain tässä kokonaisuutena ilman höpinöitä:

```csharp
using Kettunen.BMICalculator.WPFClient.MVVM;

namespace Kettunen.BMICalculator.WPFClient
{
    public class ResultViewModel : ViewModel
    {
        private double _result;
        public double Result 
        {
            get => _result;
            set => SetProperty(ref _result, value);
        }
    }
}
```
<figcaption>InputViewModel.cs</figcaption>

## Viewien DataContextit kuntoon

Koska meillä on nyt käsissämme kahdelle näkymälle ViewModelit, niin käydään myös päivittämässä näiden näkymien `d:DataContext`-arvot kohdilleen:

```xml
<UserControl ...
             d:DataContext="{d:DesignInstance Type={x:Type local:InputViewModel}"
             ...
```
<figcaption>InputView.xaml</figcaption>

```xml
<UserControl ...
             d:DataContext="{d:DesignInstance Type={x:Type local:ResultViewModel},
                                              IsDesignTimeCreatable=True}"
             ...
```
<figcaption>ResultView.xaml</figcaption>

{% include note.html content='Huom: Määritetään ResultViewille `IsDesignTimeCreatable=True`, jotta voimme kokeilla toista tapaa alustaa suunnittelunaikainen tieto näkymälle.' %}

## Tuloksen bindaus ViewModelin propertyyn

Olemme aiemmin asettaneet tulosnäkymämme arvon oletuksena "123". Jos laskurimme antaisi jokaiselle syötetylle arvolle saman tuloksen, olisi kyseessä vain aika huono laskuri.

Bindataan siis tuloksen tekstikenttä ViewModelin `Result`-arvoon:

```xml
<TextBlock Grid.Row="1"
           Text="{Binding Result}" />
```
<figcaption>ResultView.xaml</figcaption>

Design-näkymämme arvo on vaan ehkä hieman ankeasti nyt "0". Tälle täytyy tehdä jotain!

![][1]{: .center-image }
<figcaption class="caption">Design-näkymä - ResultView.xaml.</figcaption>

## Design-arvon voi määrittää myös ViewModelin puolella

{% include note.html content='Tämä on vain "hyvä tietää"-asiaa. Lue myös seuraavat muut 💡-viestit.' %}

Aiemmin annoimme `d:Text`-attribuutille arvon, joka näytettäisiin suunnittelun aikaisena tietona näkymällä (kts. `InputView.xaml`).

Voimme kuitenkin toteuttaa tämän toisellakin tapaa: suunnittelunaikainen data on myös saatavilla ViewModelin kautta, koska Designer-näkymä _alustaa ihan oikean instanssin_ ViewModelista latautuessaan, jos sen `IsDesignTimeCreatable=True`.

Meidän olisi syytä kuitenkin varmistua jotenkin siitä, ettei Designer-näkymä yritä esimerkiksi käynnistää tietokantayhteyksiä, tai suorittaa jotain kauan kestäviä operaatioita ihan vain sen takia, että se yrittää vain ladata ViewModeliamme esittääkseen sen.

{% include note.html content='Riippuen ViewModelin alustuksen logiikasta niin joskus saattaa törmätä tilanteeseen, jossa ViewModelin alustus on erittäin raskas operaatio. Tällöin olisi suotavaa olla kevyempi tapa alustaa ViewModel Designeria varten.' %}

### ViewModelille uusi property

Käydään lisäämässä `ViewModel`-kantaluokallemme uusi property `IsInDesignMode`. Tämän propertyn tarkoituksena on kertoa, että suoritetaanko koodia Designerin toimesta, vai ihan oikeasti sovellusta suorittamalla.

```csharp
using System.Windows;
    ...
    public abstract class ViewModel : INotifyPropertyChanged
    {
        public static bool IsInDesignMode => DesignerProperties.GetIsInDesignMode(new DependencyObject());
    ...
```
<figcaption>ViewModel.cs</figcaption>

{% include note.html content='Huomaa, että lisäämämme property on tyypiltään [`static`](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/static). Tämä tarkoittaa sitä, että kyseistä arvoa voi kysyä, vaikkei olisi alustanutkaan luokkaa `new`-avainsanalla.' %}

Nyt voimme käydä lisäämässä `ResultViewModelille` rakentajan, jossa pääsemme hyödyntämään uutta propertyamme:

```csharp
public ResultViewModel()
{
    if (IsInDesignMode)
    {
        Result = 123;
    }
}
```
<figcaption>ResultViewModel.cs</figcaption>

Kun olet kääntänyt ohjelman pitäisi Design-näkymässä näkyä antamasi arvo.

{% include note.html content='Olisi parempi yrittää pärjätä mahdollisimman pitkään XAML-puolella DesignDatan kanssa vrt. ViewModelin `IsInDesignMode`-propertyn käyttäminen. [Tässä](https://docs.microsoft.com/en-us/visualstudio/xaml-tools/xaml-designtime-data) muutamia tapoja, joilla tietoa voi esittää ilman ViewModel-tasolle menemistä.' %}

{% include note.html content='Käytännössä tämä mahdollistaa sellaisten suunnittelunaikaisten tietojen rakentamisen, joka saattaisi olla turhankin hankaa toteuttaa XAML:n puolella. Suosittelen käyttämään `d:`-notaatiota XAML-puolella esimerkkidatan tuottamiseen aina kun se on vain mahdollista ja jättämällä `IsDesignTimeCreatable`-arvon asettamatta `d:DataContexti`:a määritettäessä.' %}

## Seuraavaksi toteutetaan navigointi

Voimme seuraavassa osassa keskittyä toteuttamaan navigointia näiden näkymien välillä nyt kun työkalupakistamme löytyy sopiva määrä ViewModeleita ja Viewejä.

[1]: /assets/images/tehdaan-wpf-sovellus/24-01.jpg