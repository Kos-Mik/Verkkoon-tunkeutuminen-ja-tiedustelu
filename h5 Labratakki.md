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

Larin varoituksista huolimatta päätin käyttää kaikissa tehtävissä VirtualBoxia. Myös tekoälyä (Copilot, heh) on käytetty näissä tehtävissä tarkentamaan mitä mikin vaihe teoriassa tekee ja pääsemään yli vaikeimpien osioiden, koska tällä kertaa ollaan jo hieman syvemmissä vesissä.

Mininet-virtuaalikoneessa ei ole graafista käyttöliittymää, joten laboratorioympäristöä hallittiin erillisestä Kali Linux -virtuaalikoneesta SSH-yhteyden avulla. Tätä varten molemmat virtuaalikoneet liitettiin samaan VirtualBoxin Host-Only-verkkoon ja Mininet-palvelimelle määritettiin IP-osoite 192.168.56.101.

Yhteys muodostettiin komennolla ```ssh -X mininet@192.168.56.101```.

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

## b)  ICMP Spoofing ja TCP Session Hijacking

Tällä kertaa ei ollu niin selkää käsistystä mistä aloittaisin, joten oli pakko etsiä hakemistosta README.md-tiedosto:

<details>
<summary>Näytä README.md</summary>

## ** Note **
Ask teacher! Few of bugs are fixed only to local version.

## **Introduction**

This project is a hands-on exploration of network security attacks, illustrating the potential weaknesses in communication protocols and the methods attackers use to exploit these vulnerabilities. By providing scripts and instructions for each task, this repository serves as a learning resource for anyone interested in network security, ethical hacking, and cybersecurity.

## **Project Overview**

Each task in this repository simulates a different type of network attack. These exercises are conducted in a controlled environment using Mininet, a network emulator, and Python scripts. The primary objective is to analyze each attack and understand the techniques used, which can be beneficial in designing effective defense strategies in real-world scenarios.

### **Tasks Overview**

* **ICMP Spoofing Attack**: Simulates packet interception by spoofing ICMP echo requests and replies.  
* **MITM Attack Using ARP Cache Poisoning**: Demonstrates interception of communication by manipulating the ARP cache.  
* **TCP Session Hijacking Attack**: Explores session hijacking by injecting spoofed packets into an active TCP session.

---

## **Prerequisites**

Before you begin, ensure you have the following tools and libraries installed:

* **Python 3.x**: Python programming language.

**Scapy library**: For packet manipulation and crafting.  
```pip install scapy```

* **Mininet**: Network emulator for creating virtual networks.  
* **Virtual Machine (VM) environment** (recommended): To safely test network attacks without affecting live networks.

---

## **Installation**

**Clone this repository** to your local machine:  
```git clone https://github.com/yourusername/network-security-lab.git```  
```cd network-security-lab```

1. **Install dependencies**:

Install Scapy (if not already installed): ```pip install scapy```

2. **Set up Mininet**:  
   * Follow the official Mininet installation guide if you haven't installed it already.

---

## **Tasks**

### **Task 1: ICMP Spoofing Attack**

In this task, you'll learn how to capture and spoof ICMP packets (e.g., ping requests and replies) between two hosts within a Mininet network. This demonstrates how attackers can intercept and alter network communications.

**Files**:

* ```sniff_icmp.py```: Script to capture ICMP packets.  
* ```spoof_icmp.py```: Script to spoof ICMP echo replies.

### **Task 2: MITM Attack Using ARP Cache Poisoning**

This task demonstrates a **Man-in-the-Middle** attack by using ARP cache poisoning. By sending malicious ARP responses, an attacker can intercept and alter communications between two hosts.

**Files**:

* ```udp_server.py```: UDP server script to simulate normal traffic.  
* ```udp_client.py```: UDP client script to simulate normal traffic.  
* ```arp_poison.py```: Script to perform ARP cache poisoning.

### **Task 3: TCP Session Hijacking Attack**

This task explores TCP session hijacking, where an attacker takes over an active session between two hosts by injecting spoofed packets.

**Files**:

* ```tcp_server.py```: TCP server script to simulate a session.  
* ```tcp_client.py```: TCP client script to simulate a session.  
* ```sniff_tcp_session.py```: Script to capture TCP session details.  
* ```tcp_hijack.py```: Script to perform TCP session hijacking by injecting spoofed packets.

---

## **Usage**

Each task is designed to be run in a Mininet environment. Below are the steps to execute each task:

**Set up the Mininet environment**:  
```sudo python hub_topo.py```

