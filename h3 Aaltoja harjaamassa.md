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

Laitetyyppiä ei voi analyysin perusteella täysin varmentaa, mutta epäilys on, että näytteessä analysoidaan kauko-ohjattavia pistorasioita. rtl_433 tunnisti signaalin Nexa-Security- ja KlikAanKlikUit-Switch-yhteensopivaksi. Nexa-tuotemerkin kauko-ohjattavia pistorasioita myydään Suomessa monissa elektroniikkaliikkeissä (itsellänikin on käytössä Nexan kauko-ohjattavat pistorasiat), kun taas KlikAanKlikUit on Nexan hollantilainen vastine, joka toimii samalla tavalla (Homey, 2018). 



https://github.com/user-attachments/assets/696c258e-b8ef-45d9-baa5-0b9696e33075




## Lähteet

Homey 2018. Using Nexa devices with Homey. Luettavissa: https://homey.app/en-us/blog/using-nexa-devices-with-homey/. Luettu: 4.11.2026.
