# [H3 Nuuskija](https://terokarvinen.com/tunkeutumistestaus/#h3-nuuskija)(Karvinen 2024)

## x) Lue ja tiivistä.

### Wireshark tricks (HackTricks 2024)

(En pystynyt todentamaan Teron mainitsemaa tietolähteen kirjoittajaa (Popov) linkatusta verkkosivusta.)

- Tiedon analysointiin paljon toimintoja, mm.:
  - Resolved Addresses, valmistaja MAC-osoitteesta
  - Protocol Hierarchy, Conversations, DNS Info, grafiikoita...
- Laajat filteröinti ja hakutoiminnot (haku pakettien sisällöstä)
- Domain ja local hostnimet
- TLS purkaminen
- ADB kommunikointi (android debug bridge)
  - Nappaa APK (android package)

### Find Hidden Web Directories - Fuzz URLs with ffuf (Karvinen 2023)

- ffufilla voi fuzzata tehokkaasti verkkopyyntöjen hakemistopolkuja, pyyntöheadereitä ym.
  - fuzz = puppua/sotkua, eli testataan mitä tapahtuu kun parametriksi asetetaan satunnaisarvoja
- hakemistojen etsiminen: ``ffuf -w <sanalista> -u <url>/FUZZ``
- sanalistat voi laatia itse tai käyttää olemassa olevia
- filteröi ei-halutut OK 200 vastaukset koon mukaan (``-fs``)

## a) Valitse valmis hyökkäys. Ota sellainen hyökkäys, jonka saat toimimaan omaan paikalliseen harjoitusmaaliin, kuten Metasploitableen. Demonstroi hyökkäyksen toiminta.

**Ensiksi:**

Käynnistin Kalin (hyökkäyskone) ja Metasploitable 2:n (kohdekone) VirtualBoxissa. Testasin yhteyden Kalista kohteeseen ``ping 192.168.56.101`` ja ettei Kali saa ulkoverkkoon yhteyttä ``ping 8.8.8.8``. Sain yhteyden ulkoverkkoon, joten "vedin" kaapelin irti VirtualBox Kali Settings -> Network -> NAT Adapter "Cable Connected" -valinta pois. Uusi pingaus Googlen nimipalvelimille ja voi taas jatkaa.

    sudo msfconsole

Yritin etsiä sopivia exploitteja msfconsolessa ja toistaa edellisen viikon vsftpd-haavoittuvuuden hyväksikäyttökaavaa: search, use, info/show options, set, exploit. En ole vielä ainakaan niin harjaantunut Metasploitin käyttäjä, että kykenisin löytämään sopivia hyökkäyksiä kohteeseen ainoastaan lukemalla kuvaukset. Myöskään sokkona hyökkäyksen testaaminen tuntuu järjenvastaiselta. Jos sokkotestauksen automatisoisi, voisi se mahdollisesti olla hyvä tapa.

![image](https://github.com/user-attachments/assets/c21509ac-19f6-4d77-98b2-817a004f5aa4)
> Kuva 1. Ote Kalin ``history``-tulosteesta tutkittuani muutamia mahdollisia hyökkäysvaihtoehtoja.

Päädyin googlaamaan.

Valitsin services-listauksesta Samban. Muistelin, että siitä oli joko tunnilla esitelty esimerkki tai se tuli vastaan ristiinarvioinnin yhteydessä. Silmäilin [tämän](https://medium.com/@niman.ransindu/exploiting-metasploitable-2-using-samba-9262335fd09d) artikkelin. Artikkelin ohjeet mukailivat tuttua "search, use, set, exploit" -kaavaa. Otin exploitin "multi/samba/usermap_script" käyttöön, katsoin infon ja asetin RHOSTS-muuttujaan kohdekoneen ip:n.

![image](https://github.com/user-attachments/assets/6ca0fffd-5ce8-4307-a038-a289dd488d5a)
> Kuva 2. Epäonnistunut explpoitin ajo ja käytetyt optiot.

LHOSTS oli automaattisesti asetettu NAT-adapterin kytkentään, jonka otin pois käytöstä. Eli ``set LHOSTS <host-only adapter ip>`` ja ``exploit``.

Muutosten jälkeen hyökkäys onnistui ja reverse shell on auki.

Laitoin session taustalle (CTRL + Z) ja päivitin session meterpreter-sessioksi ``sessions -u 1``.

![image](https://github.com/user-attachments/assets/1c0fd514-e057-45a2-bdf1-931b93e052e4)
> Kuva 3. Onnistunut hyökkäys ja session päivitys käyttämään meterpreteriä.

## b) Sorsa. Selitä ja arvioi valitsemasi hyökkäyksen toimintaa lähdekoodista.

![image](https://github.com/user-attachments/assets/44a724b2-57e3-48e6-9c58-bcfb737d3aed)
> Kuva 4. Hyötykuorman lähdekoodin etsiminen.

Hyötykuormamoduulin nimi tulostuu ``use <exploit>`` komennon yhteydessä, jos sitä ei ole sitä ennen asetettu.

Exploitin lähdekoodi löytyy ``/usr/share

## Lähteet

HackTricks, 2024. Wireshark Tricks. Luettavissa: https://book.hacktricks.xyz/generic-methodologies-and-resources/basic-forensic-methodology/pcap-inspection/wireshark-tricks Luettu: 2024-11-10

Karvinen, T. 2024. Find Hidden Web Directories - Fuzz URLs with ffuf. Luettavissa: https://terokarvinen.com/2023/fuzz-urls-find-hidden-directories/ Luettu: 2024-11-10

Karvinen, T. 2024. Tunkeutumistestaus. H3 Nuuskija. Luettavissa: https://terokarvinen.com/tunkeutumistestaus/#h3-nuuskija Luettu: 2024-11-10