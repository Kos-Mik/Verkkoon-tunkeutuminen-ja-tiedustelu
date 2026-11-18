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

Larin varoituksista huolimatta päätin käyttää tässä tehtävässä VirtualBoxia. En lähde syvemmin asennusprosessia avaamaan

Mininet-virtuaalikoneessa ei ole graafista käyttöliittymää, joten laboratorioympäristöä hallitaan erillisestä Kali Linux -virtuaalikoneesta SSH-yhteyden avulla. 
Tätä varten molemmat virtuaalikoneet liitettiin samaan VirtualBox Host-Only -verkkoon ja Mininet-palvelimelle määritettiin IP-osoite 192.168.56.101. Käynnistetään molemmat virtuaalikoneet ja kirjaudutaan näihin sisään.

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

Ongelmia esiintyi Xtermin kanssa, mutta oppimateriaalissa oleva **Mininet ongelmien ratkomista**[sic] -osio korjasi tämän. 

<br>
<p align="center">
<img width="1284" height="525" alt="image" src="https://github.com/user-attachments/assets/5228537d-562a-45b9-a6bb-1e1b5935b8ef" />
  <br>
  <em>Ryu-controller käynnissä, Mininet käynnissä, pingall onnistui, X11-autentikointi korjattu ja xterm h1 avaa ikkunan Kaliin</em>
</p>
