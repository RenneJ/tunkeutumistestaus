# [H1 Hacker's Journey](https://terokarvinen.com/tunkeutumistestaus/#h1-hackers-journey) (Karvinen 2024)

## x) Lue/katso/kuuntele ja tiivistä

### Herrasmieshakkerit - Tapaus Vastaamo, vieraana Marko Leponen (Hyppönen & Tuominen 2024-08-15)

- psykoterapiakeskus Vastaamo tuotti oman potilastietojärjestelmän, jonka tietoturva oli puutteellinen
- Aleksanteri Kivimäki, nimimerkillä ransom_man, vei Vastaamon järjestelmistä potilastietokertomuksia
  - kiristi ensin Vastaamoa, jonka jälkeen potilaita
  - sössi ja julkaisi palvelimellaan tiedoston vastaamo.tar, mikä johti hänen vangitsemiseen ja vankeusrangaistukseen
- poikkeuksellinen tapaus laajudeltaan
  - 33 000 uhria
  - tutkinta-aineiston määrä myös poikkeuksellista 1 petatavu (1000 teratavua)
- Kivimäki vaati kymmenien bitcoinien maksun lunnaiksi
  - poliisi teki kokeilumaksun 0.1 bitcoinin arvosta
  - hyöty ei ollut välitön vaan realisoitui esitutkinnan lopussa
- vastaamo.tar sisälsi mm. ssh-avaimen wi8, jonka Kivimäki myönsi omakseen
- poliisi onnistui myös avaamaan Kivimäen 64-merkkisen salasanan
  - Hyppönen arvelee, että indeksoidusta tutkinta-aineistosta on salasana avattu avainsanoja ja tokeneita yhdistelemällä
  - rikosylikomisario Leponen toteaa, että tämänkaltainen lähestymistapa on "hyvin paljon mahdollista"
- valkohattuhakkerit olivat tapauksessa avuksi
  - vahvistava apu tutkintapolkujen kanssa ja oleellisten foorumeiden osoittamisesta
  - olivat myös apuna uhreille, ettei heitä "lyötäisi toista kertaa"

### Intrusion kill-chain (Hutchins et al. 2011, 4-5)

Intrusion kill-chain vapaasti käännettynä tunkeutumisen suoritusketju on joukko toimintoja, jotka hyökkääjän on kaikki suoritettava tunkeutuakseen kohdejärjestelmään. Analogia ketjusta viittaa jokaisen toiminnan ts. ketjun linkin välttämättömyyteen ja suoritusjärjestykseen.

**Tiedustelu** (reconnaissance). Kohteen tutkinta, tunnistus ja valinta.

**Aseellistaminen** (weaponization). Etäkäyttöön tarkoitetun troijalaisen yhdistäminen hyökkäysohjelman/haavoittuvuuden hyväksikäyttöohjelman (exploit) kanssa. Esim. hyökkäysohjelma pdf:n näköisenä.

**Toimitus** (delivery). Aseen (hyökkäysohjelman) siirto kohteeseen.

**Hyväksikäyttö** (exploitation). Hyökkäysohjelman suoritus kohteen osassa, haavoittuvuudessa (käyttöjärjestelmän osa, kohteessa oleva ohjelma tai kohteen käyttäjä).

**Asennus** (installation). Etäyhteyden asennus, jotta hyökkääjällä on pysyvä päääsy kohdejärjestelmään.

**Hallinta** (command and control). Etäyhteyden jälkeen hyökkääjän on perustettava komento/hallintakanava toimiakseen kohteessa.

**Tavoitteiden toteutus** (actions on objectives). Usein datan siirrot, manipulaatiot ja/tai saatavuuden esto. Tavoitteena voi olla myös lateraali liikkuminen kohdeverkossa (toisten koneiden hyväksikäyttö).

### The Art of Hacking - 4.3 Surveying Essential Tools for Active Reconnaissance: Port Scanning and web Service Review (Santos et al. 2019)

Työkaluja: nmap, masscan, udpprotoscanner, EyeWitness

**nmap** on monipuolisin porttiskanneri.

