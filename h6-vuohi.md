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

### b) (A1) Broken Access Control (WebGoat 2023.4)

#### Hijack a session

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

Avasin ZAPissa pyynnön joka sai vastausviestissä Set-Cookie avain-arvoparin ja lähetin uudelleen useamman pyynnön ja manuaalisesti katsoin keksejä läpi yksi kerrallaan kunnes havaitsin poikkeaman juoksevassa osassa hijack-keksiä. Otin ylös keksien arvot.

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

#### Insecure Direct Object References

TODO

#### Missing Function Level Access Control (ei kohtaa 4)

**Relying on obscurity**

Tehtävänannosta WebGoatissa päättelin, että tämä selvitetään selaimen dev toolseilla (F12 Firefoxissa). Klikkailin auki Inspectorissa kaikki elementit auki mutta en löytänyt vastausta.

Tämän jälkeen right-klikkasin lomaketta, jossa haavoittuvuus on (ks. kuva 14).

![image](https://github.com/user-attachments/assets/821c8026-8f2a-4e5c-8d90-f1f9b571ebf0)
> Kuva 14. Inspect ohjaa klikattuun elementtiin DOMissa.

![image](https://github.com/user-attachments/assets/daee9a6c-f466-47f9-ab2a-7cfae5dc64a4)
> Kuva 15. Piilo ei ollutkaan hyvä.

Lähetin lomakkeessa "Users" ja "Config". Tehtävä suoritettu!

**Gathering User Info**

Seuraavaksi tarkoituksena on saada käyttäjän Jerry tiiviste esille hyödyntäen aiemman osan tietoja. Edellisessä osiossa piilotettu valinta Users ohjaisi endpointiin `/access-control/users`. Lisäsin sen urliin.

![image](https://github.com/user-attachments/assets/41838596-85e4-424c-a785-a6f1f94a3b82)
> Kuva 16. Jotain meni taas rikki.

Kuvassa 16 on http-vastaus 500 palvelimelta. Vastausdatan lopussa sanotaan, että resurssi ei ole olemassa.

Onko tämä tehtävänannossa mainittua "leaked data"?

Tähän taas omat taidot tyssäsi. Avasin vihjeet ja klikkailin loppuun. Muokkasin ZAPissa pyyntöä, jolla sain kuvan 16 vastauksen. Lisäsin pyyntöön `Content-Type: application/json`.

![image](https://github.com/user-attachments/assets/e4e03e05-d388-432c-8f20-844c152e1460)
> Kuva 17. Käyttäjätiedot selvillä.

Lisäsin Jerryn tiivisteen WebGoatin lomakkeelle ja tehtävä on (epätyydyttävästi) suoritettu.

### c) (A7) Identity & Auth Failure (WebGoat 2023.4)

#### Authentication Bypasses

Tarkoituksena kiertää 2FA turvakysymykset. Ohjeiden esimerkissä oli keissi, jossa turvakysymysparametrit poistettiin pyynnöstä, jolloin autentikointi kierrettiin.

Kokeilin sitä tietenkin itse ensimmäisenä, ei ollut näin helppoa. Poistelin pyynnöstä parametri kerrallaan ja sain saman vastauksen epäonnistuneesta yrityksestä. Kokeilin myös nimetä parametrit uusiksi secQuestion0 -> secAnswer0 ja secQuestion1 -> secQuestion2, vaihdoin argumentin ja arvon paikkoja sekä vastaavia yhdistelmiä ja tyhjiä arvoja.

Ei auennut. Klikkailin läpi vinkit ja totisesti olin oikeilla jäljillä. Hieman turhautuneena kokeilin uudestaan nimetä secQuestion0 -> secQuestion2 ja secQuestion1 -> secQuestion3.

Oho, nyt se toimi...

![image](https://github.com/user-attachments/assets/4cdf8ba7-a8be-4baf-bcd0-1fe3763bd24d)
> Kuva 18. Autentikointi kierretty.

Olin aivan varma, että olin kokeillut tätä. Selasin ZAPissa tekemäni pyynnöt (filter POST) näppärästi nuolinäppäimellä alas alkaen ensimmäisestä pyynnöstä ja huomasin virheeni.

![image](https://github.com/user-attachments/assets/87d8e47d-4cdb-46ed-82b9-aa7d5e2b4ebe)
> Kuva 19. Huolimattomuusvirhe.

#### Insecure Login

## Lähteet

Blázquez, A.O. 2023. Session Hijacking in OWASP WebGoat . Luettavissa: https://olleb.com/OWASP-WebGoat-hijack-session/ Luettu: 2024-12-03

Karvinen, T. 2023. Try Web Hacking on New Webgoat 2023.4. Luettavissa: https://terokarvinen.com/2023/webgoat-2023-4-ethical-web-hacking/ Luettu: 2024-12-03

Karvinen, T. 2024. Tunkeutumistestaus. H6 Vuohi. Luettavissa: https://terokarvinen.com/tunkeutumistestaus/#h6-vuohi Luettu: 2024-12-03

Security in mind 2023. WebGoat Session Hijacking Tutorial: An In-Depth Guide. Katsottavissa: https://www.youtube.com/watch?v=R5YPRhM5GyE Katsottu: 2024-12-03

UnixTime.org 2024. What is the unix Timestamp? Luettavissa: https://unixtime.org/ Luettu: 2024-12-03
