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

Mininet-virtuaalikoneessa ei ole graafista käyttöliittymää, joten laboratorioympäristöä hallittaan erillisestä Kali Linux -virtuaalikoneesta SSH-yhteyden avulla. 
Tätä varten molemmat virtuaalikoneet liitettiin samaan VirtualBox Host-Only -verkkoon ja Mininet-palvelimelle määritettiin IP-osoite 192.168.56.101.

Yhteys muodostettiin komennolla:

```bash
ssh -X mininet@192.168.56.101
```

X11-välitystä käytettiin, jotta Mininetin xterm-ikkunat voitiin avata Kalin työpöydälle laboratorioharjoitusten suorittamista varten.
