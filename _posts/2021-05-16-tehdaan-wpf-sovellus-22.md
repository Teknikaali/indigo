---
title: "Tehdään WPF-sovellus - 22 - Kantaluokka ViewModeleille"
image: assets/images/tehdaan-wpf-sovellus/ketuttaako-header.jpg
layout: post
date: 2021-05-16 15:30
tag:
- WPF
category: blog
author: anssikettunen
description: Opas kuinka tehdä Windows-työpöytäsovellus käyttäen WPF, XAML ja C#. Tässä osassa muutetaan sovellus mukailemaan MVVM-tyyliä.
---

## Kantaluokka vastaa päivitysten ilmoittamisesta näkymälle

Näkymämme ei osaa päivittyä automaattisesti, ellemme kerro sille eksplisiittisesti, että "nyt tämä ja tämä tieto pitäisi päivittää".

WPF:n näkymän bindaukset osaavat kuunnella `INotifyPropertyChanged`-rajapinnan ilmoituksia propertyjen muutoksista. Emme halua kuitenkaan implementoida kyseistä rajapintaa jokaisella ViewModelillamme erikseen, joten luodaan tätä varten oma kantaluokka, josta muut ViewModelimme voivat periä rajapinnan toteutuksen automaattisesti.

Lisätään Solution-tasolle uusi kansio "MVVM" ja luodaan tänne uusi tiedosto/luokka `ViewModel`

{% include note.html content='"MVVM"-kansiossa olevat tässä luokat tulevat olemaan sellaisia, joita voisi sellaisinaan käyttää myös toisissa WPF-projekteissa.' %}

```csharp
namespace Kettunen.BMICalculator.WPFClient.MVVM
{
    public abstract class ViewModel
    {

    }
}
```
<figcaption>ViewModel.cs</figcaption>

Tehdään luokasta public ja [abstrakti](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/abstract) kantaluokka, koska haluamme aina käyttää ViewModelista jotain konkreettista toteutusta.

{% include note.html content='Emme nimeä abstraktia luokkaamme `AViewModel`, vaikka tämä seuraisikin rajapintojen `IRajapinta`-nimeämistyyliä. Jos projektissasi on muita tekijöitä mukana, niin on aina hyvä sopia yhteisistä pelisäännöistä tämmöisten asioiden suhteen.' %}

Lisätään `INotifyPropertyChanged`-rajapinnan toteutus `ViewModel`-luokallemme:

```diff
-public abstract class ViewModel
+public abstract class ViewModel : INotifyPropertyChanged
```
<figcaption>ViewModel.cs</figcaption>

Hoveroimalla aaltoviivalla alleviivatun "INotifyPropertyChanged"-sanan alla VS tarjoaa kätevän hehkulamppupainikkeen, jolla voi nopeasti ottaa käyttöön tarvitsemansa nimiavaruuden.

![][1]{: .center-image }
<figcaption class="caption">Hehkulamppu auttaa nimiavaruuksien kanssa.</figcaption>

Rajapinnan alla on edelleen aaltoviivaa, joten voimme klikata kursorin sen kohdalle, painaa `Ctrl` + `.` ja valita "Implement interface", jolloin VS lisää rajapinnan toteutuksen vaatimat funktiot ja/tai propertyt luokalle automaattisesti.

```csharp
using System.ComponentModel;

namespace Kettunen.BMICalculator.WPFClient.MVVM
{
    public abstract class ViewModel : INotifyPropertyChanged
    {
        public event PropertyChangedEventHandler PropertyChanged;
    }
}
```
<figcaption>ViewModel.cs</figcaption>

{% include note.html content='`INotifyPropertyChanged`-rajapinnan voi toteuttaa [muutamalla hieman toisistaan eroavalla tavalla](https://stackoverflow.com/questions/1315621/implementing-inotifypropertychanged-does-a-better-way-exist).' %}

Näkymä osaa kuunnella juuri lisäämäämme `PropertyChanged`-tapahtumaa, mutta meiltä puuttuu vielä tapa kutsua sitä.

Tavoitteenamme on tehdä tänne kantaluokalle mahdollisimman yksinkertainen tapa saada ammuttua `PropertyChanged`-tapahtuma, kun perivällä luokalla propertyn arvo muuttuu.

{% include note.html content='[Täältä](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/events/how-to-publish-events-that-conform-to-net-framework-guidelines) voit lukea lisää C#:n tapahtumista.' %}

Lisätään funktio `OnPropertyChanged`, jonka tehtävänä on ampua `PropertyChanged`-ilmoitus ilmoille:

```csharp
protected virtual void OnPropertyChanged(string propertyName = null)
    => PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
```
<figcaption>ViewModel.cs</figcaption>

