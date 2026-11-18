# h4 Labratakki

### Ympäristö

- **Isäntäkäyttöjärjestelmä:** Windows 11
- **Virtualisointialusta:** Oracle VirtualBox 7.2.16
- **Hyökkäys- ja analyysityöasema:** Kali Linux 2026.3
- **Verkon emulointialusta:** Mininet 2.3.0
- **Mininet-palvelimen käyttöjärjestelmä:** Ubuntu Server 20.04.6 LTS (Linux 5.4.0-208-generic, x86_64)
- **Selain:** Firefox 153.4.0esr (64-bit)
- **Etähallinta:** OpenSSH (X11 Forwarding)

## a) ARP hyökkäys

Larin varoituksista huolimatta päätin käyttää tässä tehtävässä VirtualBoxia.

Mininet-virtuaalikoneessa ei ole graafista käyttöliittymää, joten laboratorioympäristöä hallittiin erillisestä Kali Linux -virtuaalikoneesta SSH-yhteyden avulla. Tätä varten molemmat virtuaalikoneet liitettiin samaan VirtualBoxin Host-Only-verkkoon ja Mininet-palvelimelle määritettiin IP-osoite 192.168.56.101.

Yhteys muodostettiin komennolla:

```bash
ssh -X mininet@192.168.56.101
```

<p align="center">
<img width="657" height="499" alt="image" src="https://github.com/user-attachments/assets/b4688475-88c3-4cf1-8c21-056d8375397e" />
  <br>
  <em>Virtuaalikoneet keskustelevat keskenään ja SSH-yhdistäminen onnistui Kalista Mininetiin.</em>
</p>

X11-välitystä käytettiin, jotta Mininetin xterm-ikkunat voitiin avata Kalin työpöydälle laboratorioharjoitusten suorittamista varten.

Seuraavaksi avataan uusi terminaali, yhdistetään samalla tavalla SSH:n avulla Mininetiin ja tehtävänannon mukaisesti käynnistetään verkko-ohjain komennolla ```ryu-manager ryu.app.simple_switch_13``` ja tämän jälkeen toisella terminaalilla käynnistetään Mininet komennolla  ```sudo mn --topo single,3 --mac --switch ovsk --controller remote```. Testataan vielä ```pingall```-komennolla, että yhteystoimii:

Ongelmia esiintyi Xtermin kanssa, mutta oppimateriaalissa oleva **Mininet ongelmien ratkomista** [sic] -osio korjasi tämän. 

<br>
<p align="center">
<img width="1284" height="525" alt="image" src="https://github.com/user-attachments/assets/5228537d-562a-45b9-a6bb-1e1b5935b8ef" />
  <br>
  <em>Ryu-controller käynnissä, Mininet käynnissä, pingall onnistui, X11-autentikointi korjattu ja xterm h1 avaa ikkunan Kaliin</em>
</p>

Tarkistin seuraavaksi **h1**-hostin ARP-taulun komennolla ```arp -n```, mutta taulu oli tyhjä. Lähetin tämän vuoksi testipaketin komennolla ```ping -c 1 h2```, jolloin h1 suoritti ARP-resoluution ja tallensi **h2**:n MAC-osoitteen välimuistiin. Samalla varmistui, että yhteydet toimivat odotetulla tavalla ennen hyökkäyksen aloittamista.

<br>
<p align="center">
<img width="486" height="350" alt="image" src="https://github.com/user-attachments/assets/a9aa67e4-e6e7-4f09-9e11-92a290e478e1" />
  <br>
  <em>Nyt näyttää jo paremmalta.</em>
</p>

Nyt avataan **h3**-terminaali ja otetaan siellä **IP-forwardointi** käyttöön, lisätään **NAT**-sääntö SSH-liikenteelle, käynnistetään kaappaus **tcpdump**illa taustalle pyörimään ja lopuksi käynnistetään vale-SSH-palvelin:

```bash
sysctl -w net.ipv4.ip_forward=1
```
```bash
iptables -t nat -A PREROUTING -p tcp --dport 22 -j REDIRECT --to-port 22
```
```bash
tcpdump -i h3-eth0 -w arp-p.pcap &
```
```bash
sshesame --port 22 --listen_address 0.0.0.0
```

