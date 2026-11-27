# h5 WiFi

### Ympäristö

- **Isäntäkäyttöjärjestelmä:** Windows 11
- **Virtualisointialusta:** Oracle VirtualBox 7.2.16
- **Hyökkäys- ja analyysityöasema:** Kali Linux 2026.3
- **Verkon emulointialusta:** WiFiChallenge Lab v2.5.1
- **WiFiChallenge Lab v2.5.1 käyttöjärjestelmä:** Debian (64-bit)
- **Selain:** Firefox 153.4.0esr (64-bit)

## a) Tehtävät

### Introduction

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

Seuraavaksi voidaan jatkaa **Recon**-osion tehtäviä.

### Recon

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

- **p** valitsee ns. SSID Probing and Bruteforcing -tilan, jossa ei pyritä murtamaan salasanaa vaan selvittämään piilotetun tukiaseman nimi.
- **-t** lähettää probe-pyynnöt halutulle tukiasemalle (eli tässä tapauksessa tukiasemalle jonka MAC on F0:9F:C2:6A:88:26).
- **-f** lukee sanalistat tiedostosta (Arch Linux, s.a)

<p align="center">
<img width="574" height="234" alt="image" src="https://github.com/user-attachments/assets/72f90938-6bab-4250-9520-6a5df994e66c" />
  <br>
  <em>Ei ole wifi-love eikä wifi-butt, mutta...</em>
</p>

Oikea vastaus on siis **wifi-free**. Sinänsä melko johdonmukaista, kun kyseessä oli myös avoin verkko. Tosin **wifi-butt** on sellainen SSID, jonka voisin ottaa itselleni talteen tulevaisuutta varten...

### OPN

#### 05. What is the flag in the hidden AP router behind default credentials?

Eli jatketaan pidemmälle vielä mitä edellisessä tehtävässä oltiin jo löydetty. Tarkistetaan alkuun WLAN-rajapinnat komennolla ```iw dev```:

```bash
phy#70
	Interface wlan70
		ifindex 77
		wdev 0x4600000001
		addr 02:00:00:00:46:00
		type monitor
		channel 64 (5320 MHz), width: 20 MHz (no HT), center1: 5320 MHz
		txpower 20.00 dBm
phy#5
	Interface wlan5
		ifindex 12
		wdev 0x500000001
		addr 02:00:00:00:05:00
		type managed
		txpower 0.00 dBm
phy#4
	Interface wlan4
		ifindex 11
		wdev 0x400000001
		addr 02:00:00:00:04:00
		type managed
		txpower 0.00 dBm
phy#3
	Interface wlan3
		ifindex 10
		wdev 0x300000001
		addr 02:00:00:00:03:00
		type managed
		txpower 0.00 dBm
phy#2
	Interface wlan2
		ifindex 9
		wdev 0x200000001
		addr 02:00:00:00:02:00
		type managed
		txpower 0.00 dBm
phy#1
	Interface wlan1
		ifindex 8
		wdev 0x100000001
		addr 02:00:00:00:01:00
		type managed
		txpower 0.00 dBm
phy#0
	Interface wlan0
		ifindex 7
		wdev 0x1
		addr 02:00:00:00:00:00
		type managed
		txpower 0.00 dBm
```

Koska wlan70 on **monitor**-tilainen rajapinta, on löydettävä verkkoon liittymiseen tarvittava **managed**-tilainen rajapinta. Koska kaikki managed-rajapinnat näyttivät samanlaisilta, päätin aloittaa **wlan0**-rajapinnasta, koska se on tavallisesti ensisijainen WLAN-rajapinta Linux-järjestelmissä. Otetaan verkkokortti käyttöön, jotta sillä voidaan skannata ja muodostaa yhteyksiä:

```bash
sudo ip link set wlan0 up
```

