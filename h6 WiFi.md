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
<summary><b>Katso vastaus</b></summary>
  
  <br>
  
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
<details>
<summary><b>Katso vastaus</b></summary>

<br>

<p align="center">
<img width="886" height="531" alt="image" src="https://github.com/user-attachments/assets/ac003268-d081-46ba-a8b3-2a8b41105a62" />
  <br>
  <em>Kanava</em>
</p>

Oikea kanava oli siis **44**.

</details>

#### 02. What is the MAC of the wifi-IT client?

Edellisessä tehtävässä näkyvä **F0:9F:C2:1A:CA:25** ei ollut oikea vastaus, koska airodump-ng jakaa näkymän kahteen osaan: **yläosa** on tukiasemat ja **alaosa** asiakaslaite (clients/stations).
Samaa komentoa käyttäen kuin aiemmassakin tehtävässä, annoin airodumpin pyöriä tällä kertaa kauemmin:

<details>
<summary><b>Katso vastaus</b></summary>

<br>

<p align="center">
<img width="822" height="559" alt="image" src="https://github.com/user-attachments/assets/44474db1-329f-4511-9036-06c6d6cb7ac5" />
  <br>
  <em>MAC</em>
</p>

Oikea MAC oli siis **10:F9:6F:AC:53:52**

</details>

#### 03. What is the probe of 78:C1:A7:BF:72:46?

Tässä tehtävässä annoin taas airodumpin pyöriä pidempään. Airodump-ng pitää muistissaan vain viime aikoina havaittuja asemia, joten jotkut vilahtivat taulukossa melko nopeasti ja katosivat sen jälkeen. Onnekkaan sattuman kautta löysin 78:C1:A7:BF:72:46 lähettämän probe-pyynnön ensimmäisellä kerralla:

<details>
<summary><b>Katso vastaus</b></summary>

<br>

<p align="center">
<img width="885" height="145" alt="image" src="https://github.com/user-attachments/assets/b99d9b69-73d9-48e7-9952-90af923a0b58" />
  <br>
  <em>Probe</em>
</p>


Oikea vastaus: **wifi-offices**

Tarvittaessa osoitetta oltaisiin voitu myös etsiä tallentamalla ensin kaappauksen tiedostoon: 

```bash
sudo airodump-ng --write capture wlan70
```

Tämä luo erilaisia tiedostoja, kuten esim. **capture-01.csv**, **capture-01.cap** yms. Lukujärjestys riippuu ihan siitä montako kaappaustiedostoa on jo aiemmin luotu.
Näistä voi etsiä MAC-osoitteen, jos olisi sattunut menemään liveseurannassa ohi:

```bash
grep "78:C1:A7:BF:72:46" capture-01.csv
```

<p align="center">
<img width="1052" height="44" alt="image" src="https://github.com/user-attachments/assets/5c27f3ee-3c54-4dd6-8843-24e7b6cf3f96" />
  <br>
  <em>Probe löytyy näinkin</em>
</p>



</details>

#### 04. What is the ESSID of the hidden AP (mac F0:9F:C2:6A:88:26)?

Tällä kertaa tulikin hieman hankalampi tapaus, joten oli pakko käyttää vihjeitä: 

<details>
<summary><b>Katso vihjeet</b></summary>

<br>

```bash
There are no clients. Brute force!
```
```bash
Bruteforce the ESSID using rockyou.txt with some modification
```
```bash
mdk4
```
</details>

Aiempien tehtävien airodumpista näkee, että kyseisen MAC-osoitteen omaava ESSID on 9 merkin pituinen.

<p align="center">
<img width="812" height="322" alt="image" src="https://github.com/user-attachments/assets/1647ead8-d939-4619-8470-d1d09f570046" />
  <br>
  <em>9 merkin pituinen ESSID</em>
</p>

Vihjeen perusteella ladataan siis **rockyou.txt**-tiedosto:

```bash
wget https://github.com/brannondorsey/naive-hashcat/releases/download/data/rockyou.txt
```

Huomasin myös, että iso osa ESSID:stä alkaa wifi-alkuisesti. Muutin siis rockyou.txt-tiedostoa niin, että jokaisen sanan eteen tulee wifi-alkuliite, ja koska rockyoussa on yhteensä tällä hetkellä **14344391** sanaa, on järkevää trimmata sanojen määrää alkuliitemuunnoksen jälkeen vielä niin, että merkkejä on yhteensä 9 (eli **wifi-** + **4** merkkiä).

```bash
sed 's/^/wifi-/' rockyou.txt > wifi-rockyou.txt
```
```bash
awk 'length($0)==4 {print "wifi-"$0}' rockyou.txt > wifi4.txt
```

Nyt saatiin sanojen määrää karsittua 17899 kappaleeseen.

Tämän jälkeen käytetään **mdk4**-työkalua yhdessä uuden wifi4.txt-sanalistan kanssa:

```bash
sudo mdk4 wlan70 p \
  -t F0:9F:C2:6A:88:26 \
  -f wifi4.txt
```

<p align="center">
<img width="574" height="234" alt="image" src="https://github.com/user-attachments/assets/72f90938-6bab-4250-9520-6a5df994e66c" />
  <br>
  <em>Ei ole wifi-love eikä wifi-butt, mutta...</em>
</p>

Oikea vastaus on siis **wifi-free**. Sinänsä melko johdonmukaista, kun kyseessä oli myös avoin verkko. Tosin wifi-butt on sellainen SSID, jonka otan itselleni talteen tulevaisuutta ajatellen...


