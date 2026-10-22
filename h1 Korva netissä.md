# h1 Korva netissä

### Ympäristö

- **Käyttöjärjestelmä:** Windows 11, Kali Linux 2026.3
- **Selain:** Microsoft Edge 155.0.4283.62 (64-bit), Firefox 153.4.0esr (64-bit). Huom! Käytän Edgeä vain työssä ja opiskelussa sen M365-synkronoitavuuden vuoksi. Henkilökohtaisessa elämässäni en sitä käytä. Hyi.
- **Virtualisointialusta:** Oracle VirtualBox Version 7.2.16

## x) Spektrini 

<p align="center">
<img width="1307" height="476" alt="image" src="https://github.com/user-attachments/assets/0d7f1190-c2ba-4102-921c-afe9dd0e3f71" />

  <br>
  <em>Spektri ja sovellukset</em>
</p>

- FM-radion taajuusalueeksi Euroopassa, Afrikassa ja osassa Aasian maita on määritelty 87,5-108 MHz vuonna 1984 (International Telecommunication Union).
- Bluetoothin taajuusalue on 2400 - 2483.5 MHz (Bluetooth).
- Wi-Fin taajuusalue vaihtelee 2,4, 5 ja 6 GHz välillä. 2,4 GHz taajuudella on parempi kantavuus ja läpäisykyky, mutta heikompi tiedonsiirto. Tämän lisäksi 2,4 GHz:ssa kanavien taajuuskaistat menevät osittain päällekkäin, joka voi aiheuttaa häiriöitä (IPCisco.com)
- 4G/5G -verkkojen taajuusalueet voivat vaihdella noin 1 - 30 GHz:n välillä. Suomessa käytössä olevat taajuudet ulottuvat normaalisti 700 MHz:stä 26 GHz:iin (Traficom).
- Gammasäteilyn taajuus on yli 30 miljardia GHz, ja sen läpäisykyky riippuu säteilyn energiasta. Yleensä gammasäteily on erittäin läpitunkevaa (European Space Agency, 2019).
  
<br>

- Aallonpituus kasvaa taajuuden pienentyessä - samoin antennin koko. Esimerkiksi 1 MHz:n taajuudella aallonpituus on noin 300 metriä, joten tehokkaan antennin pitäisi olla kymmeniä metrejä pitkä (IPCisco.com).
- Läpäisevyyttä ei varsinaisesti voi kuvata tässä tehtävässä yhdellä nuolella, koska esimerkiksi matalataajuiset FM-radiolähetykset läpäisevät esteitä paremmin ja kantavat pidemmälle kuin korkeammilla taajuuksilla toimivat 5G-verkot. Samoin 2,4 GHz:n Wi‑Fi läpäisee seiniä paremmin ja kantaa pidemmälle kuin 5 tai 6 GHz:n Wi‑Fi. Toisaalta esim. gammasäteily on erittäin läpäisykykyistä valtavasta taajuudestaan ja lyhyestä aallonpituudestaan huolimatta.
- Korkeampien taajuuksien etuna on suurempi tiedonsiirtokapasiteetti, minkä vuoksi niillä voidaan siirtää tietoa huomattavasti nopeammin kuin matalammilla taajuuksilla.
  
## a) WebSDR