**masscan** on nopein. Erinomainen, jos kohteita on suuri määrä.

**udp-proto-scanner** on hyvä, jos tarvitsee vain selvittää udp-porttien tila.

**EyeWitness** on web-skanneri. Sitä voi käyttää hyökkäyskohteiden priorisoinnissa. Ottaa kuvan sivusta sekä muuta tietoa palvelimen vastauksesta (response headers).

Eli esim: **nmapilla** selvitetään avoimet ip-osoitteet + portit ja **EyeWitnessillä** katsotaan miten ne vastaavat pyyntöön (löytyikö secret admin page?).

### Tietomurto Vahingonkorvaus - Korvauksen sovittelu (KKO:2003:36)

- A yrittänyt murtaa turvajärjestelyn tunkeutuakseen Osuuspankkikeskuksen (OPK) tietojärjestelmään
- A:n on katsottu olevan atk-alan asiantuntija (tekohetkellä vuonna 1998 17-vuotias)
- A:n tietokoneelta löytyi listoja, jotka sisälsivät porttiskannauksella kerättyä tietoa tai siihen käytettävää tietoa
- Hovioikeudessa todettiin, että "Ei ollut uskottavaa, että A olisi tehnyt skannauksen muussa tarkoituksessa kuin että hän olisi myös tunkeutunut tietojärjestelmään, jos sellainen mahdollisuus olisi löytynyt."
  - tietomurron yritys
- A tuomittiin maksamaan osuuskunnalle 20 000 mk ja yhtiölle 55 00 mk korkoineen
- Korkeimman oikeuden tuomiolauselma: "Hovioikeuden tuomion lopputulosta ei muuteta."

## a) Asenna Kali virtuaalikoneeseen

- Host: Ubuntu 22.04 (amd64)
- Vagrant 2.4.1
- VirtualBox 7.0

Vagrant on minulle aiemmalta opintojaksolta tuttu, joten käytän sitä. Aiemmin asensin debian bookwormin komennolla:

    $ vagrant init debian/bookworm64

Joten kokeillaan:

    $ vagrant init kalilinux/rolling
    $ vagrant up

