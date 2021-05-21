---
title: "Tehdään WPF-sovellus - 26 - Korjataan navigoinnin puutteet"
image: assets/images/tehdaan-wpf-sovellus/ketuttaako-header.jpg
layout: post
date: 2021-05-21 18:30
tag:
- WPF
category: blog
author: anssikettunen
description: Opas kuinka tehdä Windows-työpöytäsovellus käyttäen WPF, XAML ja C#. Tässä osassa muutetaan sovellus mukailemaan MVVM-tyyliä.
---

## ViewModel päättää painikkeella näytettävän tekstin

Edellisessä osassa listasimme neljä ongelmakohtaa. Yksi näistä oli se, että painikkeen teksti ei muutu, vaikka navigoimmekin toiseen näkymään. Laitetaan korjauslasit päähän ja käydään hommiin!

`MainViewModel.CurrentViewModel`-propertyn arvo vaihtuu siihen ViewModeliin, joka näytetään käyttäjälle. Käytetään tätä tietoa hyväksemme päivittämällä `NavigateText`-property oikeaan arvoon aina kun `CurrentViewModel` vaihtuu.

```csharp
public class MainViewModel : ViewModel
{
    // ...
    
    public string NavigateText => CurrentViewModel is InputViewModel
            ? "CALCULATE"
            : "BACK";
    
    // ...
```
<figcaption>MainViewModel.cs</figcaption>

Tässä kohtaa herää kysymys, että olisiko meidän sittenkin pitänyt antaa eri näkymien vastuulle koko ikkunan toiminnot, jotta olisimme voineet välttää tällaiset `is`-avainsanan käyttämiset. Käytännössä `InputViewModel` olisi voinut tarjota oman `NavigateText`-propertynsä, jonka myötä voisimme asettaa `NavigateText`-propertyn näin:

```csharp
public string NavigateText => CurrentViewModel.NavigateText;
```
<figcaption>MainViewModel.cs</figcaption>

{% include note.html content='Usein kun eteen tulee `is`-avainsanan käyttöä, niin koodi saattaa olla vaikeammin testattavissa kuin koodi, jossa `is`-sanoja ei ole. No, pidetään nyt toistaiseksi tämä asia mielessä. Ehkä muokkaamme koodia myöhemmin tähän suuntaan.' %}

Nyt jos koitat suorittaa ja navigoida seuraavaan näkymään, niin painikkeen teksti ei ole kuitenkaan muuttunut mihinkään. Emme ole kertoneet käyttöliittymälle, että sen tulisi päivittää `NavigateText`-arvoa, kun `CurrentViewModel` muuttuu.

Käytetään hyödyksemme `SetProperty`:n `bool`-paluuarvoa. Tämä kertoo siis sen, että onko arvo oikeasti muuttunut. Voimme tämän perusteella kertoa UI:lle, että tässä tilanteessa pitäisi päivittää myös `NavigateText`-propertyn arvo:

```csharp
    // ...

    if (SetProperty(ref _currentViewModel, value))
    {
        OnPropertyChanged(nameof(NavigateText));
    }

    // ...
```
<figcaption>MainViewModel.cs</figcaption>

Siistiä, nyt toimii! Napataan listalta seuraava ongelmakohta: tulosnäkymältä ei pääse palaamaan takaisin syötekenttien näkymälle.

## Komennolle voi Bindata parametrin

Nykyisellään `Navigate`-komentomme ei tiedä, että mille näkymälle sen tulisi navigoida. Olemme asettaneet komennon navigoimaan aina tulosnäkymälle. Voimme bindata komennolle parametrin syötettäväksi XAML-puolelta. Käytännössä tämä tarkoittaa sitä, että syötämme omavalinnaisen parametrimme suoraan execute-toiminnolle.

Tehdään tämä siten, että komento ottaa aina nykyisen `CurrentViewModel`:n arvon sisäänsä ja päättelee siitä, että minne sen tulisi ohjata käyttäjä seuraavaksi. Bindataan painikkeen `CommandParameter`-arvoksi siis `CurrentViewModel` päänäkymällä:

```xml
<Button ...
        CommandParameter="{Binding CurrentViewModel}">
    ...
</Button>
```
<figcaption>MainView.xaml</figcaption>

Suorita ohjelma ja pistä breakpoint riville, jossa komento kutsuu `CurrentViewModel = _resultViewModel;`.
Tarkastelemalla lambdamme parametria (toistaiseksi vielä "`_`") voimme huomata, että parametrin bindaus on onnistunut hienosti:

![][1]{: .center-image }
<figcaption class="caption">Komennon parametri vastaanotettu onnistuneesti!</figcaption>

Koska nyt vastaanotamme parametrin, niin muutetaan ensitöiksemme `_` -> `x`:
```csharp
x =>
{
    CurrentViewModel = _resultViewModel;
},
```
<figcaption>MainViewModel.cs</figcaption>

`x` on tyypiltään `object`. Tämä johtuu siitä, että aiemmin määrittelemämme `DelegateCommand` sanoo rakentajassaan näin:

```csharp
public DelegateCommand(Action<object> execute, Predicate<object> canExecute)
```
<figcaption>DelegateCommand.cs</figcaption>

Emme ole siis hyödyntäneet tässä samaa strategiaa kuin `SetProperty<T>`-funktion kanssa.

{% include note.html content='Voit tässä kohtaa harjoitella `DelegateCommandin` muuttamista geneeriseksi luokaksi. Valmiita toteutuksia löytyy [myös](https://stackoverflow.com/questions/6273002/generic-type-safe-icommand-implementation).' %}

Lisätään seuraava logiikka näkymän muuttamiseksi `execute`-lambdaamme hyödyntäen [`is`-avainsanaa](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/operators/is):

```csharp
x =>
{
    if (x is InputViewModel inputViewModel)
    {
        CurrentViewModel = _resultViewModel;
    }
    else
    {
        CurrentViewModel = _inputViewModel;
    }
},
```
<figcaption>MainViewModel.cs</figcaption>

Tarkistamme siis, että onko vastaanottamamme parametri (näkymä, jossa olemme tällä hetkellä) painon ja pituuden syötenäkymä.
Jos on, niin vaihdamme näkymää asettamalla `CurrentViewModel`-propertyyn tulosten ViewModelin.
Jos ei ole, niin olemme oletettavasti tulosnäkymällä ja voimme vaihtaa takaisin syötenäkymälle.

## Muutoksemme vaikuttavat lupaavilta

![][2]{: .center-image }
<figcaption class="caption">Navigointi toiminnassa.</figcaption>

{% include note.html content='Käytin tämän animaation tekemiseen [ScreenToGif](https://www.screentogif.com/)-sovellusta.' %}

Tämän perusteella on aika selvää, että mitä meidän tulee korjata seuraavaksi: painoindeksin laskenta.

Navigointi onnistuu myös virheellisesti tulosnäkymälle siten, että emme ole syöttäneet painolle tai pituudelle arvoja ollenkaan!

Korjataan nämä ongelmat seuraavassa osassa.

[1]: /assets/images/tehdaan-wpf-sovellus/26-01.jpg
[2]: /assets/images/tehdaan-wpf-sovellus/26-02.gif