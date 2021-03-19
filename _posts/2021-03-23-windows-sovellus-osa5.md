---
hidden: true
title: "Tehdään Windows-työpöytäsovellus"
layout: post
date: 2021-03-23 18:00
headerImage: false
tag:
- WPF
- Opas
star: true
category: blog
author: Anssi Kettunen
description: Opas Windows-työpöytäsovelluksen tekemiseen WPF:llä
---

### Ongelma: Tekstilaatikoita ei erota toisistaan

Tarvitsemme selkeän tavan ilmaista kumpaan laatikkoon pitää syöttää pituus ja kumpaan painon.

Lähdetään ratkaisemaan ongelmaa lisäämällä tekstit laatikoiden päälle ja paketoimalla nämä uudet pituuden ja painon kokonaisuudet omiin `Grid`-elementteihinsä.

Huomioi erityisesti, että nyt molemmat `TextBox`-elementit ovat omien ruudukkojensa "1"-riveillä, koska uusi `TextBlock` sijoitetaan "0"-riville. Annetaan pituuden ja painon tekstit `TextBox`-elementin `Text`-attribuutille:

`MainWindow.xaml:`

```diff
        ...
+        <Grid>
+            <Grid.RowDefinitions>
+                <RowDefinition />
+                <RowDefinition />
+            </Grid.RowDefinitions>
+            <TextBlock Text="Weight" />
            <TextBox Grid.Row="1"
                     Background="MintCream"
                     Foreground="DarkSalmon" />
+        </Grid>

+        <Grid Grid.Row="1">
+            <Grid.RowDefinitions>
+                <RowDefinition />
+                <RowDefinition />
+            </Grid.RowDefinitions>
+            <TextBlock Text="Height" />
-            <TextBox Background="Cornsilk"
+            <TextBox Grid.Row="1"
+                     Background="Cornsilk"
                     Foreground="Chocolate" />
+        </Grid>
```

Ohjelmamme käyttöliittymän koodin pitäisi näyttää nyt tältä kokonaisuudessaan:
`MainWindow.xaml:`

```xml
<Window x:Class="Kettunen.BMICalculator.WpfClient.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:local="clr-namespace:Kettunen.BMICalculator.WpfClient"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        Title="BMI Calculator"
        Width="800"
        Height="450"
        mc:Ignorable="d">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition />
            <RowDefinition />
        </Grid.RowDefinitions>

        <Grid>
            <Grid.RowDefinitions>
                <RowDefinition />
                <RowDefinition />
            </Grid.RowDefinitions>
            <TextBlock Text="Weight" />
            <TextBox Grid.Row="1"
                     Background="MintCream"
                     Foreground="DarkSalmon" />
        </Grid>

        <Grid Grid.Row="1">
            <Grid.RowDefinitions>
                <RowDefinition />
                <RowDefinition />
            </Grid.RowDefinitions>
            <TextBlock Text="Height" />
            <TextBox Grid.Row="1"
                     Background="Cornsilk"
                     Foreground="Chocolate" />
        </Grid>

    </Grid>
</Window>
```

💡 Voit kommentoida XAML:ssa käyttämällä tagia `<!--` avaamaan kommentti ja `-->` sulkemaan se. Kommentteja on hyvä käyttää, jos haluat erityisesti selkeyttää jotain kohtaa, tai laittaa huomioita määrittelyn sekaan tarkentamaan asioita. Esimerkiksi jos edeltävä koodilistaus tuntuu liian sekavalta kaikkine ruudukkoineen, niin voit vapaasti lisätä kommentteja selkeyttämään mikä minkäkin elementin tehtävä on.

```xml
<!-- Tämä on kommentti -->

<!--
    Tämä on useamman rivin 
    pituinen kommentti
 -->
```

Noin!

Nyt voimme selkeästi syöttää haluamamme tiedot, mutta entäs miten saamme itse laskentatuloksen näkyviin?