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

![image](https://github.com/user-attachments/assets/3c19e49b-8275-48ab-b154-c6147cfee720)
> Kuva 6. Keksissä unix-time?

Katseeni kohdistui kuvassa näkyvään hijack-keksiin, jonka loppusosa alkaa 1733. Epäilen kyseessä olevan unix timestamp eli millisekunnit 1970-01-01 (UTC) alkaen (UnixTime.org 2024).

Tähän tyssäsi oma osaamiseni katsoin vinkit, joissa sanottiin hijack_cookien alkuosan olevan juokseva numero. Tämäkään ei auttanut minua eteenpäin. Turvauduin läpikvelyohjeisiin (Blázquez 2023).

Ohjeita luettuani tajusin, että olin missannut ZAPissa responsesta merkittävän osan. Käyttöliittymässä oli oletuksena peittyneenä näkymä, jossa näytettiin palautettu cookie.

![image](https://github.com/user-attachments/assets/60e80bda-cdf8-4a6e-9af3-6267148986be) ![image](https://github.com/user-attachments/assets/943eda3b-ff64-494c-b345-9f567e8eed19)
> Kuvat 7 & 8. Set-Cookie.

Luin lisää Blázquezin (2023) ohjeita ja kokeilin käyttää hänen skriptiään.

En saanut kuvan skriptiä toimimaan. Muokkasin sen alla olevan kuvan mukaiseksi ja sain vastaukseksi `400 Bad Request`.

![image](https://github.com/user-attachments/assets/f1867f16-4085-4d98-808b-5b3c71c46fad)
> Kuva 9. Shell-skripti keksien saamiseksi.

Avasin ZAPissa pyynnön joka sai vastausviestissä Set-Cookie avain-arvoparin ja klikkailin menemään ja manuaalisesti katsoin keksejä läpi yksi kerrallaan kunnes havaitsin poikkeaman juoksevassa osassa hijack-keksiä. Otin ylös keksien arvot.

![image](https://github.com/user-attachments/assets/87182594-091b-4251-8381-1388481cc88c)
> Kuva 10. Nappaamani hijack-keksit.

Sovelsin [tästä](https://www.youtube.com/watch?v=R5YPRhM5GyE) videosta (Security in mind 2023) tavan fuzzata timestampin loppuosa ZAPissa.

![image](https://github.com/user-attachments/assets/8a34ba52-7d0d-4a01-9bc8-7897eac72a5b)
> Kuva 11. Tools -> Fuzz 

Editoin pyyntöön hijack_cookien.

![image](https://github.com/user-attachments/assets/19509066-c1c7-48f0-a128-81e0fdca8b85)
> Kuva 12. Edit message.

Maalasin unix timestampin loppuosan ja painoin Add. Klikkailin valikoista Numberzzzz. Aloitusarvoksi pienempi timestamp ja lopetusarvoksi isompi timestamp. Käytössä oleva keksi on oltava niiden arvojen välissä.

Mutta...

![image](https://github.com/user-attachments/assets/b7fda99b-baa7-4db9-ad73-218422937747)
> Kuva 13. Unexpected 500.

Noin puolet vastauksista olivat 500 internal server error. Vastaavaa virhetilannetta en löytänyt mistään ohjeista. Kyseessä on palvelinpään virhe.

Harmikseni aika on rajallista ja palautukseen on 15h. Sinä aikana pitäisi nukkua ja tehdä muut tämän viikon tehtävät. Nyt epäilen, että Java kannattaisi downgradeta versioon 17...

Tämä tehtävä jäänee kesken.


## Lähteet

Blázquez, A.O. 2023. Session Hijacking in OWASP WebGoat . Luettavissa: https://olleb.com/OWASP-WebGoat-hijack-session/ Luettu: 2024-12-03

Karvinen, T. 2023. Try Web Hacking on New Webgoat 2023.4. Luettavissa: https://terokarvinen.com/2023/webgoat-2023-4-ethical-web-hacking/ Luettu: 2024-12-03

Karvinen, T. 2024. Tunkeutumistestaus. H6 Vuohi. Luettavissa: https://terokarvinen.com/tunkeutumistestaus/#h6-vuohi Luettu: 2024-12-03

Security in mind 2023. WebGoat Session Hijacking Tutorial: An In-Depth Guide. Katsottavissa: https://www.youtube.com/watch?v=R5YPRhM5GyE Katsottu: 2024-12-03

UnixTime.org 2024. What is the unix Timestamp? Luettavissa: https://unixtime.org/ Luettu: 2024-12-03
