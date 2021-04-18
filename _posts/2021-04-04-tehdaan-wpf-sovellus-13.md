---
title: "Tehdään WPF-sovellus - 13 - Painikkeen uusi ulkoasu"
image: assets/images/tehdaan-wpf-sovellus/ketuttaako-header.jpg
layout: post
date: 2021-04-18 13:00
tag:
- WPF
category: blog
author: anssikettunen
description: Opas kuinka tehdä Windows-työpöytäsovellus käyttäen WPF, XAML ja C#. Tässä osassa lisätään uudet tyylit eri tyyppisille elementeille.
---

## Tyylitellään Button

![Painike.][1]{: .center-image }
<figcaption class="caption">Painike.</figcaption>

Painikkeemme on nykyisellään hurjan tylsän näköinen. Harmaa erämaa, jossa elämää ei ole. Synkkä taivas, jolla ei ole tähden tähteä. Metsä, joka ei vastaa kun sinne huudetaan. No. Ehkä sait jo ajatuksesta kiinni, joten aloitetaan!

Tämä on jo tuttua puuhaa, eli tehdään seuraavat muutokset App.xaml:iin:
* Vaihdetaan taustaväri
* Asetetaan fontti
* Muutetaan tekstin väri

```xml
<Style TargetType="Button">
        <Setter Property="Background" Value="#EB1555" />
        <Setter Property="BorderBrush" Value="#EB1555" />
        <Setter Property="FontFamily" Value="Calibri" />
        <Setter Property="FontSize" Value="48" />
        <Setter Property="Foreground" Value="White" />
</Style>
```
<figcaption>App.xaml - Button-tyyli.</figcaption>

Kaikki hyvin tähän saakka? Öh, eipä oikeastaan. Nopea vilkaisu Designeriin herättää ehkä hieman hämmennystä:

![Mikä tässä on pielessä?][2]{: .center-image }
<figcaption class="caption">Mikä tässä on pielessä?</figcaption>

Määrittelimme aiemmin, että `Foreground` (eli painikkeen tekstin väri) olisi arvoltaan `White`. Kuvaa katsomalla voimme vain todeta, että "Ei.. Ei se ole valkoinen". Mikä tässä on vialla?

## Elementtien sisällä on elementtejä

