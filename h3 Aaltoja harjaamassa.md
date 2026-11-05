# h3 Aaltoja harjaamassa

### Ympäristö

- **Käyttöjärjestelmä:** Windows 11, Kali Linux 2026.3
- **Selain:** Firefox 153.4.0esr (64-bit).
- **Virtualisointialusta:** Oracle VirtualBox Version 7.2.16


## x) Tiivistelmät

### Hubacek 2019


### Cornelius 2022

### 

## b) rtl_433

Ensiksi asennetaan Kali-virtuaalikoneelle **rtl_433**-ohjelma komennolla ```sudo apt install rtl-433``` ja ajetaan komento ```./rtl_433``` testiksi, että asennus on onnistunut ja ohjelma voidaan suorittaa:

<br>
<br>
<p align="center">
<img width="643" height="105" alt="image" src="https://github.com/user-attachments/assets/ea447e0e-ddcd-4386-a335-9f8bb8e2c938" />
  <br>
  <em>Asennus on onnistunut. Minulla ei ole mitään ylimääräisiä laitteita kiinni koneessani, joten no supported devices found -ilmoitus on odotettu.</em>
</p>

## c) Automaattinen analyysi

Tässä tehtävässä analysoidaan rtl_443-ohjelmalla **Converted_433.92M_2000k.cs8**-tiedostoa. Tarkoituksena on mm. selvittää näytteessä tapahtuu ja mitä tunnisteita siitä löytyy.

Lataan ensin tiedoston samaan hakemistoon, johon rtl_443 on myös asennettu, jonka jälkeen lähden analysoimaan tiedostoa:

```bash
wget https://terokarvinen.com/verkkoon-tunkeutuminen-ja-tiedustelu/samples/Converted_433.92M_2000k.cs8
```

```bash
rtl_433 -r Converted_433.92M_2000k.cs8
```

<p align="center">
<img width="800" height="536" alt="image" src="https://github.com/user-attachments/assets/989aabdd-0946-4c38-821f-7df392c63deb" />
<img width="802" height="624" alt="image" src="https://github.com/user-attachments/assets/0459f1d9-b0df-4f8d-a07a-e746318ac22e" />
  <br>
  <em>Analyysin tulos.</em>
</p>

Analyysin kautta löytyi seuraavia tunnisteita:

- **House Code/ID:** 8785315
- **Channel:** 3
- **Unit:** 3
- **Command:** OFF
- **Group Call:** No
- **Group:** 0

rtl_433 tunnisti signaalin **KlikAanKlikUit-Switch**-, **Proove-Security**- ja **Nexa-Security** -yhteensopivaksi laiteprotokollaksi. House Code/ID on kaikissa sama eli 8785315. Lähetettävä komento on OFF, joten tarkoituksena näyttäisi olevan laitteen sammuttaminen. Sama viesti esiintyy neljä kertaa tallenteessa, ne ovat sisällöltään identtisiä ja niiden väliset ajat ovat noin 80 ms, joten joko kyseessä on todella vikkeläsorminen ihminen tai sitten näin nopea ja tasavälinen toistuminen on todennäköisemmin laitteen ominaisuus.