{% include note.html content='`string propertyName = null` funktion parametrina tarkoittaa sitä, että parametri on optionaalinen: Jos parametria ei anneta, niin arvoksi asetetaan `null`.' %}

## Propertyn arvon päivittäminen SetPropertyllä yksinkertaiseksi

Perusidea propertyjen päivittämisessä on, että kun propertylle annetaan _uusi_ arvo - eli kutsutaan sen setteriä - niin tällöin tästä ammuttaisiin `PropertyChanged`-tapahtuma automaattisesti.

Jos emme lisäisi enää mitään kantaluokalle, niin perivien luokkien tulisi tehdä jotain tämän tapaista, jos haluttaisiin ilmoittaa muutoksista vain silloin, kun propertyn arvo on oikeasti muuttunut:

```csharp
private string _nimi;
public string Nimi
{
    get
    {
        return _nimi;
    }
    set
    {
        // Varmistetaan, että muutos tehdään vain arvon oikeasti muuttuessa
        if(EqualityComparer<string>.Default.Equals(_nimi, value))
        {
            _nimi = value;

            // Laukaistaan muutoksen tapahtuma
            OnPropertyChanged(nameof(Nimi));
        }
    }
}
```
<figcaption>Mielivaltaisen ViewModelin property</figcaption>

Tässä olisi aika paljon kirjoitettavaa, jos tämä pitäisi tehdä jokaisen propertyn kohdalla!

Ratkaistaan tämä ongelma lisäämällä kätevä `SetProperty`-funktio kantaluokallemme, joka hoitaa kaiken tuon seremonian meille automaattisesti taustalla:

```csharp
protected virtual bool SetProperty<T>(ref T field, T value, [CallerMemberName] string propertyName = "")
{
    if (EqualityComparer<T>.Default.Equals(field, value))
    {
        return false;
    }

    field = value;
    OnPropertyChanged(propertyName);
    return true;
}
```
<figcaption>ViewModel.cs</figcaption>

Tässä listaus funktiossa käytetyistä konsepteista:

* `field` ja `value`: `field` on [backing field](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/properties#properties-with-backing-fields), jota ollaan päivittämässä `value`-parametrin arvolla.

* `T`-tyyppi: Kyse on ns. "määrittämättömästä", eli geneerisestä tyypistä. Aiheesta löytyy lisää termillä ["generics"](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/generics/). `SetProperty` ottaa siis sisäänsä kaksi arvoa välittämättä arvojen suhteen mistään muusta, kuin että arvot ovat samaa tyyppiä `T`.

* `ref`-avainsana: Arvo syötetään funktiolle [referenssinä](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/ref) arvon tyypistä riippumatta (value/reference).
Tämä mahdollistaa sen, että kun tässä funktiossa kutsumme `field = value`, niin oikeasti propertyn backing field päivitetään kutsuvassa luokassa. Kätevää!

* `[CallerMemberName]`-attribuutti: Tällä vältetään määrittelemästä aina erikseen, että minkä niminen property on muuttunut. `OnPropertyChanged` ottaa sisäänsä muuttuneen propertyn nimen, jolloin tämän [attribuutin](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/attributes/) avulla meidän ei tarvitse eksplisiittisesti syöttää `SetProperty`-funktiolle muuttuvan propertyn nimeä. Nice.

* `EqualityComparer<T>`: Genericsit eivät toimi `==`-operaattorin kanssa, joten joudumme käyttämään tätä referenssien vertailutapaa.

![][2]{: .center-image }
<figcaption class="caption">== -operaattori ei toimi genericsien kanssa.</figcaption>

* Funktion `bool`-paluuarvo: Funktio palauttaa `True`, jos annettu arvo on eri kuin nykyinen arvo, muutoin `False`. Tätä voidaan käyttää kätevästi hyödyksi kun haluamme laukaista jonkin toisen propertyn päivityksen näkymälle nykyisen propertyn muuttuessa.

ViewModel-kantaluokka on nyt valmis tarpeisiimme!

Tähän lopuksi vielä käydään periyttämässä MainViewModel ViewModelistamme, jolloin MainViewModel.cs tulee näyttämään tältä:

```csharp
using System.Windows.Input;
using Kettunen.BMICalculator.WPFClient.MVVM;

namespace Kettunen.BMICalculator.WPFClient
{
    public class MainViewModel : ViewModel
    {
        public ICommand Navigate { get; }

        public string NavigateText => "CALCULATE";
    }
}
```
<figcaption>MainViewModel.cs</figcaption>

Seuraavassa osassa luodaan toteutus `ICommand`-rajapinnalle, jotta pääsisimme navigoimaan piakkoin tulosnäkymään painiketta painamalla.

[1]: /assets/images/tehdaan-wpf-sovellus/22-01.jpg
[2]: /assets/images/tehdaan-wpf-sovellus/22-02.jpg