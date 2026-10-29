# h2 Nuuh

### Ympäristö

- **Käyttöjärjestelmä:** Windows 11, Kali Linux 2026.3
- **Selain:** Firefox 153.4.0esr (64-bit).
- **Virtualisointialusta:** Oracle VirtualBox Version 7.2.16


## x) Tiivistelmät

### Wireshark - Getting Started

### Network Interface Names on Linux


## a) Linux

Minulla on jo valmiiksi VirtualBoxille asennettu Kali Linux (2026.3), koska olen aiemmin suorittanut Teron ja Larin **Tunkeutumistestaus**- ja **Sovellusten hakkerointi** -kurssit, joilla tämä käyttis on ollut enemmän tai vähemmän välttämättömyys.
Sitä ei siis ole tarvinnut asentaa tätä kurssia varten uudelleen.

<p align="center">
<img width="956" height="801" alt="image" src="https://github.com/user-attachments/assets/ba8b8194-b364-4359-a794-d9bc0166b66e" />
  
  <br>
  <em>Alempi Kali nykyisessä käytössä, koska ylempi meni jossain vaiheessa todella jumiin. Säästin sen vain siksi, että siellä on Tunkeutumistestaus-kurssilta materiaalia.</em>
</p>

## b) Ei voi kalastaa

Nyt on tarkoitus osoittaa, että pystyn katkaisemaan ja palauttamaan virtuaalikoneen nettiyhteyden. Vannoutunut Linux-väki varmasti tekisi tämän terminaalin kautta, mutta koska olen laiska ja siitä huolimatta, että rakkauteni Linuxia kohtaan vain jatkaa kasvamistaan, 
olen silti yli 30 vuoden ajan ollut Windows-mies (sen huomaa jo siitäkin, että Kalissa käyttämäni Desktop environment on Cinnamon eli hyvin Windows-tyyppinen työpöytäympäristö). Siispä teen GUI:n avulla tällaisia yksinkertaisia toimenpiteitä, koska sitähän varten sellainen on luotu:

<p align="center">
<img width="232" height="177" alt="image" src="https://github.com/user-attachments/assets/4f6cac41-65d9-4cd4-89fe-362cc422c4a3" />
  <br>
<img width="506" height="65" alt="image" src="https://github.com/user-attachments/assets/dfbaa9fd-ff3e-4322-8ef6-f46a0a2448ba" />
  <br>
  <em>Yhteys katkaistu ja pingattu 8.8.8.8 eli Googlen julkiseen DNS-palvelimeen, joka yleensä on hyvä tapa testata nettiyhteyden toimivuutta. Yhteyttä ei siis voida muodostaa, koska verkkoa ei käytettävissä ollenkaan.</em>
</p>

<br>

<p align="center">
<img width="232" height="176" alt="image" src="https://github.com/user-attachments/assets/6406fd75-5818-4112-bbcf-ddf573fcf9ea" />
  <br>
<img width="505" height="213" alt="image" src="https://github.com/user-attachments/assets/7e1805df-6306-4d72-b7fa-0ba748026ae1" />
  <br>
 <em>Yhteys palautettu ja pingattu samalla tavoin 8.8.8.8 - tällä kertaa onnistuneesti.</em>
</p>

Eli kuten iltapäivälehdistö asian ilmaisisi, tällä yksinkertaisella ranneliikkeellä nettiyhteys saadaan katkaistua ja palautettua takaisin virtuaalikoneessa luotettavasti.

## c) Wireshark

Kalissa Wireshark kuuluu jo valmiiksi esiasennettuihin sovelluksiin, joten tätä ei tarvinnut lähteä erikseen asentamaan. Käynnistin Wiresharkin ja käynnistin pakettien kaappauksen, jonka jälkeen ajoin terminaalissa komennon ```curl yle.fi``` ja suodatin Wiresharkin kaappaamat DNS-tiedot:

<p align="center">
<img width="1314" height="552" alt="image" src="https://github.com/user-attachments/assets/bad84ab6-140b-44ae-b491-ba1d62f7fc41" />
  
  <br>
  <em>Wiresharkin kaappaamat DNS-tiedot</em>