Laitetyyppiä ei voi analyysin perusteella täysin varmentaa, mutta epäilys on, että näytteessä analysoidaan kauko-ohjattavia pistorasioita (tämä tosin varmistui myöhemmässä tehtävässä, mutta koska perisyntini on aina ollut tunkea kädet heti saveen sen sijaan, että luen tehtävänannot kokonaan ja huolella, en tätä tehtävää tehtäessä vielä tienny sitä). rtl_433 tunnisti signaalin Nexa-Security- ja KlikAanKlikUit-Switch-yhteensopivaksi. Nexa-tuotemerkin kauko-ohjattavia pistorasioita myydään Suomessa monissa elektroniikkaliikkeissä [(itsellänikin on käytössä Nexan kauko-ohjattavat pistorasiat)](https://youtu.be/LNihJGIleYU?t=1414), kun taas KlikAanKlikUit on Nexan hollantilainen vastine, joka toimii samalla tavalla (Homey, 2018). 



## d) Too compex 16?

Tällä kertaa tarkoituksena on ladata .complex16s-muotoinen tiedosto, muuntaa se rtl_433-yhteensopivaan muotoon ja analysoida sitä.

Ensin siis ladataan tiedosto nimeltä **Recorded-HackRF-20250411_183354-433_92MHz-2MSps-2MHz.complex16s**, jonka jälkeen se muunnetaan uudelleen muotoon **tehtavaD_433.92M_2000k.cs8** ja avataan analysoitavaksi seuraavilla komennoilla:

```bash
wget https://terokarvinen.com/verkkoon-tunkeutuminen-ja-tiedustelu/samples/Recorded-HackRF-20250411_183354-433_92MHz-2MSps-2MHz.complex16s
```
```bash
mv Recorded-HackRF-20250411_183354-433_92MHz-2MSps-2MHz.complex16s tehtavaD_433.92M_2000k.cs8
```
```bash
rtl_433 -r tehtavaD_433.92M_2000k.cs8
```

<p align="center">
<img width="853" height="556" alt="image" src="https://github.com/user-attachments/assets/f5dac503-999a-4ba1-af01-39d776d18370" />
  <br>
  <em>Heeeetkinen!</em>
</p>

Kun tiedoston muuntaminen oli tehty ja se oli avattu, analyysin tulos oli sama kuin edellisessä tehtävässä: signaali tunnistui KlikAanKlikUit-Switch-, Proove-Security- ja Nexa-Security-yhteensopivaksi, House Code / ID oli 8785315, komento OFF jne. Tässä kohtaa minua alkoi epäilyttää, että olisiko tehtävässä jokin jäynä tai bugi. Lähdin vertailemaan edellisen C-osion tehtävän tiedostoa ja nyt muunnettua tiedostoa keskenään:

<p align="center">
<img width="614" height="351" alt="image" src="https://github.com/user-attachments/assets/824dcc60-cf0d-4135-897f-375c57496cb2" />
  <br>
  <em>Samalta näyttää.</em>
</p>

Vaikka todisteet puhuvat puolestaan, en ollut vieläkään täysin vakuuttunut. Lähdin siis vertailemaan tiedostojen SHA-256-tiivisteitä keskenään vain ollakseni täysin varma asiasta:

<p align="center">
<img width="1043" height="212" alt="image" src="https://github.com/user-attachments/assets/8fcd07e9-c18c-4fce-aff4-5b882d8016fb" />
  <br>
  <em>Edelleenkin samalta näyttää.</em>
</p>

Pakko se on uskoa... tiedostot ovat sisällöltään täysin identtiset, vaikka niiden alkuperäiset nimet ja tallennusmuoto poikkeavat toisistaan. Tehtävä on siis suoritettu.

## d) Ultimate

Nyt asennetaan virtuaalikoneelle **URH** eli **Universal Radio Hacker**. **pipx**-työkalu ja siihen tarvittavat säädöt olivat Kaliin jo valmiiksi asennettuina, joten tässä vaiheessa komennoksi riitti vain ```pipx install urh```, jonka jälkeen URH voitiin käynnistää:


<p align="center">
<img width="793" height="505" alt="image" src="https://github.com/user-attachments/assets/b59b7f77-c726-4e36-9b21-f42e9376cec2" />
  <br>
  <em>Käynnistetty URH</em>
</p>

## f) Yleiskuva

Tarkoituksena tässä tehtävässä on tarkastella **1-on-on-on-HackRF-20250412_113805-433_912MHz-2MSps-2MHz.complex16s**-näytettä ja antaa siitä yleiskuvaus. Tehtävänannossa tarkennetaan vielä, että näytteessä on Nexan pistorasian kaukosäätimen valon 1 ON -nappia painettu kolmesti. 

Ihan jo tiedoston nimen perusteella tallennus on tehty 12.4.2025 klo 11:38:05 taajuudella 433,912 MHz ja näytteenottotaajuudella 2 MS/s. Kun URH:lla tarkastelee tiedostoa esim. **Signal view**n kautta, näkee selvästi kolme erillistä lähetyspursketta, jotka ainakin silmämääräisesti näyttävät samanlaisilta. Tämä vastaa tehtävänannon tietoa siitä, että kaukosäätimen nappia on painettu kolmesti. Yksittäisiä bittejä tutkimalla pystyy näkemään aikaleimat, ja viimeisen bitin kohdalla aikaleima on 5,37 sekuntia. Tuntematta paremmin URH:n toimintaa tässä vaiheessa, epäilisin tämän olevan tallenteen kokonaispituus.

<p align="center">
<img width="1091" height="589" alt="image" src="https://github.com/user-attachments/assets/b43a974d-2e2f-4b13-96a2-f27573be0bcb" />
<img width="1038" height="317" alt="image" src="https://github.com/user-attachments/assets/e78f0ce5-7141-4470-b819-5f8581fa6381" />
  <br>
  <em>Lähetyspurskeet ja aikaleima</em>
</p>



## g) Bittistä



## Lähteet

Homey 2018. Using Nexa devices with Homey. Luettavissa: https://homey.app/en-us/blog/using-nexa-devices-with-homey/. Luettu: 4.11.2026.