Koska meillä on edellisessä tehtävässä tiedossa halutun tukiaseman MAC-osoite, suoritetaan WLAN-skannaus, josta etsitään BSSID F0:9F:C2:6A:88:26 ja näytetään kyseisen tukiaseman tiedot sekä niitä ympäröivät rivit tarkempaa analysointia varten:

```bash
sudo iw dev wlan0 scan | grep -i -A30 -B10 "f0:9f:c2:6a:88:26"
```

<p align="center">
<img width="612" height="190" alt="image" src="https://github.com/user-attachments/assets/93951e2a-1b14-4eac-8572-45fba84d2dbb" />
  <br>
  <em>Tuttu MAC</em>
</p>

Eli skannaus vahvisti, että kyseessä oli sama piilotettu tukiasema kuin aiemmin. Koska edellisessä tehtävässä oltiin selvitetty ESSID (wifi-free), muodostetaan yhteys, haetaan IP-osoite DHCP:llä ja tarkistetaan, että osoite on saatu:

```bash
sudo iw dev wlan0 connect wifi-free
```

```bash
sudo dhclient wlan0
```

```bash
ip addr show wlan0
```

<p align="center">
<img width="883" height="150" alt="image" src="https://github.com/user-attachments/assets/4cf94209-af2a-4531-a987-c3a2dc3b8915" />
  <br>
  <em>Yhteys muodostettu!</em>
</p>

DHCP-palvelin oli myöntänyt osoitteen ja verkkoyhteys oli muodostunut onnistuneesti. Tarkistetaan vielä reititystiedot komennolla ```ip route``` ja tulosteesta näkyi, että WLAN-rajapinta on nyt liitetty verkkoon (**192.168.16.0/24 dev wlan0 proto kernel scope link src 192.168.16.61**) eli myös tukiasema on samassa aliverkossa. Kokeilin pingata default gateway -osoitteeseen, joka tässä tapauksessa oli 192.168.16.1 (default gateway käyttää usein aliverkon ensimmäistä osoitetta (.1), joten kyseinen osoite oli siksi luonnollinen tarkistuskohde). Kaikki pingaukset saivat vastauksen. 

Kartoitetaan vielä varmuuden vuoksi tukiaseman palvelut porttiskannauksella:

```bash
nmap -sV 192.168.16.1
```
<p align="center">
<img width="675" height="214" alt="image" src="https://github.com/user-attachments/assets/fe50c19e-bb69-4411-9ce7-b4453037988c" />
  <br>
  <em>Porttiskannauksen tulos</em>
</p>

HTTP- ja HTTPS-palveluiden perusteella voitiin päätellä, että tukiasemassa on selainpohjainen hallintaliittymä, joten kokeillaan selaimella mennä osoitteeseen https://192.168.16.1

<p align="center">
<img width="721" height="758" alt="image" src="https://github.com/user-attachments/assets/e0b17041-5b5d-412e-a92f-553446217cf7" />
  <br>
  <em>Hallintaliittymä - löydetty'd!</em>
</p>

Yritin alkuun muutamaa ilmiselvää käyttäjänimi + salasana -yhdistelmää, kuten **username** + **password**, mutta nämä eivät olleet oikeita. Lopulta ihan perus **admin** + **admin** päästi sisälle ja lippu löytyi.

<p align="center">
<img width="557" height="405" alt="image" src="https://github.com/user-attachments/assets/75bf42d3-c2f4-47c8-b1fd-84c5fc5e9693" />
  <br>
  <em>Lippu - löydetty'd!</em>
</p>


### Legacy

#### 07. What is the flag on the wifi-old AP website?

Aloitetaan tehtävä taas käyttämällä airodumpia wlan70-rajapinnassa:

```bash
sudo airodump-ng wlan70
```

<p align="center">
<img width="877" height="488" alt="image" src="https://github.com/user-attachments/assets/b604998d-ea32-4a44-97d7-f39eb738feca" />
  <br>
  <em>WEP, heh.</em>
</p>