</p>

<br>

Tästä nähdään, että vaikka virtuaalikone on NAT-verkossa (10.0.2.15), DNS-palvelimeksi on päätynyt sisäverkon palomuuri osoitteessa 192.168.xx.xx. Minulla on vanha Sophos XG115 -mallinen palomuuri, johon on asennettu avoimen lähdekoodin OPNsense-palomuuri- ja reititinalusta. Kaappauksesta näkyy, että DNS-kysely ei mennyt suoraan esim. Googlelle tai Cloudflarelle, vaan OPNsense vastasi siihen. Kali käyttää siis DNS-palvelimena suoraan OPNsensea. OPNsense puolestaan käyttää Quad9:ää upstream-palvelimena:

<p align="center">
<img width="1077" height="658" alt="image" src="https://github.com/user-attachments/assets/2e88d0d5-dbd9-407b-a124-8e894afd0ad4" />
  <br>
  <em>Quad9:n testisivu virtuaalikoneella, joka vahvistaa, että DNS-kyselyt välittyvät OPNsensen kautta Quad9-palvelulle</em>
</p>

Tämä siis lähinnä vain kuriositeettina, joka hieman ohittaa jo tehtävänannon, mutta joka on ainakin omasta mielestäni ihan mielenkiintoinen havainto.

## d) Oikeesti TCP/IP

C-tehtävässä esitellystä DNS-kaappauksesta voidaan tunnistaa TCP/IP-mallin kaikki neljä kerrosta. **Sovelluskerroksella** toimii DNS, joka vastaa nimipalvelukyselyistä. **Kuljetuskerroksella** käytetään UDP-protokollaa, mikä näkyy lähde- ja kohdeportteina 47719 ja 53. **Verkkokerroksella** puolestaan näkyvät IPv4-osoitteet 10.0.2.15 ja 192.168.xx.xx, joiden avulla paketti reititetään verkossa. **Peruskerroksella** käytetään Ethernet II -kehystä, joka sisältää lähettäjän ja vastaanottajan MAC-osoitteet (GeeksforGeeks, 2026).

<p align="center">
<img width="652" height="275" alt="image" src="https://github.com/user-attachments/assets/f23392c9-eb25-4be4-a8a8-b00244d8134e" />
  <br>
  <em>Sovelluskerros (Application Layer)</em>
</p>
<br>
<p align="center">
<img width="652" height="201" alt="image" src="https://github.com/user-attachments/assets/61828d12-4e06-4375-aeb7-d6cdb5428037" />
  <br>
  <em>Kuljetuskerros (Transport Layer)</em>
</p>
<br>
<p align="center">
<img width="646" height="255" alt="image" src="https://github.com/user-attachments/assets/aab56dc7-f629-437d-abbd-6ee03e6fb699" />
  <br>
  <em>Verkkokerros (Internet Layer)</em>
</p>
<br>
<p align="center">
<img width="1004" height="151" alt="image" src="https://github.com/user-attachments/assets/c6d89848-e17d-4d40-b8e1-7a5228ec1e0e" />
  <br>
  <em>Peruskerros (Link Layer)</em>
</p>

## e) Mitäs tuli surffattua? 

Tässä tehtävässä avataan surfing-secure.pcap-tiedosto Wiresharkilla ja tutkitaan pintapuolisesti, millainen kaappaus on kyseessä: 

- Paketteja: 283 framea.
- Kaappauksen kokonaiskesto: n. 7,5 sekuntia.
- Protokollat: DNS, TCP, TLSv1.3, QUIC, ARP.
- Sisäverkon laitteet: 192.168.122.7 ja 192.168.122.1.
- Liikenne on suurilta osin salattua (TLS 1.3 ja QUIC), joten sisältöä ei voida lukea suoraan kaappauksesta (Checkpoint.com).

<p align="center">
<img width="1290" height="319" alt="image" src="https://github.com/user-attachments/assets/cc82cc4d-2ca0-4405-bae1-2d4ddd75b2ae" />
  <br>
  <em>Kaappauksen pakettien kokonaismäärä, käytetty aika, osa protokolleista ja toinen sisäverkon laite.</em>