1. **Run the relevant scripts** on different Mininet hosts to perform each task.

**Task 1: ICMP Spoofing**  
`# On Host 1`  
```python sniff_icmp.py```

`# On Host 2`  
```ping 10.0.0.1```

**Task 2: ARP Cache Poisoning (MITM)**  
`# On Host 1`  
```python udp_server.py```

`# On Host 2`  
```python udp_client.py```

`# On Host 3`  
```python arp_poison.py```

**Task 3: TCP Session Hijacking**  
`# On Host 1`  
```python tcp_server.py```

`# On Host 2`  
```python tcp_client.py```

`# On Host 3`  
```python sniff_tcp_session.py```  
```python tcp_hijack.py```

2. **Observe the output**: Each script will display output on the console, showing captured packets, spoofed replies, and other details based on the task.

**Note**: Execute these commands within the Mininet environment to simulate real network conditions.

---

## **Contributing**

Contributions to this repository are welcome. All contributions should follow best practices and include detailed comments and documentation.

---

## **License**

This project is licensed under the MIT License. For more information, please refer to the LICENSE file.

</details>

Selkis! Aloitetaan siis **ICMP Spoofing** -labrasta. Kuten README:ssa opastetaaan, mennään **scripts**-hakemistoon ```cd ~/lab/01-Network-Security-Lab/scripts``` ja käynnistetään topologia ```sudo python hub_topo.py```. Tämän jälkeen avataan terminaalit **h1**, **h2** ja **h3**.

- **h1** terminaalissa käynnistetään kuuntelu ```python sniff_icmp.py```.
- **h2**-terminaalissa lähetetään pingi ```ping 10.0.0.1```.
- **h3**-terminaalissa käynnistetään väärentäjä ```python spoof_icmp.py``` ja laitetaan myös Wireshark tutkimaan liikennettä varmuuden vuoksi ```wireshark &```:

<p align="center">
<img width="968" height="580" alt="image" src="https://github.com/user-attachments/assets/4e5f178c-fa99-46d8-95d0-6aa9484895e3" />
<img width="956" height="737" alt="image" src="https://github.com/user-attachments/assets/d9128526-13a1-4914-97fb-b6619239c582" />
  <br>
  <em>Pingaus, spooffaus ja kaappaus</em>
</p>

Kesti jonkin aikaa oivaltaa, mitä spoofausohjelman käynnistämisen jälkeen oikeastaan tapahtui. Lopulta sekä h1-terminaalin kuunteluohjelmassa että Wireshark-kaappauksessa havaittiin hyökkäyksen käynnistymisen jälkeen ylimääräisiä ICMP Echo Reply -paketteja, joiden tunnisteet myös poikkesivat tavallisista vastauksista (**id=0x0000, seq=0/0**). Näitä paketteja ei esiintynyt ennen spoofausohjelman käynnistämistä. Tämä osoittaa, että hyökkääjän **spoof_icmp.py**-skripti lähetti verkkoon väärennettyjä ICMP-vastauksia oikean palvelimen lähettämien vastausten rinnalle.

Ping-liikenne jatkui kuitenkin näennäisesti normaalina, minkä vuoksi hyökkäystä ei ollut helppo havaita pelkästään käyttäjän näkökulmasta. Kuten aiemmin mainitsin, itseltänikin kesti hetki ymmärtää, mikä kaappauksissa oli muuttunut. Harjoitus havainnollisti mielestäni hyvin, kuinka hyökkääjä voi tuottaa uskottavan näköisiä ICMP-vastauksia ilman, että ICMP-protokolla kykenee varmistamaan niiden todellista alkuperää. Samalla tuli hyvinkin selväksi, kuinka helposti myös ihminen voi tulkita väärennetyn liikenteen normaaliksi liikenteeksi, jos poikkeavuuksia ei osaa etsiä.

<br>

Seuraavaksi tehdään **TCP Session Hijacking** -labra. Pysytään samassa hakemistossa kuin **ICMP Spoofing** -labrassakin ja pidetään sama myös topologia käynnissä. Avataan vain "puhtaat" **h1**, **h2** ja **h3** -terminaalit.