Tehdään tässä samalla tavalla kuin Larin luentomateriaalissa eli generoidaan **besside-ng**-työkalulla alkuun liikennettä ja annetaan tämän pyöriä hetken aikaa:

```bash
sudo besside-ng -c 3 -b F0:9F:C2:71:22:11 wlan70 -v
```
**-c 3**, koska wifi-old käyttää 3-kanavaa, kuten airodumpin tulosteessa näkyi.

Käynnistetään tämän jälkeen **aircrack-ng**-työkalu, joka analysoi kaapatut WEP-paketit ja niissä esiintyvät IV-arvot (Initialization Vectors).

```bash
sudo aircrack-ng wep.cap
```

<p align="center">
<img width="724" height="327" alt="image" src="https://github.com/user-attachments/assets/91a2d6de-6718-4fc8-ad6d-15a9f24fc648" />
  <br>
  <em>Avain - löydetty'd</em>
</p>

Tämä ei kuitenkaan ole vielä lippu, koska tehtävänantona löytää se tukiaseman sivuilta.

Koska wlan0 on edelleen edellisen tehtävän jäljiltä yhteydessä wifi-free -verkkoon, katkaistaan yhteys ja tarkistetaan yhteys:

```bash
sudo iw dev wlan0 disconnect
```

```bash
iw dev wlan0 link
```

**Not connected**. Vapautetaan vielä vanha DHCP lease:

```bash
sudo dhclient -r wlan0
```

**Killed old client process**. Kokeillaan tämän jälkeen WEP-verkkoa ja tarkistetaan, että yhdistäminen on onnistunut:

```bash
sudo iw dev wlan0 connect wifi-old key d:0:11BB33CD55
```

```bash
iw dev wlan0 link
```

<p align="center">
<img width="403" height="252" alt="image" src="https://github.com/user-attachments/assets/49cd406f-9dfd-4dc3-b24d-dbdc6726f2ab" />
  <br>
  <em>I'm in!</em>
</p>

Haetaan IP-osoite DHCP:llä ja tarkistetaan, että osoite on saatu:

```bash
sudo dhclient wlan0
```

```bash
ip addr show wlan0
```

<p align="center">
<img width="728" height="123" alt="image" src="https://github.com/user-attachments/assets/ba4aa472-127e-44fd-be5f-4811d26b9dee" />
  <br>
  <em>Yhteys muodostettu!</em>
</p>

DHCP-palvelin oli myöntänyt osoitteen ja verkkoyhteys oli muodostunut onnistuneesti. Tarkistetaan vielä reititystiedot komennolla ```ip route``` ja tulosteesta näkyi, että WLAN-rajapinta on nyt liitetty verkkoon (**192.168.1.0/24 dev wlan0 proto kernel scope link src 192.168.1.61**) eli myös tukiasema on samassa aliverkossa. Tuntuu, että olisin tehnyt tämän joskus aiemminkin. Pingattu jälleen oletettuun default gatewayhyn (192.168.1.1) ja se vastasi. Tehdään vielä samaan syssyyn porttiskannaus:

```bash
nmap -sV 192.168.1.1
```

<p align="center">
<img width="708" height="211" alt="image" src="https://github.com/user-attachments/assets/a3bebce2-de60-497e-afe6-5c6e98764164" />
  <br>
  <em>Hyvältä näyttää.</em>
</p>

Eli HTTP- ja HTTPS -palvelut ovat tässäkin olemassa. Mennään siis selaimella default gateway -osoitteeseen, ja lippu löytyikin jo ennen autentikointia.

<p align="center">
<img width="670" height="824" alt="image" src="https://github.com/user-attachments/assets/c3656953-cc59-4cdd-9df6-bc8837901b2f" />
  <br>
  <em>Lippu - löydetty'd</em>
</p>






## Lähteet

Arch Linux s.a. MDK4(2) System Calls Manual. Luettavissa: https://man.archlinux.org/man/extra/mdk4/mdk4.8.en. Luettu: 26.11.2026.