</p>

## g) Minkä merkkinen verkkokortti käyttäjällä on?

Peruskerrosta (Ethernet II) tutkimalla ei MAC-osoitteen perusteella pystytty suoraan varmistamaan verkkokortin valmistajaa, koska osoite on paikallisesti hallittu (locally administered), eikä moni netissä oleva MAC-osoitteiden tunnistuspalvelu tuntunut myöskään tunnistavan sitä. Googlettamalla kyseistä MAC-osoitetta löytyi GitHub-sivu, jossa mainittiin osoitteen etuliitteen (52:54:00) vastaavan QEMU-virtuaalikoneiden käyttämiä virtuaalisia verkkokortteja (ashee 2015). Myös ainakin yksi MAC-osoitteiden tunnistuspalvelu antoi saman tuloksen (ServerSide.com s.a.).

<p align="center">
<img width="1291" height="615" alt="image" src="https://github.com/user-attachments/assets/94e5a70b-ecf2-44d2-954a-b5fa2b1f933a" />
  <br>
  <em>MAC-osoite löytyy, mutta ei muita tunnistetietoja verkkokortista.</em>
</p>

## h) Millä weppipalvelimella käyttäjä on surffaillut?

Kun kaappauksen suodattaa DNS-hakusanalla, saa DNS-kyselyiden perusteella melko hyvän käsityksen siitä, millä sivustoilla käyttäjä on vieraillut:

- www.google.com
- terokarvinen.com
- kommenttero.terokarvinen.com
- gc.zgo.at
- goatcounter.com
- terokarvinen.goatcounter.com

<p align="center">
<img width="1326" height="571" alt="image" src="https://github.com/user-attachments/assets/1648b595-6130-40fc-9d96-7afeaaad18e4" />
  <br>
  <em>DNS-suodatus</em>
</p>

Nopeasti vilkaistuna sana **goat** ja satunnaiset konsonantit sen ympärillä herättivät hieman epäilyksiä, kuten varmasti monilla muillakin 2000-luvun internetissä kasvaneilla. Tarkempi tarkastelu kuitenkin osoitti liikenteen liittyvän avoimen lähdekoodin GoatCounter-kävijäseurantapalveluun. 

Vaikka varsinainen HTTPS-liikenne on TLS-salattua, yhteyksien kohteita voidaan päätellä DNS-kyselyistä sekä TLS Client Hello -paketeissa näkyvästä SNI-tiedosta. Kaappauksesta löytyvä SNI-arvo terokarvinen.com vahvistaa, että käyttäjä on muodostanut HTTPS-yhteyden kyseiseen palvelimeen. Havaintojen perusteella käyttäjä siis on ainakin vieraillut sivustolla terokarvinen.com, ja mahdollisesti käyttänyt Googlea sivulle päätymiseen.

## Lähteet

- Checkpoint.com. s.a. How Does the QUIC Protocol Work? Luettavissa: https://www.checkpoint.com/cyber-hub/network-security/what-is-quic/. Luettu 29.10.2026.
- GeeksforGeeks.org 2026. TCP/IP Model. Luettavissa: https://www.geeksforgeeks.org/computer-networks/tcp-ip-model/. Luettu 28.10.2026.
- Karvinen, T 2025. Network Interface Names on Linux. Luettavissa: https://terokarvinen.com/network-interface-linux/. Luettu 28.10.2026.
- Karvinen, T 2025. Wireshark - Getting Started. Luettavissa: https://terokarvinen.com/wireshark-getting-started/. Luettu 28.10.2026.
- ashee 2015. GitHub: Common mac address prefix/oui. Luettavissa: https://gist.github.com/ashee/9241ab6281e6f4d1ef9b/. Luettu: 29.10.2026.
- ServerSide.com. s.a. MAC Address Lookup. Luettavissa: https://serverside.com/tools/mac-address-lookup?mac=52:54:00. Luettu: 29.10.2026.
