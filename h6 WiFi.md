# h5 WiFi

### Ympäristö

- **Isäntäkäyttöjärjestelmä:** Windows 11
- **Virtualisointialusta:** Oracle VirtualBox 7.2.16
- **Hyökkäys- ja analyysityöasema:** Kali Linux 2026.3
- **Verkon emulointialusta:** WiFiChallenge Lab v2.5.1
- **WiFiChallenge Lab v2.5.1 käyttöjärjestelmä:** Debian (64-bit)
- **Selain:** Firefox 153.4.0esr (64-bit)

## a) Tehtävät

Aloitetaan lineaarisesti WiFiChallenge Labin **Introduction**-osion lämmittelykierroksella:

#### 00. What is the content of the file /root/flag.txt on the VM?

Tarkastetaan, että kyseinen tiedosto on oikeasti olemassa:
```bash
ls -ld /root/flag.txt
```
Katsotaan tiedoston sisältö:
```bash
cat /root/flag.txt
```

<details>
<summary>Katso vastaus</summary>
  
**flag{2162ae75cdefc5f731dfed4efa8b92743d1fb556}**

<p align="center">
<img width="738" height="111" alt="image" src="https://github.com/user-attachments/assets/a8b0da61-8cbd-4d99-abd6-7deb36c1486f" />
  <br>
  <em>Lippu</em>
</p>


</details>

Seuraavaksi voidaan jatkaa **Recon**-osion tehtäviä:

#### 01. What is the channel that the wifi-global Access Point is currently using?

Aluksi katsotaan käytettävissä olevat verkkorajapinnat:
```bash
iw dev
```

<p align="center">
<img width="728" height="167" alt="image" src="https://github.com/user-attachments/assets/c2e2e332-7b2a-47d2-ba1d-aff3b3d3aeda" />
  <br>
  <em>Ainoa verkkorajapinta, jossa oli toimintaa</em>
</p>

64 ei kuitenkaan ole oikea vastaus, koska tehtävässä kysyttiin tukiaseman eikä monitori-rajapinnan kanavaa. 

Käytetään siis **airodump**-työkalua kuuntelemaan langatonta liikennettä ja listaamaan ympäristön tukiasemat ja niiden ominaisuudet:

```bash
airodump-ng wlan70
```

<p align="center">
<img width="886" height="531" alt="image" src="https://github.com/user-attachments/assets/ac003268-d081-46ba-a8b3-2a8b41105a62" />
  <br>
  <em>Oikea kanava löytyi</em>
</p>

Oikea kanava oli siis **44**.

#### 02. What is the MAC of the wifi-IT client?