Tässä tehtävässä etsitään WebSDR:n avulla vähintään 400 km etäisyydellä itsestä sijaitseva radiovastaanotin ja tutkitaan sen ominaisuuksia, kuten taajuutta, aallonpituutta ja käytettyä modulaatiota (Karvinen, 2026).
Itse valitsin Yhdysvalloissa, Pennsylvanian Milfordissa sijaitsevan K3FEF & W3TKP Radio Ranch -nimisen radion (http://websdr.k3fef.com:8901/), jonka vastaanottimen toimintataajuusalue on n. 0-30 MHz. 

**AM**-modulaatiota käyttämällä etsin radiolähetystä seuraamalla waterfall-näkymää ja valitsemalla siitä voimakkaimman signaalin. Hienosäädin tämän jälkeen vielä vastaanotinta kilohertsi kerrallaan ja eri kaistanleveyksillä, kunnes taajuudella **1190,01 kHz** ja n. **5,5 kHz** kaistanleveydellä ääni kuului kaikkein selkeimmin ja ei-robottimaisena. Lähetys piti sisällään muun muassa IRS:n (Yhdysvaltain verohallinto) mainoksen, Christina Aguileran Genie in a Bottle -kappaleen, kuumia lattarirytmejä ja useita espanjankielisiä mainoksia. Näiden havaintojen perusteella asema vaikuttaa olevan suunnattu lähinnä latinoyleisölle. Otin näistä näytteeksi pari videokaappausta:

<details>
<summary>Näytä videokaappaukset</summary>

https://github.com/user-attachments/assets/16b52f8e-c293-45a5-a0ee-07991484af28

https://github.com/user-attachments/assets/447d434b-7ff4-4484-982d-bbdb5d2b1168

</details>

<br>

Eli tiivistettynä radiolähetyksellä on seuraavat speksit:

- **Taajuus**: 1190,01 kHz
- **Modulaatio**: AM
- **Aallonpituus**: n. 252 m

Aallonpituus saatiin laskemalla valonnopeus (n. **300000000** m/s) / taajuus (1190,01 kHz = **1190010** Hz) (Wikikirjasto).

## b) Moduwhat?

Nyt kokeillaan kunnella toista kanavaa samasta radiosta eri modulaatiolla. Koska aiemmassa käytin AM-modulaatiota, siirryn nyt käyttämään FM-modulaatiota. Löysin kanadalaisen radion, jossa oli alkuun meneillään jokin puheohjelma, jonka jälkeen tuli CTV News Toronton keskipäivän uutiset (tätä tehtävää tehtäessä kello oli 19.00 Suomessa, joten Kanadassa kello on 12.00). FM-modulaatiolla tutkiessa ääntä oli huomattavasti vaikeampaa saada selkeäksi ja kuuntelukelpoiseksi, vaikka kuinka hienovaraisesti yritti taajuuksia ja kaistanleveyttä säätää. Lopulta sain videokaappaukseen pienen pätkän suhteellisen selkeää uutislähetystä, mutta häiriöitä tuli enemmän kuin AM-modulaatiolla:

<details>
<summary>Näytä videokaappaus</summary>

https://github.com/user-attachments/assets/5d9f5c67-67f7-4466-b300-d6ef6cbf29b8

</details>

Radioähetyksen speksit:

- **Taajuus**: 6060,37 kHz
- **Modulaatio**: FM
- **Aallonpituus**: n. 49,5 m

## Lähteet

- Bluetooth. Understanding Bluetooth range. Luettavissa: https://www.bluetooth.com/learn-about-bluetooth/key-attributes/range/. Luettu: 21.10.2026.
- European Space Agency. 2019. Astrophysics. Luettavissa: https://sci.esa.int/web/astrophysics/-/35909-gamma-ray. Luettu: 21.10.2026.
- International Telecommunication Union. FM / TV Regional Frequency Assignment Plans. Luettavissa: https://www.itu.int/en/ITU-R/terrestrial/broadcast/Pages/FMTV.aspx. Luettu: 21.10.2026.
- IPCisco.com. WLAN Frequency Bands. Luettavissa: https://ipcisco.com/lesson/wlan-frequency-bands/. Luettu: 21.10.2026.
- Karvinen, T. 2026. Verkkoon tunkeutuminen ja tiedustelu. Luettavissa: https://terokarvinen.com/verkkoon-tunkeutuminen-ja-tiedustelu/. Luettu: 22.10.2026.
- Traficom. Matkaviestinverkkojen taajuudet ja luvanhaltijat. Luettavissa: https://www.traficom.fi/fi/radioluvat-ja-taajuudet/radiotaajuuksien-kaytto/matkaviestinverkkojen-taajuudet-ja-luvanhaltijat. Luettu: 21.10.2026.
- Wikikirjasto. Radiotaajuuskirja/aallonpituus. Luettavissa: https://fi.wikibooks.org/wiki/Radiotaajuuskirja/aallonpituus. Luettu: 22.10.2026. 

