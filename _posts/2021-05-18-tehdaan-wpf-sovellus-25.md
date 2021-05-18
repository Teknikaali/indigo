---
title: "Tehdään WPF-sovellus - 25 - Navigoinnin toteutus"
image: assets/images/tehdaan-wpf-sovellus/ketuttaako-header.jpg
layout: post
date: 2021-05-18 20:00
tag:
- WPF
category: blog
author: anssikettunen
description: Opas kuinka tehdä Windows-työpöytäsovellus käyttäen WPF, XAML ja C#. Tässä osassa muutetaan sovellus mukailemaan MVVM-tyyliä.
---

## Navigointi tapahtuu ViewModelia vaihtamalla

Olemme aiemmin lisänneet päänäkymälle ResultView-näkymän omana elementtinään. Haluamme kuitenkin vaihtaa tätä näkymää aina, kun alareunan painiketta painetaan. Eli tarvitsemme tähän jonkin hieman dynaamisemman vaihtoehdon.

Laitetaan ResultViewin tilalle ContentControl, joka nimensä mukaisesti huokuu hyvää paikkaa esittää sisältöä. Lisätään myös MainViewModelille uusi property `CurrentViewModel`, jonka tarkoitus on ylläpitää tietoa missä näkymässä käyttäjä milloinkin on.

{% include note.html content='En oikein löytänyt miellyttävää esimerkkiä miten `ContentControlia` tulisi käyttää ViewModelien kanssa, mutta [tässä](https://www.technical-recipes.com/2016/switching-between-wpf-xaml-views-using-mvvm-datatemplate/) yksi menettelevä.' %}

Bindataan ContentControlin `Content` MainViewModelin `CurrentViewModel`-propertyyn.

```diff
-<local:ResultView />
+<ContentControl Content="{Binding CurrentViewModel}" />
```
<figcaption>MainWindow.xaml</figcaption>

```csharp
public class MainViewModel : ViewModel
{
    private ViewModel _currentViewModel;
    public ViewModel CurrentViewModel
    {
        get => _currentViewModel;
        private set => SetProperty(ref _currentViewModel, value)
    }
    // ...
```
<figcaption>MainViewModel.cs</figcaption>

Voimme asettaa `CurrentViewModelin` setterin `private`, koska emme halua kuin `Navigate`-komennon muokkaavan tätä arvoa.

### Navigate-komento vaihtaa ViewModelia

Seuraavaksi tarvitsemme ViewModelit, joiden välillä haluamme `Navigate`-komennon vaihtavan.

Lisätään `MainViewModelille` talteen `InputViewModel` ja `ResultViewModel` omiin kenttiinsä, jotta voimme antaa komennon asettaa näistä toisen aina vuorollaan `CurrentViewModelin` arvoksi. Alustetaan myös nämä uudet muuttujat rakentajassa.

```csharp
public class MainViewModel : ViewModel
{
    private readonly InputViewModel _inputViewModel;
    private readonly ResultViewModel _resultViewModel;
    
    // ...
    
    public MainViewModel()
    {
        _inputViewModel = new InputViewModel();
        _resultViewModel = new ResultViewModel();
    }
```
<figcaption>MainViewModel.cs</figcaption>

{% include note.html content='Luomme vaadittavat ViewModelit suoraan rakentajassa, mutta tähän on myös olemassa parempikin tapa: Dependency Injection. Käytännössä MainViewModelin ei tarvitsisi huolehtia, että miten Input- tai ResultViewModel muodostetaan. [Tässä](https://www.freecodecamp.org/news/a-quick-intro-to-dependency-injection-what-it-is-and-when-to-use-it-7578c84fa88f/) aikas hyvin kirjoitettu pätkä aiheesta.' %}

Haluamme, että ensimmäinen näkymä, jonka käyttäjä näkee on painon ja pituuden syötekentät. Asetetaan siis `CurrentViewModelin` arvoksi suoraan rakentajassa juuri luomamme `_inputViewModel`:

```csharp
public MainViewModel()
{
    // ...

    CurrentViewModel = _inputViewModel;
}
```
<figcaption>MainViewModel.cs</figcaption>

Käynnistetään sovelluksemme tässä vaiheessa ja ihmetellään näppäimistöllä pyyhältäneiden sormiemme tuotoksia:

![][1]{: .center-image }
<figcaption class="caption">Kummallista tekstiä.</figcaption>

.. no eihän se nyt aivan oikealta näytä, vai mitä?

Bindasimme aiemmin `ContentControl.Content`-arvon suoraan `ContentViewModel`-propertyyn, mutta emme ole kertoneet sovellukselle, että _miten_ haluamme tuota tietoa näyttää.

{% include note.html content='WPF näyttää oletuksena bindatuille objekteille, joiden tyyliä ei ole määritelty erikseen, niiden `ToString()`-kutsun tuottaman tekstin.' %}

#### (vapaaehtoinen vaihe) ToString() ylikirjoittaminen

Voit kokeilla miten `ToString()`:n ylikirjoittaminen oikein käytännössä vaikuttaa näkymään näin:

1) Lisää `InputViewModelille` ylikirjoitus haluamallasi tekstillä

```csharp
public override string ToString()
{
    return "Kissimirre";
}
```
<figcaption>InputViewModel.cs</figcaption>

2) Käännä ja suorita ohjelma