`Button` itsessään koostuu useammasta elementistä. Voimme havainnollistaa tätä käynnistämällä sovelluksen ja tarkastelemalla [Live Visual Tree](https://docs.microsoft.com/en-us/visualstudio/xaml-tools/inspect-xaml-properties-while-debugging) -ikkunasta painikkeen elementtiä tarkemmin. Varmista, että "Show Just My XAML" ei ole päällä, jotta voimme "porautua" WPF:n omienkin kontrollien hierarkian sisään:

![Live Visual Tree - "Show Just My XAML"][3]{: .center-image }
<figcaption class="caption">Live Visual Tree - "Show Just My XAML"</figcaption>

{% include note.html content='"Show Just My XAML" on useimmiten hyvä pitää päällä, koska suurimmassa osaa tapauksista halutaan tutkia vain itse määritettyjen kontrollien ja elementtien arvoja.' %}

{% include note.html content='Tässä vaiheessa on hyvä myös tutustua Live Visual Treen tarjoamiin muihin ominaisuuksiin.' %}

![Live Visual Tree - Porauduttu syvemmälle][4]{: .center-image }
<figcaption class="caption">Live Visual Tree - Porauduttu syvemmälle</figcaption>

Kappas kummaa. Mitäpä sieltä löytyikään: `TextBlock`!

Tämä selittää sen, että miksi painikkeen tekstin väri ei olekaan valkoinen vaan harmaa. Tyylittelimme kaikki sovelluksen `TextBlock`-elementit esittämään tekstinsä harmaana. Painike kunnioittaa tätä sääntöä, joten miten korjaamme asian?

## Ylikirjoitetaan painikkeen sisältö

> Ei ole häpeä etsiä tietoa.
> - Minä, 2021

Useimmissa tapauksissa emme ole yksin ongelmiemme kanssa. Ei ole siis mitään syytä olla turvautumatta kattavaan tietopankkiin: Internetiin. Käyttämäni hakukone tuotti kirjoitushetkellä hakusanoilla "wpf button textblock style" 239 000 tulosta, joista ensimmäisten joukossa oli vastaus ongelmaamme: [StackOverflow - How are WPF Buttons and TextBlock styles related?](https://stackoverflow.com/questions/4484313/how-are-wpf-buttons-and-textblock-styles-related).

Ratkaisu on siis hyvinkin yksinkertainen: Kerromme erikseen, ettemme halua painikkeen sisäiselle tekstilaatikolle mitään tyylimäärittelyä. Asetetaan tyylin arvoksi siis `Null` ja siirretään teksti painikkeelta `TextBlock`-elementille:

```xml
<Button Grid.Row="2"
        Click="Button_Click">
    <TextBlock Style="{x:Null}"
               Text="CALCULATE" />
</Button>
```
<figcaption>MainWindow.xaml - Painikkeen uusi määrittely.</figcaption>

![Buttonin teksti näyttää nyt paremmalta][5]{: .center-image }
<figcaption class="caption">Buttonin teksti näyttää nyt paremmalta</figcaption>

Kun nyt käynnistämme sovelluksen, niin kaikki vaikuttaa taas olevan ok.

...

Kunnes liikutamme kursorin painikkeen päälle:

![Painikkeen hover on väärän väärinen][6]{: .center-image }
<figcaption class="caption">Painikkeen hover on väärän väärinen</figcaption>

Painikkeen hoverin väri ei oikein vastaa muuta tyyliä, joten korjataan asia määrittelemällä ensimmäinen interaktiivinen toiminto tyylien kannalta: hoveroinnista painikkeen taustavärin vaihtaminen!

## Hover-väri korjataan triggereillä

WPF:ssä interaktiivisia toimintoja voi toteuttaa käyttämällä [triggereitä](https://www.tutorialspoint.com/wpf/wpf_triggers.htm). Lisätään painikkeen tyylille triggeri, joka muuttaa painikkeen väriä kursorin ollessa sen päällä:

```xml
<Style.Triggers>
    <Trigger Property="IsMouseOver" Value="True">
        <Setter Property="Background" Value="#31142F" />
    </Trigger>
</Style.Triggers>
```
<figcaption>App.xaml - Button-tyyliin lisätty trigger.</figcaption>

Käytännössä tämän määrittelyn voisi lukea näin auki:
"Kun painikkeen `IsMouseOver`-arvo on `True` (kursori on elementin päällä), niin asetetaan `Background` arvoon `#341142F` (vaihdetaan taustaväri)."

Törmäämme jälleen yhteen WPF:n omituisuuksista: Painikkeen sisäisissä tyylimäärittelyissä on määritelty erikseen `IsMouseOver`-triggeri, joka ylikirjoittaa oman triggerimme. [Ratkaisuna](https://stackoverflow.com/a/17259993/1646905) tähän voimme kiertää oletustyylin pakottaman hover-tyylittelyn korvaamalla painikkeen tyylissä `Template`-arvon:

{% include note.html content='Käytin hakulausetta "wpf button trigger mouseover".' %}

```xml
<Setter Property="Template">
    <Setter.Value>
        <ControlTemplate TargetType="{x:Type Button}">
            <Border Background="{TemplateBinding Background}">
                <ContentPresenter HorizontalAlignment="Center"
                                  VerticalAlignment="Center" />
            </Border>
        </ControlTemplate>
    </Setter.Value>
</Setter>
```
<figcaption>App.xaml - Button-tyyliin korjattu Templaten määrittely.</figcaption>

![Hover korjattu][7]{: .center-image }
<figcaption class="caption">Hover korjattu</figcaption>

Hienoa! Painikkeemme vaikuttaisi olevan nyt viimeistelty.

{% include note.html content='On hyvä huomioida tässä vaiheessa, että tekemämme ratkaisut toimivat tässä kyseisessä sovelluksessa juuri näihin kyseisiin ongelmiin hyvin. Nämä samat ratkaisut eivät välttämättä toimi yhtä tehokkaasti esim. suuremmissa sovelluksissa, joissa on useita tyylimäärittelyjä ja monimutkaisempia vaatimuksia.' %}

Voimme seuraavaksi käydä käsiksi elementtien asemointiin ikkunan sisällä ja lisätä yksiköiden tekstit takaisin paremmille paikoilleen.

[1]: /assets/images/tehdaan-wpf-sovellus/13-01.jpg
[2]: /assets/images/tehdaan-wpf-sovellus/13-02.jpg
[3]: /assets/images/tehdaan-wpf-sovellus/13-03.jpg
[4]: /assets/images/tehdaan-wpf-sovellus/13-04.jpg
[5]: /assets/images/tehdaan-wpf-sovellus/13-05.jpg
[6]: /assets/images/tehdaan-wpf-sovellus/13-06.jpg
[7]: /assets/images/tehdaan-wpf-sovellus/13-07.jpg