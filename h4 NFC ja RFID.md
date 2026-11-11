# h4 NFC ja RFID

### Ympäristö

- **Käyttöjärjestelmä:** Windows 11, Kali Linux 2026.3
- **Selain:** Firefox 153.4.0esr (64-bit).
- **Virtualisointialusta:** Oracle VirtualBox Version 7.2.16

## a) Käytössäni olevat RFID-tuotteet ja urkinnalta suojautuminen


Tällä hetkellä käytössäni olevat RFID-tuotteet ovat lähimaksulla varustettu pankkikortti, NFC-toiminnolla varustettu puhelin, taloyhtiön roskakuilun avaava lätkä, työpaikan kulkulätkä sekä aina yhtä epäluotettava iLOQ-älyavain, joka tuntuu avaavan taloni ovia aina silloin, kun sitä itseään sattuu huvittamaan.
Aiemmin minulla on ollut myös opiskelijakortti, HSL-matkakortti, salikortti sekä passi, mutta näiden palveluiden siirryttyä pitkälti mobiilipuolelle tai vanhennuttua ilman uusimista jatkuvasti käytössä olevien RFID-tuotteiden määrä on vähentynyt merkittävästi.

RFID-urkinnalta en ole suojautunut niin hyvin kuin minulla olisi edellytyksiä. Minulla on kyllä RFID-suojattu maksukorttikotelo, mutta sen pieni koko yhdistettynä suureen määrään ns. tyhmiä kortteja sekä tapaani hamstrata kuitteja on saanut minut pysyttäytymään perinteisessä lompakossa.
Puhelimeni NFC-toiminto ei ole oletusarvoisesti päällä, ja käytän RFID-suojattua lompakkokoteloa. Avainnippu puolestaan on suojaamaton ja kulkee taskussa normaalisti muun tarpeellisen mukana. Tosin roskakuilun lätkää tai iLOQ-avainta en saanut skannattua millään testipuhelimen sovelluksella testattuani niitä nopeasti viime tunnilla.

Sen sijaan työpaikan kulkulätkän tietoja oli helppo lukea puhelimen NFC Toolsilla, ja tuttavani, jolla on hallussaan Flipper Zero, näytti kuinka helposti sen myös pystyy kloonaamaan. Lätkää hallinnoi Abloy, joka on tarjonnut myös mahdollisuutta korvata kulkulätkän mobiilisovelluksella. Ongelmana on kuitenkin se, että ratkaisu perustuisi edelleen puhelimen NFC-toiminnon käyttöön, jota en normaalisti pidä päällä. Lisäksi joutuisin ottamaan puhelimeni pois RFID-suojatusta kotelosta aina sitä käyttäessäni.
Minulle on myös tullut vastaan tietynlainen raja siinä, miten moneen eri paikkaan ja sovellukseen oikeasti haluan rekisteröityä, koska näihin laittamiani tietoja tullaan kuitenkin myymään eteen päin kolmansille osapuolille. Loppujen lopuksi näen helposti skannattavan kulkulätkän tässä tapauksessa pienempänä pahana.

## b) rtl_433
