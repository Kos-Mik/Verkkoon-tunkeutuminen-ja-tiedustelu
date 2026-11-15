# h4 NFC ja RFID

### Ympäristö

- **Käyttöjärjestelmä:** Windows 11, Kali Linux 2026.3
- **Selain:** Firefox 153.4.0esr (64-bit).
- **Virtualisointialusta:** Oracle VirtualBox Version 7.2.16

## a) Käytössäni olevat RFID-tuotteet ja urkinnalta suojautuminen

Tällä hetkellä käytössäni olevat RFID-tuotteet ovat lähimaksulla varustettu pankkikortti, NFC-toiminnolla varustettu puhelin, taloyhtiön roskakuilun avaava lätkä, työpaikan kulkulätkä sekä aina yhtä epäluotettava iLOQ-älyavain, joka tuntuu avaavan taloni ovia aina silloin, kun sitä itseään sattuu huvittamaan.
Aiemmin minulla on ollut myös opiskelijakortti, HSL-matkakortti, salikortti sekä passi, mutta näiden palveluiden siirryttyä pitkälti mobiilipuolelle tai vanhennuttua ilman uusimista, käytössä olevien RFID-tuotteiden määrä minulla on vähentynyt merkittävästi.

RFID-urkinnalta en ole suojautunut niin hyvin kuin minulla olisi edellytyksiä. Minulla on kyllä RFID-suojattu maksukorttikotelo, mutta sen pieni koko yhdistettynä suureen määrään ns. tyhmiä kortteja sekä tapaani hamstrata kuitteja on saanut minut pysyttäytymään perinteisessä lompakossa.
Puhelimeni NFC-toiminto ei ole oletusarvoisesti päällä, koska en käytä puhelinta maksuvälineenä vähittäiskaupoissa, ja muutenkin käytän RFID-suojattua lompakkokoteloa. Avainnippu puolestaan on suojaamaton ja kulkee taskussa normaalisti muun tarpeellisen mukana. Tosin roskakuilun lätkää tai iLOQ-avainta en saanut skannattua millään testipuhelimen sovelluksella testattuani niitä nopeasti viime tunnilla. Tämä johtunee iLOQin monimutkaisemmista kopiosuojausominaisuuksista (iLOQ s.a.) ja siitä, ettei avaimen tietoja ole tarkoituskaan saada luettua yhtä helposti kuin monista tavallisista RFID- tai NFC-tunnisteista. Ainakaan omilla välineilläni ja osaamisellani siitä ei saanut irti mitään käyttökelpoista.

Sen sijaan työpaikan kulkulätkän tietoja oli helppo lukea puhelimen NFC Toolsilla, ja tuttavani, jolla on hallussaan Flipper Zero, näytti kuinka helposti sen myös pystyy kloonaamaan. Lätkää hallinnoi Abloy, joka on tarjonnut myös mahdollisuutta korvata kulkulätkän mobiilisovelluksella. Ongelmana on kuitenkin se, että ratkaisu perustuisi edelleen puhelimen NFC-toiminnon käyttöön, jota en normaalisti pidä päällä. Lisäksi joutuisin ottamaan puhelimeni pois RFID-suojatusta kotelosta aina sitä käyttäessäni.
Minulle on myös tullut vastaan tietynlainen raja siinä, miten moneen eri paikkaan ja sovellukseen oikeasti haluan rekisteröityä, koska tietojani tullaan kuitenkin myymään eteen päin kolmansille osapuolille. Ja koska oven avaaminen lätkällä on muutenkin huomattavasti nopeampi toimenpide kuin puhelimella, niin loppujen lopuksi näen helposti skannattavan kulkulätkän käytön tässä tapauksessa pienempänä pahana.

## b) APDU komentojen rakenteet

APDU (Application Protocol Data Unit) on yksinkertaistettuna tapa, jolla älykortit ja lukulaitteet keskustelevat keskenään. Sitä käytetään esim. maksukorteissa, kulkulätkissä, SIM-korteissa, passeissa ja FIDO2-tietoturvalaitteissa.

APDU perustuu ISO 7816 -standardiin, joka määrittelee miten tiedonsiirto kortin ja lukulaitteen välillä tapahtuu. Käytännössä homma toimii niin, että lukulaite lähettää kortille komennon ja kortti vastaa siihen. Tämän takia APDU:t jaetaan yleensä ns. Command APDU:n ja Response APDU:n (Yubico, s.a).

