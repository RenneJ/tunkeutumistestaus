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

## Lähteet

Karvinen, T. 2023. Try Web Hacking on New Webgoat 2023.4. Luettavissa: https://terokarvinen.com/2023/webgoat-2023-4-ethical-web-hacking/ Luettu: 2024-12-03

Karvinen, T. 2024. Tunkeutumistestaus. H6 Vuohi. Luettavissa: https://terokarvinen.com/tunkeutumistestaus/#h6-vuohi Luettu: 2024-12-03