Avataan tämän jälkeen uusi h3-terminaali ja ajetaan varsinainen myrkytys ```python3 ./arp_poison.py``` -komennolla. Sen jälkeen yritetään ottaa yhteys h1-terminaalilla h2-hostiin:

<p align="center">
<img width="480" height="370" alt="image" src="https://github.com/user-attachments/assets/9cee1927-c7f0-4bfa-95f5-ef696340cdde" />
  <br>
  <em>ARP-myrkytys onnistui, mutta SSH havaitsi tilanteen epäilyttäväksi.</em>
</p>

Tässä kohtaa SSH havaitsi, että kohdepalvelimen julkinen avain oli muuttunut ja antoi varoituksen mahdollisesta man-in-the-middle-hyökkäyksestä. Tämä johtui siitä, että ARP-myrkytyksen seurauksena liikenne ei enää kulkenut suoraan h2:lle, vaan hyökkääjä h3:n kautta. SSH:n host key -tarkistus siis esti yhteyden muodostamisen väärälle palvelimelle. 

Poistetaan vanha avain komennolla ```ssh-keygen –R h2``` jonka jälkeen ajetaan uudelleen ```ssh h2``` -komento:

<p align="center">
<img width="479" height="161" alt="image" src="https://github.com/user-attachments/assets/60d363e5-639d-4469-a127-a90acb98758d" />
  <br>
  <em>I'm in!</em>
</p>

Tällä kertaa, kun aiempi avain poistettiin known_hosts-tiedostosta ja uusi avain hyväksyttiin, SSH-yhteys muodostettiin hyökkääjän ylläpitämään valepalvelimeen. Tämä osoittaa, että ARP-myrkytys mahdollisti SSH-liikenteen ohjaamisen hyökkääjälle, mutta SSH:n host key -tarkistus olisi voinut estää hyökkäyksen onnistumisen, mikäli käyttäjä olisi huomioinut varoituksen eikä hyväksynyt uutta avainta.

Tutkitaan vielä lokia:

<p align="center">
<img width="597" height="303" alt="image" src="https://github.com/user-attachments/assets/c5792aa4-32fc-4ee7-9fb2-7e1c1d0c0150" />
  <br>
  <em>Lokimerkinnät</em>
</p>


Lokitiedoista näkee suoraan, että hyökkääjä pystyi vastaanottamaan käyttäjätunnuksen (root) sekä salasanan (salasana). Tämä osoittaa, että ARP-myrkytys mahdollisti kirjautumistietojen kaappaamisen, jos käyttäjä jättää huomiotta SSH:n antamat turvallisuusvaroitukset.

<p align="center">
<img width="953" height="767" alt="image" src="https://github.com/user-attachments/assets/8a80c101-90a9-44a2-b72e-217adf8cf539" />
  <br>
  <em>Osittainen Wireshark-kaappaus</em>
</p>

Wireshark-kaappauksessa havaittiin ensin ARP-myrkytyksen aiheuttamia väärennettyjä ARP-vastauksia, joissa hyökkääjän MAC-osoite (00:00:00:00:00:03) yhdistettiin IP-osoitteisiin 10.0.0.1 ja 10.0.0.2. Tämän jälkeen kaappauksessa näkyi SSH-yhteyden muodostuminen porttiin 22. SSH-protokollan tunnistetiedoista ilmeni, että palvelin vastasi tunnisteella "SSH-2.0-sshesame", mikä osoittaa yhteyden ohjautuneen hyökkääjän ylläpitämään valepalvelimeen eikä varsinaiselle kohdepalvelimelle h2. Tämä vahvistaa ARP-myrkytyksen onnistuneen.

Yhteenvetona: tässä harjoituksessa onnistuttiin ohjaamaan h2:lle tarkoitettu SSH-liikenne hyökkääjäkoneelle ARP-myrkytyksen avulla. SSH:n avaintarkistus kuitenkin havaitsi muutoksen välittömästi ja varoitti käyttäjää mahdollisesta man-in-the-middle-hyökkäyksestä. Vasta käyttäjän hyväksyttyä uuden avaimen hyökkääjä pystyi vastaanottamaan käyttäjätunnuksen ja salasanan. Harjoitus havainnollistaa hyvin sitä, kuinka tehokas suoja SSH:n host key -tarkistus on silloin, kun käyttäjä kiinnittää siihen huomiota.