Näkymällä pitäisi näkyä `ToString()`-funktion palauttama arvo. Hienoa! 😺

![][2]{: .center-image }
<figcaption class="caption">Kissimirre.</figcaption>

#### (vapaaehtoinen vaihe loppui)

### DataTemplate määrittää miltä ViewModel näyttää

[DataTemplaten](https://docs.microsoft.com/en-us/dotnet/desktop/wpf/data/data-templating-overview) avulla voimme kertoa WPF:lle, että miltä haluamme tarjotun tiedon näyttävän ruudulla.

Yksinkertaisuudessaan voimme lisätä kaksi uutta `DataTemplate`-elementtiä `App.xaml`-tiedostoon muiden tyylien joukkoon:

```xml
<DataTemplate DataType="{x:Type local:InputViewModel}">
    <local:InputView />
</DataTemplate>

<DataTemplate DataType="{x:Type local:ResultViewModel}">
    <local:ResultView />
</DataTemplate>
```
<figcaption class="caption">App.xaml</figcaption>

`DataType` kertoo `DataTemplatelle`, että minkä tyyppiselle tiedolle sen tulisi luoda näkymä. Meidän ei välttämättä olisi tarvinnut luoda Input- ja ResultViewejä ollenkaan, mutta tekemällä näin saimme jaettua näkymiä helposti hallittaviin pienempiin palasiin. Olisimme voineet määritellä `DataTemplaten` myös näin avaamalla ResultViewin auki DataTemplaten sisälle:

```xml
<DataTemplate DataType="{x:Type local:ResultViewModel}">
    <Border Margin="24"
            Style="{StaticResource ItemBorder}">
        <Grid VerticalAlignment="Center">
            <Grid.RowDefinitions>
                <RowDefinition />
                <RowDefinition />
            </Grid.RowDefinitions>
            <TextBlock Text="BMI" />
            <TextBlock Grid.Row="1"
                       Text="{Binding Result}" />
        </Grid>
    </Border>
</DataTemplate>
```
<figcaption class="caption">Vaihtoehtoinen tapa toteuttaa `DataTemplate`</figcaption>

{% include note.html content='Eräs opettajistani tapasi kysyä: "Miten norsu tulisi syödä?" .. "Pieninä palasina tietenkin!", kun kyse oli jostain isomman kokonaisuuden pilkkomisesta pienempiin osiin. Näin jälkikäteen ajateltuna melko häiritsevä mielikuva ottaen huomioon norsujen uhanalaisuuden.. jep jep 🙈🐘' %}

Nyt kun suoritamme ohjelman, niin hommat alkavat näyttämään vähän toiveekkaammalta:

![][3]{: .center-image }
<figcaption class="caption">Nyt näyttää paremmalta.</figcaption>

### Joko sitä komentoa pääsee toteuttamaan?

Jos nyt sitten tämän kerran.

Loimme aiemmin [osassa 23]({% post_url 2021-05-17-tehdaan-wpf-sovellus-23 %}) `DelegateCommand`-toteutuksen `ICommand`-rajapinnalle. Nyt pääsemme vihdoinkin sitä käyttämään!

Alustetaan `Navigate`-propertymme `DelegateCommandiksi` ja nikkaroidaan sen sisälle painikkeen painamisesta tapahtuva logiikka.

Voimme määrittää `DelegateCommandin` parametrit (`execute` ja `canExecute`) kätevästi käyttämällä lambdoja. Asetetaan `execute` vaihtamaan `CurrentViewModel` tulosten näyttämisen ViewModeliin. Sallitaan myös vielä tässä vaiheessa painikkeen painaminen aina tilanteesta riippumatta palauttamalla suoraan `true` `canExecute`-kyselylle:

```csharp
Navigate = new DelegateCommand(
    _ =>
    {
        CurrentViewModel = _resultViewModel;
    },
    _ => true);
```
<figcaption class="caption">MainViewModel.cs</figcaption>

{% include note.html content='`_`-merkkiä voi käyttää perinteisemmän `x`-merkin sijaan lambdassa, jos emme ole kiinnostuneita tästä parametrista (kts. [discard](https://docs.microsoft.com/en-us/dotnet/csharp/discards)). Esimerkiksi emme vielä tarvitse komennon `execute`-parametrin arvoa, joten voimme merkata sen "hylätyksi" arvoksi käyttämällä `x`:n sijaan `_`-merkintää.' %}

Navigointi toimii nyt nätisti tulosnäkymään, kun painiketta painetaan. Käy vaikka kokeilemassa. Hienoa!

### Vielä vähän ratkottavaa

Meillä on käsissämme vielä muutama ongelma ratkottavana:

1) Painikkeen teksti ei muutu sanaan "BACK" siirryttäessä tulosnäkymälle

2) Tulosnäkymältä ei pääse palaamaan takaisin

3) Painoindeksiä ei lasketa ollenkaan (hups.)

4) Navigointi tulosnäkymään onnistuu, vaikka emme olisikaan syöttäneet mitään arvoja

Tässä voisi olla hyvää tekemistä seuraavalle osalle!

[1]: /assets/images/tehdaan-wpf-sovellus/25-01.jpg
[2]: /assets/images/tehdaan-wpf-sovellus/25-02.jpg
[3]: /assets/images/tehdaan-wpf-sovellus/25-03.jpg