- **h1**-terminaalissa ajetaan ```python tcp_server.py```, joka toimii TCP-palvelimena.
- **h2**-terminaalissa ajetaan ```python tcp_client.py```, joka muodostaa yhteyden palvelimeen.
- **h3**-terminaalissa käynnistetään ```python sniff_tcp_session.py```, joka kuuntelee verkossa kulkevaa TCP-liikennettä ja kerää tietoa aktiivisesta TCP-istunnosta.
- Avataan vielä kaksi muuta h3-terminaalia, koska aiemmassa h3-terminaalissa skripti jää kuuntelemaan liikennettä ja se pitää jättää käyntiin. Käynnistetään toisessa jälleen Wireshark ja ajetaan toisessa ```python tcp_hijack.py```, joka yrittää syöttää väärennettyjä paketteja aktiiviseen TCP-istuntoon.

<p align="center">
<img width="968" height="704" alt="image" src="https://github.com/user-attachments/assets/9b1721ba-7614-4d59-8e61-e19138841834" />
<img width="995" height="311" alt="image" src="https://github.com/user-attachments/assets/c23ef34b-7b44-495c-bfd5-a43f6e877f8e" />
  <br>
  <em></em>
</p>

Tämä harjoitus oli huomaamattomampi kuin ARP-myrkytys, sillä mitään yhtä selvää onnistumisen merkkiä ei tullut vastaan. Normaali yhteys h1:n ja h2:n välillä muodostui ilman ongelmia, minkä jälkeen h3 alkoi kuunnella yhteyttä ja kerätä siitä tietoja hyökkäystä varten.

Itse hyökkäys näkyi lähinnä siinä, että tcp_hijack.py lähetti väärennetyn TCP-paketin esiintyen h2:na. Ilman Wiresharkia olisi ollut melko vaikea sanoa mitä oikeastaan tapahtui, sillä terminaaleissa ei näkynyt mitään yhtä ilmeistä kuin esimerkiksi ARP-myrkytyksen yhteydessä.

Mielenkiintoisin havainto löytyi lopulta Wireshark-kaappauksesta, jossa näkyi hyökkäyksen jälkeen TCP Reset (RST, ACK) -paketti. Tämän perusteella hyökkääjän lähettämä paketti vaikutti aktiiviseen yhteyteen ja aiheutti häiriön sen toiminnassa. Harjoituksen perusteella TCP-istunnon kaappaaminen vaikutti myös selvästi monimutkaisemmalta kuin ICMP-spoofaus, koska ennen varsinaista hyökkäystä täytyi ensin kerätä tietoa olemassa olevasta yhteydestä. Hyökkäys siis onnistui vaikuttamaan yhteyteen, mutta sen vaikutukset olivat huomattavasti vaikeammin havaittavissa kuin aiemmissa harjoituksissa.

## c) TCP SYN-Flood

Tällä kertaa ajetaan TCP SYN-Flood -hyökkäyssimulaatio. Simulaatio käynnistettiin projektin README-tiedoston ohjeiden mukaisesti. Ensin avattiin projektin hakemisto **02-SDN_DDoS_Simulation-tryout** ja käynnistettiin topologia komennolla ```sudo python tree_topology.py```. Tämän jälkeen käynnistettiin SDN-ohjain toisessa terminaalissa komennolla ```ryu-manager main.py```. 

<p align="center">
<img width="679" height="532" alt="image" src="https://github.com/user-attachments/assets/a1db80ca-e49e-4635-84eb-41091f6c4982" />
  <br>
  <em></em>
</p>

Topologia käynnistyi onnistuneesti, ja simulaation tulosteiden perusteella yksi hosteista määritettiin hyökkääjäksi samalla kun toinen hosti lähetti normaalia liikennettä. SDN-ohjain kuitenkin kaatui TensorFlow-kirjaston yhteensopivuusongelmaan **(tf.reset_default_graph())**, minkä vuoksi simulaatio ei edennyt täyteen toimintaan. Tässä kohtaa oli pakko jälleen ottaa tekoäly hyötykäyttöön, että mikä meni pieleen: simulaatiossa havaittiin useita yhteensopivuusongelmia, koska projekti oli toteutettu **TensorFlow 1.x** -versiolle, kun laboratorioympäristössä oli käytössä **TensorFlow 2.13.1**. Ongelma ratkaistiin ottamalla käyttöön TensorFlown yhteensopivuuskerros **(tensorflow.compat.v1)** sekä päivittämällä vanhentuneita TensorFlow-kutsuja. 

<p align="center">
<img width="916" height="635" alt="image" src="https://github.com/user-attachments/assets/1370ca6b-8a31-4345-880b-ffe0ddf32711" />
<img width="916" height="635" alt="image" src="https://github.com/user-attachments/assets/24a0a3c3-18aa-4094-ae5c-a0042cafe05f" />

