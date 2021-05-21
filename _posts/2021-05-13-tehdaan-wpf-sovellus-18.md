---
title: "Tehdään WPF-sovellus - 18 - Tuotantokelpoiseksi MVVM:llä"
image: assets/images/tehdaan-wpf-sovellus/18-header.jpg
layout: post
date: 2021-05-13 10:00
tag:
- WPF
category: blog
author: anssikettunen
description: Opas kuinka tehdä Windows-työpöytäsovellus käyttäen WPF, XAML ja C#. Tässä osassa muutetaan sovellus mukailemaan MVVM-tyyliä.
---

![][2]

## MVVM on WPF-sovelluksen sydän

Sovelluksemme toimii virheittä, näyttää hyvältä ja saamme laskettua painoindeksimme sillä. Siistiä! Silti mieltäni kalvaa jokin tämän suhteen..

Olemme kehittäneet sovellusta hyvin.. miten sen sanoisi.. pintapuolisesti? On aika sukeltaa kovan kuoren alle ja tehdä jotain merkityksellistä sovellukselle. Sisäisesti. Diippii.

MVVM. Äm-vee-vee-äm. Mistä tässä WPF:lle tärkeässä kirjainyhdistelmässä oikein on kyse?

MVVM tulee sanoista **Model-View-ViewModel** ja se on yksi WPF-sovelluksissa käytettävistä tavoista mallintaa sovellusta ("design pattern" englanniksi ja ehkä "suunnittelumalli" suomeksi tms.). Sen tärkein tehtävä on erotella käyttöliittymämme vastuualueet selkeästi toisistaan helposti hallittaviin komponentteihin.

![][1]{: .center-image }
<figcaption class="caption">Hienoja laatikoita. Ihan vain koska. Vilkaise ja siirry eteenpäin.</figcaption>

Jos googletat "MVVM" kuvahaulla, niin saat paljon vastaavia kuvia laatikoista ja niitä yhdistävistä nuolista. Välillä laatikot ovat vaihtaneet paikkaa toistensa kanssa ja välillä nuolia on enemmän tai vähemmän. "Se vähän riippuu" kuvaa hyvin tätäkin tilannetta: _MVVM on erinomainen määränpää, mutta reitti on suhteellisen vapaa._

