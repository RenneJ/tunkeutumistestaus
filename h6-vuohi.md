# [H6 Vuohi](https://terokarvinen.com/tunkeutumistestaus/#h6-vuohi) (Karvinen 2024)

## x) Lue/katso ja tiivistä.

**Try Web Hacking on New Webgoat 2023.4** (Karvinen 2023)

- asenna java 17 jdk ja jre (development kit ja environment)
- asenna palomuuri (ufw)
- viimeisin julkaisu githubista (.jar tiedosto)
- huomioi webgoatin portti on todennäköisesti sama kuin proxyn

![image](https://github.com/user-attachments/assets/c6e4b133-8ae2-4d41-ae46-1fb8668a7023)
> Kuva 1. Komento webgoatin portin vaihtamiseksi ja käynnistämiseen. Lähde: https://terokarvinen.com/2023/webgoat-2023-4-ethical-web-hacking/

## a) Asenna Webgoat 2023.4.

Target: Kali Rolling VM (VirtualBox 7.0). Host: Ubuntu 22.04.

    $ sudo apt update
    $ sudo apt install ufw
    $ sudo ufw enable

Java onkin asennettu aiemmin (muistaakseni ZAPia varten). Tarkistin kuitenkin version. Ymmärtääkseni uudemman javan pitäisi pystyä ajamaan vanhempaa javaa.

![image](https://github.com/user-attachments/assets/8e688a5b-c4ca-4657-abb2-c99108c1bedc)
> Kuva 2. Java-versio 23.

    $ java -Dfile.encoding=UTF-8 -Dwebgoat.port=8888 -Dwebwolf.port=9090 -jar webgoat-2023.4.jar

Suoritettuani yllä olevan komennon avasin verkkoselaimessa `127.0.0.1:8888`, jolloin sain tyhjän sivun. Lisäsin urliin `/WebGoat` ja sain verkkomurtautumisharjoituspalvelimen auki.

![image](https://github.com/user-attachments/assets/3125f985-c7c2-462a-9771-286da73b9eb0)
> Kuva 3. Vuohi hereillä.

## Ratkaise WebGoat 2023.4

Luodessani uutta käyttäjää WebGoat ystävällisesti kehoitti ottamaan yhteyden ulkoverkkoon pois päältä.

![image](https://github.com/user-attachments/assets/3dc41c3a-de84-4dc2-8887-2dad672cbf6a)
> Kuva 4. No internet.

Testataan nyt, että ZAP nappaa liikenteen WebGoat palvelimen ja asiakkaan välillä. Lisäsin FoxyProxyyn uuden wildcard include säännön (pattern) `*WebGoat*` ja **Proxy By Patterns** asetuksen päälle.

ZAP toimii oletetusti.

![image](https://github.com/user-attachments/assets/f451e4f2-3588-48f2-90a8-b302b4654e83)
> Kuva 5. ZAPping.

### b) (A1) Broken Access Control

Avattuani tämän haasteen ja luettuani tehtävänannon, nappaa toisen käyttäjän sessio, avasin selaimen dev toolsin ja katsoin storage-välilehdeltä omat keksini.

![h6bhijack01](https://github.com/user-attachments/assets/f43d1f63-d388-4bc0-9add-9443c1610d8f)
> Kuva 6. Keksissä unix-time?

Katseeni kohdistui kuvassa näkyvään hijack-keksiin, jonka loppusosa alkaa 1733. Epäilen kyseessä olevan unix timestamp eli millisekunnit 1970-01-01 (UTC) alkaen (UnixTime.org 2024).

Huom! Vaikka tehtävässä on kyseessä harjoituspalvelin, enkä ole yhteydessä ulkoverkkoon Kali-koneellani, pidän silti tärkeänä periaatetta, että en julkaise sessiotunnisteita. Kannattaa harjoitellessaankin toimia hyvien käytänteiden mukaan. Siksi olen kuvasta piilottanut keksit.



## Lähteet

Karvinen, T. 2023. Try Web Hacking on New Webgoat 2023.4. Luettavissa: https://terokarvinen.com/2023/webgoat-2023-4-ethical-web-hacking/ Luettu: 2024-12-03

Karvinen, T. 2024. Tunkeutumistestaus. H6 Vuohi. Luettavissa: https://terokarvinen.com/tunkeutumistestaus/#h6-vuohi Luettu: 2024-12-03

UnixTime.org 2024. What is the unix Timestamp? Luettavissa: https://unixtime.org/ Luettu: 2024-12-03
