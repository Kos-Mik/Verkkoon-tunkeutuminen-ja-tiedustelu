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

<p align="center">
<img width="643" height="105" alt="image" src="https://github.com/user-attachments/assets/ea447e0e-ddcd-4386-a335-9f8bb8e2c938" />
  <br>
  <em>Asennus on onnistunut. Minulla ei ole mitään ylimääräisiä laitteita kiinni koneessani, joten no supported devices found -ilmoitus on odotettu.</em>
</p>

## c) Automaattinen analyysi

Tässä tehtävässä analysoidaan rtl_443-ohjelmalla **Converted_433.92M_2000k.cs8**-tiedostoa. Tarkoituksena on mm. selvittää näytteessä tapahtuu ja mitä tunnisteita siitä löytyy.

Lataan ensin tiedoston samaan hakemistoon, johon rtl_443 on myös asennettu komennolla jonka jälkeen lähden analysoimaan tiedostoa:


```bash
wget https://terokarvinen.com/verkkoon-tunkeutuminen-ja-tiedustelu/samples/Converted_433.92M_2000k.cs8
```
```bash
rtl_433 -r Converted_433.92M_2000k.cs8
```