Jos haluat lukea lisää MVVM:stä ja sitä ympäröivästä ajatusmaailmasta, niin esimerkiksi [tässä](https://www.wintellect.com/model-view-viewmodel-mvvm-explained/) on suhteellisen hyvä artikkeli vilkaista läpi.

Käydään kuitenkin seuraavaksi hyvin pintapuolisesti läpi mitä eri laatikot tarkoittavat.

## MVVM ja sen kolme pilaria

### View
View on nimensä mukaisesti käyttäjälle näkyvä osuus sovelluksesta.
Olemme aiemmissa osissa toteuttaneet tämän osan sovelluksesta lähes suoraan sellaisenaan käyttökelpoiseksi MVVM:n kanssa.
Tehdään kuitenkin pientä jaottelua ja säätöä näkymän suhteen, jotta se sopisi paremmin MVVM:n muottiin.

### ViewModel
ViewModelin avulla sidomme (bind) ohjelman näkymän taustalla olevaan malliin. Näkymän ei siis ole tarve enää jatkossa osata laskea painoindeksiä, koska se ei ole näkymän tehtävä.

{% include note.html content='Tällä hetkellä laskenta tapahtuu täysin näkymän taustatiedostossa (MainWindow.xaml.cs), jolloin itse _näkymä_ on vastuussa laskennasta.' %}

Esimerkiksi "CALCULATE"-painikkeesta painettaessa näkymän ei tarvitsisi huolehtia _miten_ ja _missä_ laskenta tapahtuu, kunhan se vain tapahtuu ja näkymä saa esitettäväkseen tuloksen.
ViewModel tulee hoitamaan jatkossa tämän ja näkymän tulee vain huolehtia siitä, että tulokset näytetään nätisti silloin kun ne halutaan näyttää.

### Model
Mallin osuus jää tässä vaiheessa harmittavasti hieman taka-alalle. Mallilla tarkoitetaan sitä osuutta sovelluksesta, joka on "dataa".

Jatkossa voisimme esimerkiksi tallentaa käyttäjän syöttämät pituuden, painon ja painoindeksituloksen talteen.
Voisimme sitten myöhemmin noutaa kyseiset tiedot ja listata ne käyttöliittymässä esimerkiksi jonkinlaista historianäkymää ja -graafia varten.
Tässä tapauksessa antaisimme tämän datan (Model, joka pitäisi sisällään painon, pituuden ja bmi-arvon) ViewModelille muunnettavaksi siten, että se voitaisiin näyttää onnistuneesti Viewillä oikeassa muodossa.

## MVVM MVVM MVVM

Voit käyttää jotain valmista pakettia tuomaan MVVM:n ominaisuuksia sovellukseen (tämä on erinomainen valinta), tai sitten luoda koko toteutuksen itse.
Teemme tässä oppaassa toteutuksen itse, jotta MVVM:n peruskonseptit tulisivat tutuksi. Näin jatkossa kolmannen osapuolen MVVM-toteutusta on helpompi ymmärtää.

{% include note.html content='Sovelluksen testattavuus on merkittävä mittari sovelluksen terveydentilan seurantaan. MVVM:n implementoinnin "sivuvaikutuksena" sovelluksesta voi tulla helpommin testattava tietyin esiehdoin. Tästä mahdollisesti lisää myöhemmin.' %}

Käytännössä sovelluksemme on kuitenkin niin yksinkertainen, ettei se edes välttämättä tarvitsisi MVVM:ää avukseen. Vedämme tätä opasta kuitenkin täysillä eteenpäin, niin ei jätetä kiviä kääntämättä ihan vain sen takia, että sellainen mahdollisuus meille annettaisiin. Ylittämällä aidan matalimmasta kohtaa jää paljon asioita oppimatta. Elämämohjeita. Vau. Jatketaan.

Mainitsin tässä osassa ainakin 20 kertaa termin "MVVM", joten toivoisin pystyväni hillitsemään kyseisen termin viljelyä jatkossa.

...

Ehkä vielä muutama kerta kiellon päälle: MVVM - M̶V̶V̷M̷ - M̴̢̦̺̮̬̬̅̈́͂ͅV̶͕̫̮̀̓̽̊̊͌͊͗͘͝ͅV̵̙͓̭̌̆̉͗͗̄̀́̐̕M̴̫̱͉͇̬͊  M̸͕͙̤̻̺̯̣̺̫̦̹̩̰͖͙̩̖͈̀̾ͅͅV̵̧̛̳̼̬̝̙̤̞͚̫̯̮̗̰̹̼̿̿́̅͌̅͛̀̈́̏̀͛̋̏͛̃̄͊͝V̴̲̰̦̰̝̦̞̬̥̼̫̈́̇͆͌͌̈̿͌̎͂̚̚ͅM̴̫̟̥̪͓̗̅̏!!

Jaha. Lähti vähän lapasesta. Täytyy lopettaa ennen kuin manaa jonkin demonin esille.

Vai onk̴͎̾ó̵̱ j̵̛̳̩̦̲͌̎͛o̶̧̲̭̗̓ l̴̝͖̩̯̀͑̈́̏͊̉̋͛̔̌ì̶̡͈̥̝͎͖̫̖̙͒̅̆͗̐̄̋͑̃͠ȉ̸̞͉̰͙͕̩̱͖̤̾̀̌͘ͅạ̷͈̗̖͂̌͆̽͛̒̂͆̌̚͘͝n̶̛͓͇̘̽̾̒͂̎̌̕͠͝ m̶̢̤̥̻͍͉̟̟͋y̷̺͔̯̼̑̋̀̾ö̶̢͚̬͖̹̾͆̀̅́̏̀͝ḣ̷̖͖̺͚̖̬̻̇̂̈́̏ä̷͔̒̈́͂́͠i̴͈͉̹̝̣͆̚͜͠s̴̻̪̣̔̽͝͝t̸̞̩̀ä̷͕̟̇̐̾͆ ?

Seuraavassa osassa lisätään selkärankaa sovelluksellemme aloittamalla tämän nelikirjaimisen hirviön toteuttaminen.

[1]: /assets/images/tehdaan-wpf-sovellus/18-01.jpg
[2]: /assets/images/tehdaan-wpf-sovellus/18-header.jpg