Komennossa on seuraavat peruskentät: **CLA** kertoo komentoluokan, **INS** varsinaisen käskyn ja **P1** sekä **P2** toimivat käskyn lisäparametreina. **Lc** kertoo lähetettävän datan pituuden, **Command data** sisältää kortille lähetettävän tiedon ja **Le** määrittää odotettavan vastauksen pituuden. Kortin vastauksessa puolestaan tulee mahdollisen datan lisäksi tilakoodit **SW1** ja **SW2**, joiden avulla laite tietää onnistuiko komennon suoritus. Esim. koodi **90 00** tarkoittaa yleensä sitä, että suoritus onnistui (CardLogix, s.a).

Loppujen lopuksi melkoinen kuhina käy todella lyhyellä aikavälillä. RFID- tai NFC-tunnisteen lukeminen ei olekaan pelkkää skannaamista, vaan laitteen ja kortin välillä käydään melko tarkkaan määriteltyä keskustelua, jossa lukija lähettää erilaisia komentoja ja kortti päättää, mitä tietoja se palauttaa vastauksena.

## c) Kiintoisa RFID-hakkerointiuutinen

RFID-hakkerointiin liittyvistä uutisista mielenkiintoisimpana pidin **SecurityWeek**in artikkelia, jossa kerrottiin miljoonia RFID-kortteja koskeneesta takaovesta. Quarkslabin tutkija Philippe Teuwen selvitti, että Shanghai Fudan Microelectronicsin valmistamissa MIFARE Classic -perheen RFID-korteissa oli tarkoituksellisesti lisätty toiminto, jonka avulla kortteja voitiin kloonata ilman varsinaisen suojauksen murtamista (Naraine, 2024).

Tapauksesta tekee kiinnostavan se, ettei kyse ollut perinteisestä ohjelmointivirheestä tai huonosta salasanasta, vaan valmistajan lisäämästä takaovesta. Käytännössä kortin turvallisuus voitiin ohittaa huomattavasti helpommin kuin käyttäjät tai järjestelmän ylläpitäjät olivat luultavasti ajatelleet.

Vaikka tämä on jo pari vuotta vanha uutinen, se on vaikuttanut sen verran laajasti, että se kiinnitti erityisesti huomioni. Kuten myös aiemmassa a-tehtävässä totesinkin, olin tunnilla kokeillut työpaikkani kulkulätkän skannaamista NFC Toolsilla ja nähnyt, kuinka helposti siitä sai luettua tietoja. Vaikka uutisessa käsitellyt kortit eivät välttämättä ole samoja kuin käyttämäni tunnisteet, tämä tapaus havainnollistaa mielestäni melko hyvin sitä, kuinka paljon RFID-järjestelmän turvallisuus riippuu myös kortin valmistajan ratkaisuista. Mielenkiintoisinta tässä tapauksessa onkin se, että joskus järjestelmän heikoin lenkki on se, että valmistaja on jättänyt laitteeseen takaoven jo alun perin. Tavallisena käyttäjänä on käytännössä mahdotonta tietää, onko omassa kulkulätkässä tai kortissa vastaavia ratkaisuja, joten RFID-tuotteiden todellinen turvallisuus ei välttämättä olekaan niin hyvä kuin voisi olettaa.

## Lähteet

- CardLogix s.a. Application Protocol Data Unit (APDU). Luettavissa: https://www.cardlogix.com/glossary/apdu-application-protocol-data-unit-smart-card/. Luettu 12.11.2026.
- iLOQ s.a. iLOQ S5 -tuotteet. Luettavissa: https://www.iloq.com/fi/katalogi/k5s-6/. Luettu. 13.11.2026.
- Naraine, B. 2024. Major Backdoor in Millions of RFID Cards Allows Instant Cloning. Luettavissa: https://www.securityweek.com/major-backdoor-in-millions-of-rfid-cards-allows-instant-cloning/. Luettu 15.11.2016.
- Yubico s.a. APDUs. Luettavissa: https://docs.yubico.com/yesdk/users-manual/yubikey-reference/apdu.html. Luettu 11.11.2016.
  