Komennon parametrin "kalilinux/rolling" löytyi selaamalla olemassa olevia vagrant boxeja HashiCorpin [sivulta](https://portal.cloud.hashicorp.com/vagrant/discover?query=kali) (kuva 1). Virtuaalimasiinan alustamisessa kesti useampi minuutti.

![image](https://github.com/user-attachments/assets/5c812167-9acb-4f08-9caa-a2e8620c870d)
> Kuva 1. Hakutulos hakusanalla "kali" HashiCorpin Vagrant boxeista.

Debian käynnistyi avaamatta graafista käyttöliittymää VirtualBoxissa. Mutta Kali avautui VirtualBoxin liittymässä. Muistin oikein ja gui:tä käyttäen pääsee kirjautumaan tunnuksilla vagrant ja salasana myös vagrant. Otetaan kuitenkin yhteys terminaalissa ssh:lla.

    $ vagrant ssh
    
![image](https://github.com/user-attachments/assets/d27b189f-6cd8-4286-8ec3-4e3cd656fa72)
> Kuva 2. Kali asennettu ja käyttövalmis.

## b) Irrota Kali-virtuaalikone verkosta. Todista testein, että kone ei saa yhteyttä Internetiin (esim. 'ping 8.8.8.8')

Alkutilanne:

![image](https://github.com/user-attachments/assets/88be0462-b73d-402a-a935-6feccbe55e62)
> Kuva 3. Pingaaminen Googlen nimipalvelimelle onnistuu.

Päätin asentaa ufw:n ja kokeilla asettaa säännöt ettei vm kykene ottamaan mitään yhteyksiä (kuva 4). Portti 22 jätetään auki, koska sitä tarvitaan ssh-yhteyden ottamiseksi (ja nykyisen yhteyden ylläpitämiseksi). Ufw komentojen syntaksi: ``man ufw``.

    $ sudo ufw deny out [porttinumero]
    $ sudo ufw allow 22                # ssh
    $ sudo ufw enable

![image](https://github.com/user-attachments/assets/f0d6bf9c-0237-49b3-89ae-52782615e4a3)
> Kuva 4. Ufw:n säännöt.

Tässä tilassa komento ``curl https://terokarvinen.com`` ei tulosta mitään terminaaliin mutta ei myöskään pääty virheilmoitukseen. Pingaaminen Googlen nimipalvelimille vielä toimii (kuva 5).

![image](https://github.com/user-attachments/assets/6681429b-973e-4074-b3b7-01aead86e9c5)
> Kuva 5. Kuvassa 4 näkyvät säännöt eivät estä icmp-liikennettä.

Kokeilin myös graafisessa käyttöliittymässä ottaa "kaapelin" irti (Devices -> Network -> Network Setting -> Advanced -> Cable Connected). Tällöin ei taas ssh-yhteys pelaa...

Googlailin ja päädyin [tähän](https://askubuntu.com/a/793123) käyttäjän Dan AskUbuntu-vastaukseen.

    $ sudo ufw default deny outgoing
    $ sudo ufw default deny incoming
    $ sudo ufw status                 # tarkistus, ettei portti 22 ole kiinni

Tämän jälkeen curlaaminen tai pingaaminen onnistu (kuva 6). Nyt curl tulostaa terminaaliin myös virheilmoituksen.

![image](https://github.com/user-attachments/assets/df4056fa-4462-43c1-8211-1e419f811b2d)
> Kuva 6. Ping 8.8.8.8 eikä curl https://terokarvinen.com kummatkaan toimi.

Täten totean virtuaalimasiinan olevan tilassa, jossa se ei saa yhteyttä "nettiin".

## c) Porttiskannaa 1000 tavallisinta tcp-porttia omasta koneestasi (nmap -A localhost). Analysoi tulokset.

Komento: ``sudo nmap -A localhost``

Optio ``-A`` tarkoittaa "Enable OS detection, version detection, script scanning, and traceroute" (``man nmap``).

Tämä tehtävä tehtiin myös perjantain (2024-25-10) oppitunnilla. Muistan, että silloin tässä samassa koneessa oli auki portit 22/tcp (OpenSSH) sekä 631/tcp (CUPS).

Nyt tilanne oli toinen (kuva 7). Nyt on auki myös portti 111/tcp (rpcbind).

![image](https://github.com/user-attachments/assets/064a4578-b308-4f2f-bb4a-19308070df4b)
> Kuva 7. Localhostin porttiskannaus.

Tulosanalyysi:

Portti 22/tcp on ssh-protokollalle (secure shell) varattu portti. Sen täytyy olla auki sillä koneella, johon halutaan ottaa ssh-yhteys. Ohjelma, joka käyttää sitä on tapauksessani OpenSSH. Minun ei tarvitse pitää tätä auki, joten daemonin voi pysäyttää.

    $ sudo systemctl stop ssh
    $ sudo systemctl disable ssh

Portti 111/tcp kuuntelee rpc-protokollaliikennettä (remote procedure call). Rpc-protokollaa käyttäen voidaan kutsua asiakaskoneelta palvelimella olevia funktioita kuten ne olisivat paikallisia (aws s.a.). Esim. NFS (network file sharing) käyttää rpc-protokollaa. En osaa varmuudella sanoa, minkä takia portti on nyt auki. Se saattaa liittyä siihen, kun partitioin kiintolevyn ja poistin tältä koneelta dual boot Windowsin. Tein sen gparted-ohjelmalla. Hakutulosten perusteella minun ei ole tarvetta pitää porttia auki ja se saattaa olla turvariski (HackTricks 2024). Jos ymmärsin oikein option ``-A`` toiminnan (script scanning osa, toimii myös lipulla -sC) niin nmap tai tarkemmin NSE (nmap script engine) pystyy suorittamaan skriptin kohteessa (nmap.org s.a. a). Kuvassa 7 voi nähdä avoinna olevan portin 111/tcp alla olevassa tulosteessa "rpcinfo", josta näkee lisätietoa rpcbind-daemonista (mm. versionumerot).

    $ sudo systemctl stop rpcbind    # Output: Warning: Stopping rpcbind.service, but it can still be activated by: rpcbind.socket
    $ sudo systemctl stop rpcbind.socket
    $ sudo systemctl disable rpcbind

Tässä vaiheessa tarkistan, että käytöstä poistetut daemonit eivät käynnisty bootatessa ja ne eivät ala kuuntelemaan mitään porttia. Jostain kumman syystä ``sudo nmap -A localhost`` ei tunnistanut kohdekoneen käyttöjärjestelmää (kuva 8). Kokeilin komentoa ``sudo nmap -O localhost`` (``man nmap``), joka tunnisti oikein koneeni käyttiksen oikein Linuxiksi. Tämän jälkeen ajoin uudestaan ``sudo nmap -A localhost`` ja tulosteessa on eritelty OS ja service (kuva 9).

![image](https://github.com/user-attachments/assets/824b6e10-9999-4061-88cf-98bedc53ff3a)
> Kuva 8. Porttiskannaus koneen uudelleenkäynnistyksen jälkeen. Portit 22 ja 111 pysyvät kiinni.

![image](https://github.com/user-attachments/assets/430372e9-a1e0-4a61-8ed3-4ab35ae400e4)
> Kuva 9. OS ja service onnistuneesti havaittu.

Portti 631/tcp kuuntelee ipp-protokollaliikennettä (internet print protocol) ja CUPS on daemon, joka sen on avannut. CUPS on todennäköisesti tullut Ubuntun tai jonkin muun asentamani ohjelman mukana. Minun ei tarvitse pitää porttia auki, koska en aio käyttää tätä konetta tulostinpalvelimena tai tulostimena (Sweet & McDonald 2017).

    $ sudo systemctl stop cups
    $ sudo systemctl disable cups

## d) Asenna kaksi vapaavalintaista demonia ja skannaa uudelleen. Analysoi ja selitä erot.

Asennetaan Apache HTTP-palvelin ja Postfix SMTP-palvelin.

    $ sudo apt install apache2
    $ sudo apt install postfix          # konfiguroinnissa valitsin local only ja default nimen
    $ sudo systemctl status apache2     # tarkistetaan, että daemonit ovat päällä
    $ sudo systemctl status postfix

![image](https://github.com/user-attachments/assets/6a1528cc-de8d-4172-a3d6-23e4f3bd2454)
> Kuva 10. Porttiskannaus uusien daemonien asennuksen jälkeen.

Postfix käynnistyy portissa 25/tcp kuuntelemaan smtp-liikennettä. Nmap/nse on saanut ok-vastaukset skripteihin ``smtp-commands`` ja ``ssl-cert``. Smtp-komennot ovat protokollasession aikana tapahtuvia toimintoja (Yung 2019). ``smtp-commands`` yrittää selvittää smtp-palvelimen tukemat komennot (nmap.org s.a. b). ``ssl-cert`` selvittää tietoja palvelimen ssl-sertifikaatista (nmap.org s.a. c).

Apache2 käynnistyy portissa 80/tcp kuuntelemaan http-liikennettä. Nmap/nse on saanut ok-vastaukset ``http-server-header`` (nmap.org s.a. d) ja ``http-title`` (nmap.org s.a. e). Samankaltaisen tiedon kuin ``http-title`` saa myös esimerkiksi ``curl`` komennolla.

![image](https://github.com/user-attachments/assets/2332a1cd-ea09-4ffe-9fab-e00edeb9422b)
> Kuva 11. Curl ja grep.

En tarvitse näiden palveluiden toimintoja, joten poistetaan ne.

    $ sudo apt remove postfix apache2

![image](https://github.com/user-attachments/assets/ce1e411a-08db-45b4-9ebf-4bd091f427cc)
> Kuva 12. Tilanne postfixin ja apachen poistamisen jälkeen.

## e) Asenna Metasploitable 2 virtuaalikoneeseen

Ladataan ensin metasploitable zippi (https://sourceforge.net/projects/metasploitable/) ja puretaan se. Seurasin metasploitablen asennusohjeita [tämän](https://www.youtube.com/watch?v=1rIvnMenA2g) videon mukaan.

Uuden virtuaalimasiinan luomisen jälkeen yritin säätää sen network-asetuksia käyttämään Host-only adapteria. Tämä ei onnistunut koska host-only -verkkoa ei ole. Loin sen VirtualBoxin käyttöliittymässä File -> Tools -> Network manager -> Create. Uuden verkon luotuani pystyin asettamaan metasploitable-koneen host-only adapteriin VirtualBoxissa koneen asetuksista Settings -> Network. Laitoin aiemmin luodun kali-koneen myös kiinni samaan host-only adapteriin. Mutta kali-kone on sen lisäksi kiinni NATissa, jotta sillä voidaan tarvittaessa ottaa yhteys ulkoiseen verkkoon.

Kirjautumistiedot Metasploitableen näkyvät kuvassa 13 ``msfadmin/msfadmin``.

![image](https://github.com/user-attachments/assets/19dd7ae5-dd82-4d58-a2d6-a40b88719390)
> Kuva 13. Metasploitable vm käynnistyy ja ei saa yhteyttä ulkoverkkoon. 

## f) Tee koneiden välille virtuaaliverkko.

Tämä tulikin käytännössä tehdyksi jo edellisessä kohdassa. Todetaan, että asetukset on tehty tehtävänannon vaatimusten mukaisesti.

    Kali saa yhteyden Internettiin, mutta sen voi laittaa pois päältä      # kuvat 14 ja 15
    Kalin ja Metasploitablen välillä on host-only network, niin että porttiskannatessa ym. koneet on eristetty intenetistä, mutta ne saavat yhteyden toisiinsa     # kuvat 16 ja 17
    Vaihtoehtoisesti voit tehdä molempien koneiden asennuksen ja virtuaaliverkon vagrantilla. Silloin molemmat koneet samaan Vagrantfile:n.
    (Karvinen 2024)

![image](https://github.com/user-attachments/assets/08c5b233-7fd4-4e37-b4e5-402f20baf366)
> Kuva 14. Kali saa yhteyden Internetiin, curl ja ping ok!

Vedetään "kaapeli" irti. Eli poistetaan valinta "Cable Connected" kali-koneesta siitä adapterista, joka on kiinni NATissa. Settings -> Network -> Adapter 1 -> Cable Connected. Testataan kuvassa 14 näkyvät komennot uudestaan.

![image](https://github.com/user-attachments/assets/4a0a7773-5447-4457-8a9d-9f3c2ee8152c)
> Kuva 15. "Kaapeli" on irti.

Tarkistetaan koneiden ip-osoitteet esim. komennolla ``ifconfig``.

Kalin ip-osoite: 192.168.56.102

Metasploitable: 192.168.56.101

![image](https://github.com/user-attachments/assets/2a48c3c2-7174-4452-a7a3-909dc7fbd01e)
> Kuva 16. Pingaus kalista metasploitableen ok.

![image](https://github.com/user-attachments/assets/e1a66e5b-fd7c-434a-8f3f-48c3db2f66ff)
> Kuva 17. Pingaus metasploitablesta kaliin ok.

## g) Etsi Metasploitable porttiskannaamalla (nmap -sn). Tarkista selaimella, että löysit oikean IP:n - Metasploitablen weppipalvelimen etusivulla lukee Metasploitable.

Komennon lippu ``-sn`` on manuaalisivujen mukaan: ``Ping Scan - disable port scan``. Hieman ylempänä manuaalisivuilla oli ohjeet kohteen spesifioimiselle. Komento ja tulokset näkyvät kuvassa 18.

![image](https://github.com/user-attachments/assets/40786ae7-bf6e-44e3-85ce-f09b35a419e7)
> Kuva 18. Komennon ajon tulokset.

Mikä sitten on metasploitable/kohdekone (ajatellen, jos ip-osoite ei olisi tiedossa)? Tuloksista voidaan poistaa oma ip (192.168.56.102) ja default gateway eli reitittimen osoite (192.168.56.1). Jolloin jäljelle jää 192.168.56.100-101. Tehtävänannossa kehotetaan kysymään kohteen weppipalvelimelta, joten:

    $ curl 192.168.56.100    # palauttaa curl: (7) failed to connect
    $ curl 192.168.56.101    # palauttaa metasploitable default-sivun

Minua jäi häiritsemään, että mikä tuo 100-päätteinen ip-osoite on. Se on varmaankin dhcp-palvelin. Mutta en kyennyt sitä todentamaan kali-koneelta käsin. Kokeilin seuraavia komentoja.

    $ grep dhcp-server-identifier /var/lib/dhcp/dhclient.eth0.leases  # output: 10.0.2.2
    $ ip r    # output: 192.168.56.0/24 dev eth1

Tarkistin VirtualBoxin käyttöliittymästä, että dhcp-palvelimen ip-osoite todellakin on 192.168.56.100 (File -> Tools -> Network manager -> DHCP Server).

Ensimmäinen yllä olevista komennoista pitäisi yksiselitteisesti osoittaa, että mistä ip-osoitteesta kone on saanut oman ip-osoitteen. Tiedoston nimessä on ``eth0``. Se on väärä liitäntä, host-only adapteri on ``eth1``. Kokeilin poistaa NATin (``eth0``) pois käytöstä kokonaan, ei siis vain "Cable Connected" pois vaan "Enable Network Adapter" pois.

![image](https://github.com/user-attachments/assets/4eace628-66b3-46b4-8925-f4020f3be281)
> Kuva 19. Kuvakaappaus dhcp-leasesta.

Kuvassa alempi on uudempi lease ja voimassa oleva. Dhcp-palvelin antoi uuden osoitteen (192.168.56.103) ja on nyt liitännässä ``eth0`` ainoana kytkentänä.

## h) Porttiskannaa Metasploitable huolellisesti ja kaikki portit (nmap -A -p-). Poimi 2-3 hyökkääjälle kiinnostavinta porttia. Analysoi ja selitä tulokset näiden porttien osalta.

**1. 21/tcp (ftp).** Ftp on tiedostojen siirtoon käytetty protokolla.

Huomion herätti kohta ``Anynomous login allowed``. Kykenisinkö hyökkääjänä anastamaan tiedostoja tätä hyväksikäyttäen? Tai tuomaan omia haittaohjelmia tästä portista? Applikaatio, joka tätä käyttää, on vsftpd v2.3.4. Nopean googlailun tuloksena löytyi tunnettu keino avata shell kohteessa (https://www.cve.org/CVERecord?id=CVE-2011-2523).

![image](https://github.com/user-attachments/assets/a4786d7f-2455-4208-812e-d229b43158d9)
> Kuva 20. Täyden porttiskannauksen tulos portista 21/tcp.

**2. 111/tcp (rpcbind).** Tämä oli omalla koneellanikin auki, mutta suljin sen kun en keksinyt sille käyttöä ja siihen saattoi liittyä tietoturvariskejä. En selvittänyt olivatko ne kohdallani realistisia, mutta katsoin parhaaksi sulkea portin ja palvelun.

Näyttää siltä, että Metasploitable toimii nfs-palvelimena (network file system). Nfs:n avulla voidaan jakaa ja käyttää tiedostoja kuten ne olisivat paikallisesti saatavilla. [Täältä](https://book.hacktricks.xyz/network-services-pentesting/nfs-service-pentesting) löytyy potentiaalisia hyväksikäyttötapoja.

![image](https://github.com/user-attachments/assets/2d5ccc8f-f748-42dc-8035-3e8727f7e08c)
>Kuva 21. Skannauksen tulos portista 111/tcp.

**3. 3306/tcp (mysql).** Hyökkääjänä minua kiinnostaa kohteessa oleva data. Portti, joka kuuntelee yhteyksiä tietokantaan, vaikuttaa mielenkiintoiselta kohteelta. Applikaation ja version tietämällä pystyy alkaa hyvin selvittämään tietoturvapuutteita. Erityisen mielenkiintoinen tässä tuloksessa on kohta ``Salt: T{0y31<zrNmYV'Yn?-`~``. Onko tämä jotain puppua, joka on lisätty tietueisiin? Jos on niin se varmaankin helpottaa data dekryptaamista.

![image](https://github.com/user-attachments/assets/9a6a7d9d-e973-4e63-9090-3d8da287ff8f)
> Kuva 22. Skannauksen tulos portista 3306/tcp.

## Lähteet

aws, s.a. What’s the Difference Between RPC and REST?. 2024, Amazon Web Services Inc. Luettavissa: https://aws.amazon.com/compare/the-difference-between-rpc-and-rest/. Luettu: 2024-27-10

HackTricks, 2024. 111/TCP/UDP - Pentesting Portmapper. Luettavissa: https://book.hacktricks.xyz/network-services-pentesting/pentesting-rpcbind#basic-information. Luettu: 2024-27-10

Hutchins, E.M., Cloppert, M.J., Amin R.M. 2011. Intelligence-Driven Computer Network Defense Informed by Analysis of Adversary Campaigns and Intrusion Kill Chains. Lockheed Martin Corporation. Luettavissa: [https://lockheedmartin.com/content/dam/lockheed-martin/rms/documents/cyber/LM-White-Paper-Intel-Driven-Defense.pdf](https://lockheedmartin.com/content/dam/lockheed-martin/rms/documents/cyber/LM-White-Paper-Intel-Driven-Defense.pdf). Luettu: 2024-26-10

Hyppönen, M. & Tuominen, T. 2024-08-15. Tapaus Vastaamo, vieraana Marko Leponen. Herrasmieshakkerit. Kuunneltavissa: [https://podcasts.apple.com/fi/podcast/tapaus-vastaamo-vieraana-marko-leponen-0x33/id1479000931?i=1000665442473](https://podcasts.apple.com/fi/podcast/tapaus-vastaamo-vieraana-marko-leponen-0x33/id1479000931?i=1000665442473). Kuunneltu: 2024-26-10.

Karvinen, T. 2024. Tunkeutumistestaus - H1 Hacker's Journey. Luettavissa: https://terokarvinen.com/tunkeutumistestaus/#h1-hackers-journey. Luettu: 2024-26-10

KKO:2003:36. Tietomurto Vahingonkorvaus - Korvauksen sovittelu. Korkeimman oikeuden ennakkopäätös. Luettavissa: https://finlex.fi/fi/oikeus/kko/kko/2003/20030036. Luettu: 2024-26-10

nmap.org s.a. a. The Phases of an Nmap Scan. Luettavissa: https://nmap.org/book/nmap-phases.html. Luettu: 2024-27-10

nmap.org s.a. b. Script smtp-commands. Luettavissa: https://nmap.org/nsedoc/scripts/smtp-commands.html. Luettu: 2024-27-10

nmap.org s.a. c. Script ssl-cert. Luettavissa: https://nmap.org/nsedoc/scripts/ssl-cert.html. Luettu: 2024-27-10

nmap.org s.a. d. Script http-server-header. Luettavissa: https://nmap.org/nsedoc/scripts/http-server-header.html. Luettu: 2024-27-10

nmap.org s.a. e. Script http-title. Luettavissa: https://nmap.org/nsedoc/scripts/http-title.html. Luettu: 2024-27-10

Santos, O., Taylor, R., Sternstein, J., McCoy, C. 2019. The Art of Hacking (Video Collection). O'Reilly. Katsottavissa (vaatii kirjautumisen sekä maksun tai 10pv kokeilujakson hyväksymisen): [https://learning.oreilly.com/videos/the-art-of/9780135767849/9780135767849-SPTT_04_03/](https://learning.oreilly.com/videos/the-art-of/9780135767849/9780135767849-SPTT_04_03/). Katsottu: 2024-26-10

Sweet, M., McDonald, I. 2017. RFC 8010 Internet Printing Protocol/1.1: Encoding and Transport. IETF. Luettavissa: https://www.rfc-editor.org/rfc/rfc8010.html. Luettu: 2024-27-10

Yung, Z. 2019. List of All SMTP Commands and Response Codes. Railsware Products Studio LLC. Luettavissa : https://mailtrap.io/blog/smtp-commands-and-responses/. Luettu: 2024-27-10
