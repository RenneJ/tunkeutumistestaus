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

Komennon parametrin "kalilinux/rolling" löytyi selaamalla olemassa olevia vagrant boxeja HashiCorpin [sivulta](https://portal.cloud.hashicorp.com/vagrant/discover?query=kali) (kuva 1). Virtuaalimasiinan alustamisessa kesti useampi minuutti. Varmaankin siksi, että levykuva on ISO.

![image](https://github.com/user-attachments/assets/5c812167-9acb-4f08-9caa-a2e8620c870d)
> Kuva 1. Hakutulos hakusanalla "kali" HashiCorpin Vagrant boxeista.

Debian käynnistyi avaamatta graafista käyttöliittymää VirtualBoxissa. Mutta Kali avautui VirtualBoxin liittymässä. Muistin oikein ja gui:tä käyttäen pääsee kirjautumaan tunnuksilla vagrant ja salasana myös vagrant. Otetaan kuitenkin yhteys terminaalissa ssh:lla.

    $ vagrant ssh
    
![image](https://github.com/user-attachments/assets/d27b189f-6cd8-4286-8ec3-4e3cd656fa72)
> Kuva 2. Kali asennettu ja käyttövalmis.

## b) Irrota Kali-virtuaalikone verkosta

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

### c) Porttiskannaa 1000 tavallisinta tcp-porttia omasta koneestasi

Tämä tehtävä tehtiin myös perjantain (2024-25-10) oppitunnilla. Muistan, että silloin tässä samassa koneessa oli auki portit 22/tcp (OpenSSH) sekä 631/tcp (CUPS).

Nyt tilanne oli toinen (kuva 7). Nyt on auki myös portti 111/tcp (rpcinfo).

![image](https://github.com/user-attachments/assets/064a4578-b308-4f2f-bb4a-19308070df4b)
> Kuva 7. Localhostin porttiskannaus.

Tulosanalyysi:

Portti 22/tcp on ssh-protokollalle (secure shell) varattu portti. Sen täytyy olla auki sillä koneella, johon otetaan yhteys (serveri). Ohjelma, joka käyttää sitä on tapauksessani OpenSSH. Minun ei tarvitse pitää tätä auki, joten daemonin voi pysäyttää ``sudo systemctl disable``. Ymmärtääkseni komennolla ``stop`` voi myös pysäyttää, mutta palvelu käynnistyy koneen uudelleenkäynnistyessä. Ssh:n saa takaisin päälle ``sudo systemctl enable ssh``.

Portti 111/tcp kuuntelee rpc-protokollaliikennettä (remote procedure call). Rpc-protokollaa käyttäen voidaan kutsua asiakaskoneelta palvelimella olevia funktioita kuten ne olisivat paikallisia 

## Lähteet

Amazon Web Services, s.a. What’s the Difference Between RPC and REST?. 2024, Amazon Web Services Inc. Luettavissa: [https://aws.amazon.com/compare/the-difference-between-rpc-and-rest/]. Luettu: 2024-27-10

Hutchins, E.M., Cloppert, M.J., Amin R.M. 2011. Intelligence-Driven Computer Network Defense Informed by Analysis of Adversary Campaigns and Intrusion Kill Chains. Lockheed Martin Corporation. Luettavissa: [https://lockheedmartin.com/content/dam/lockheed-martin/rms/documents/cyber/LM-White-Paper-Intel-Driven-Defense.pdf](https://lockheedmartin.com/content/dam/lockheed-martin/rms/documents/cyber/LM-White-Paper-Intel-Driven-Defense.pdf). Luettu: 2024-26-10

Hyppönen, M. & Tuominen, T. 2024-08-15. Tapaus Vastaamo, vieraana Marko Leponen. Herrasmieshakkerit. © 2019 F-Secure. Kuunneltavissa: [https://podcasts.apple.com/fi/podcast/tapaus-vastaamo-vieraana-marko-leponen-0x33/id1479000931?i=1000665442473](https://podcasts.apple.com/fi/podcast/tapaus-vastaamo-vieraana-marko-leponen-0x33/id1479000931?i=1000665442473). Kuunneltu: 2024-26-10.

Karvinen, T. 2024. Tunkeutumistestaus - H1 Hacker's Journey. Luettavissa: [https://terokarvinen.com/tunkeutumistestaus/#h1-hackers-journey](https://terokarvinen.com/tunkeutumistestaus/#h1-hackers-journey). Luettu: 2024-26-10

KKO:2003:36. Tietomurto Vahingonkorvaus - Korvauksen sovittelu. Korkeimman oikeuden ennakkopäätös. Lueattavissa: [https://finlex.fi/fi/oikeus/kko/kko/2003/20030036](https://finlex.fi/fi/oikeus/kko/kko/2003/20030036). Luettu: 2024-26-10

Santos, O., Taylor, R., Sternstein, J., McCoy, C. 2019. The Art of Hacking (Video Collection). O'Reilly. Katsottavissa (vaatii kirjautumisen sekä maksun tai 10pv kokeilujakson hyväksymisen): [https://learning.oreilly.com/videos/the-art-of/9780135767849/9780135767849-SPTT_04_03/](https://learning.oreilly.com/videos/the-art-of/9780135767849/9780135767849-SPTT_04_03/). Katsottu: 2024-26-10
