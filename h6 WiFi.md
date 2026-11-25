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

**What is the content of the file /root/flag.txt on the VM?**

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

<img width="738" height="111" alt="image" src="https://github.com/user-attachments/assets/a8b0da61-8cbd-4d99-abd6-7deb36c1486f" />

</details>

Seuraavaksi voidaan jatkaa **Recon**-osion tehtäviä
