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



## Lähteet

HackTricks, 2024. Wireshark Tricks. Luettavissa: https://book.hacktricks.xyz/generic-methodologies-and-resources/basic-forensic-methodology/pcap-inspection/wireshark-tricks Luettu: 2024-11-10

Karvinen, T. 2024. Find Hidden Web Directories - Fuzz URLs with ffuf. Luettavissa: https://terokarvinen.com/2023/fuzz-urls-find-hidden-directories/ Luettu: 2024-11-10

Karvinen, T. 2024. Tunkeutumistestaus. H3 Nuuskija. Luettavissa: https://terokarvinen.com/tunkeutumistestaus/#h3-nuuskija Luettu: 2024-11-10