<img width="853" height="637" alt="image" src="https://github.com/user-attachments/assets/e40c014c-5394-442d-8863-4b864e263c84" />
<img width="909" height="634" alt="image" src="https://github.com/user-attachments/assets/167056ae-9941-4cc4-b229-e40c3ae83c37" />

  <br>
  <em>Ennen ja jälkeen. Huom! Tässä on vain pari esimerkkiä. Korjattavia tiedostoja oli enemmänkin.</em>
</p>

Näiden muutosten jälkeen simulaatio käynnistyi onnistuneesti ja SDN-ohjain alkoi vastaanottaa verkkoliikenteen tietoja. Simulaation aikana Mininet-virtuaalikone alkoi antaa soft lockup -varoituksia useista prosessoriytimistä. Samalla myös oman koneeni tuuletin lähti huutamaan melkoisesti, joten nämä kyllä kertovat ympäristön kuormittuneen huomattavasti simulaation aikana. Tämä sopii hyvin yhteen SYN Flood -hyökkäyksen toimintaperiaatteen kanssa, jossa tavoitteena on kuluttaa kohteen resursseja mahdollisimman paljon.

<p align="center">
  <img width="801" height="681" alt="image" src="https://github.com/user-attachments/assets/206495b9-f833-4607-a859-9ea1c4b73daa" />
  <img width="1383" height="533" alt="image" src="https://github.com/user-attachments/assets/21e37c89-2a80-4a34-862b-8f36b9c6aa21" />
  <br>
  <em></em>
</p>

SDN-ohjain vastaanotti jatkuvasti OpenFlow-tilastotietoja, mikä näkyi kasvavina packet_count-arvoina. Tästä huolimatta luokittelijan tuloste pysyi koko testin ajan arvoissa **Attack: 0** ja **Benign: 0**. Liikenteen seuranta vaikutti siis toimivan, mutta hyökkäysliikenteen tunnistamiseen tarkoitettu osa ei näyttänyt tuottavan tunnistustuloksia. Yhtenä mahdollisena syynä tähän voivat olla itse korjatut TensorFlow-yhteensopivuusongelmat tai projektin muu bugi.


Näin siis TCP SYN Flood -hyökkäyksen pitäisi teoriassa toimia:

TCP SYN Flood -hyökkäys perustuu TCP-yhteyden muodostamiseen käytettävään kolmivaiheiseen kättelyyn. Normaalisti asiakas lähettää ensin **SYN**-paketin, johon palvelin vastaa **SYN-ACK**-paketilla. Tämän jälkeen asiakas vielä vahvistaa yhteyden **ACK**-paketilla, jolloin yhteys muodostuu onnistuneesti.

TCP SYN Flood -hyökkäyksessä tarkoituksena on kuitenkin lähettää suuri määrä **SYN**-paketteja ilman, että yhteyksiä koskaan viimeistellään. Palvelin käsittelee jokaisen pyynnön normaalisti ja varaa niille resursseja odottaessaan viimeistä **ACK**-pakettia. Kun uusia yhteyspyyntöjä tulee jatkuvasti lisää eikä vanhoja saada päätökseen, palvelimelle alkaa kertyä suuri määrä keskeneräisiä yhteyksiä (Cloudflare, s.a).

Nyt tehdyssä simulaatiossa hyökkäävä hosti vaihtui automaattisesti episodien välillä. Tulosteiden perusteella esimerkiksi **host4**, **host6** ja **host3** toimivat vuorollaan hyökkääjinä samalla kun jokin toinen hosti lähetti normaalia liikennettä. Tarkoituksena oli mallintaa tilannetta, jossa osa verkon liikenteestä on normaalia ja osa hyökkäysliikennettä.

Hyökkäyksen tavoitteena ei ole päästä järjestelmään sisälle tai varastaa tietoa, vaan kuluttaa kohdejärjestelmän resursseja niin paljon, että normaalien käyttäjien yhteydet hidastuvat tai estyvät kokonaan. Cloudflaren kuvauksen perusteella hyökkäystä voidaan tehostaa käyttämällä väärennettyjä lähdeosoitteita, jolloin palvelin jää odottamaan vastauksia yhteyksiltä, joita ei todellisuudessa ole olemassa. Tämä kasvattaa keskeneräisten yhteyksien määrää entisestään ja lisää palvelimen kuormitusta.

## Lähteet

- Cloudflare s.a. SYN flood attack. Luettavissa: https://www.cloudflare.com/learning/ddos/syn-flood-ddos-attack/. Luettu: 20.11.2026.
- Microsoft Copilot

