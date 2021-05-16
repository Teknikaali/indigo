---
title: "Tehdään WPF-sovellus - 23 - Komennon toteutus"
image: assets/images/tehdaan-wpf-sovellus/ketuttaako-header.jpg
layout: post
date: 2021-05-17 18:00
tag:
- WPF
category: blog
author: anssikettunen
description: Opas kuinka tehdä Windows-työpöytäsovellus käyttäen WPF, XAML ja C#. Tässä osassa muutetaan sovellus mukailemaan MVVM-tyyliä.
---

## Komennon toteuttaminen yhdellä luokalla

`ICommand`-rajapinnan toteuttavan luokan property voidaan bindata `Button`-elementin `Command`-attribuuttiin. Meillä ei vain kylläkään ole yhtäkään toteutusta vielä `ICommand`-rajapinnalle.. Tehdään tähän muutos!

{% include note.html content='Meidän itse luomaamme toteutusta ei tule sekoittaa WPF:stä valmiiksi löytyviin `RoutedCommand`- tai `RoutedUICommand`-toteutuksiin. Näistä lisää [täällä](https://docs.microsoft.com/en-us/dotnet/desktop/wpf/advanced/commanding-overview).' %}

Aloitetaan lisäämällä MVVM-kansioon uusi luokka `DelegateCommand` ja määrätään se toteuttamaan `ICommand`-rajapinta:

```csharp
using System;
using System.Windows.Input;

namespace Kettunen.BMICalculator.WPFClient.MVVM
{
    public class DelegateCommand : ICommand
    {
        public event EventHandler CanExecuteChanged;

        public bool CanExecute(object parameter)
        {
            throw new NotImplementedException();
        }

        public void Execute(object parameter)
        {
            throw new NotImplementedException();
        }
    }
}
```
<figcaption>DelegateCommand.cs</figcaption>

Komento koostuu yksinkertaisimmillaan näistä kolmesta rajapinnan määrittelemästä komponentista.
* `CanExecuteChanged`-tapahtuma laukeaa, kun komennon suoritettavuus (CanExecute) muuttuu.
* `CanExecute`-funktiolla tehdään tarkastus voiko komennon suorittaa nykyisessä ohjelman tilassa.
* `Execute`-funktiota kutsutaan, jos `CanExecute` palauttaa true ja esim. painiketta on painettu näkymältä.

{% include note.html content='Voit vilkaista [täältä](https://stackoverflow.com/questions/42113388/what-is-the-reason-for-icommand-in-mvvm) "syitä", että miksi `ICommand` on olemassa.' %}

Toteutetaan oma komentomme hyvin yleiskäyttöiseksi, jotta emme joutuisi tekemään aina uutta tyyppiä uudelle komennolle esim. `CalculateCommand`, `BackCommand` jne..

### Rakentajan toteuttaminen

Aloitetaan rakentajasta. Komennon tarkoitus on siis suorittaa toiminto ja olla tietoinen  siitä, että voiko toimintoa ylipäätään suorittaa kyseisessä ohjelman tilassa.

Näkymä antaa komennolle parametriksi `null` tai `object`-tyyppisen instanssin kutsuessaan komentoa. Määrittelemme näkymän puolella annettavan parametrin myöhemmin.

Näiden tietojen perusteella voimme nikkaroida rakentajan kasaan:

```csharp
public class DelegateCommand : ICommand
{
    private readonly Action<object> _execute;
    private readonly Predicate<object> _canExecute;

    // ...

    public DelegateCommand(Action<object> execute, Predicate<object> canExecute)
    {
        _execute = execute;
        _canExecute = canExecute;
    }
}
```
<figcaption>DelegateCommand.cs</figcaption>

{% include note.html content='[Tässä](https://stackoverflow.com/a/56338425/1646905) on oikein sievät esimerkit mitä `Action`, `Func` ja `Predicate` ovat.
' %}

### CanExecute ja Execute

Näiden lisäysten jälkeen `CanExecuten` ja `Executen` toteuttaminen on hyvin suoraviivaista. Kutsutaan niissä vain juuri lisäämiämme `_canExecute`- ja `_execute`-toimintoja.

```csharp
public bool CanExecute(object parameter) => _canExecute(parameter);

public void Execute(object parameter) => _execute(parameter);
```
<figcaption>DelegateCommand.cs</figcaption>

{% include note.html content='Voisimme rakentaa komennosta paljon hienommankin luomalla rakentajan, joka ottaa sisäänsä myös parametrittomat `executen` ja `canExecuten`. Tällöin meidän tulisi ottaa `null` useammin huomioon, joten olen jättänyt tämän toteutuksen vain mahdollisimman yksinkertaiselle tasolle.' %}

### CanExecuteChanged

`CanExecuteChanged`-tapahtuman voimme toteuttaa käyttämällä `CommandManageria` hyödyksi:

```csharp
public event EventHandler CanExecuteChanged
{
    add { CommandManager.RequerySuggested += value; }
    remove { CommandManager.RequerySuggested -= value; }
}
```
<figcaption>DelegateCommand.cs</figcaption>

{% include note.html content='`CommandManager.RequerySuggested` laukaistaan aina silloin, kun `CommandManager` havaitsee, että jokin komennon suoritukseen vaikuttava tekijä on muuttunut. Esimerkiksi focuksen muuttuminen elementiltä toiselle. [Täällä](https://stackoverflow.com/questions/2763630/how-does-commandmanager-requerysuggested-work) lisää aiheesta.' %}

Ja mitäs ihmettä. Sanoisin, että se oli siinä! 💪

## Mietteitä tästä kaikesta

Sinänsä tuntuu erikoiselta, ettei WPF:n kirjastoissa ole mukana oletustoteutusta jo valmiiksi `ICommand`- tai `INotifyPropertyChanged`-rajapinnoille. Väittäisin, että kuitenkin suurin osa WPF-sovelluksista nojaa juurikin näiden rajapintojen onnistuneisiin toteutuksiin.

Toisaalta toimiviksi todettuja MVVM-paketteja on runsaasti mistä valita, joten ehkä oletustoteutusta ei ole katsottu tarpeelliseksi.

Tähän väliin voisi melkeinpä suositella Tim Coreyn läpikäyntiä eri MVVM-frameworkeista [(video)](https://www.youtube.com/watch?v=8E000zu8UhQ).

Seuraavassa osassa luodaan Input- ja Result-näkymille omat ViewModelinsa. Siinä puuhassa päästäänkin käyttämään `ViewModel.SetProperty`-funktiota tarkoituksenmukaisella tavallaan!