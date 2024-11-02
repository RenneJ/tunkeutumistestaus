# [H2 Social sploit](https://terokarvinen.com/tunkeutumistestaus/#h2-social-sploit) (Karvinen 2024)

## x) Lue/katso/kuuntele ja tiivistä

### Chapter 1: Approaching a Penetration Test Using Metasploit (Jaswal 2020)

#### Conducting a penetration test with Metasploit

Termistöä:

- Exploits: haavoittuvuuksia hyväksikäyttävä koodikokonaisuus. Suomeksi hyväksikäyttövälittäjä(?)
- Payload: hyötykuorma. Kohdekoneen hyväksikäytön jälkeen suoritettava koodi. Määrittelee kohteessa suoritettavat toimet.
- Auxiliary: lisämoduulit, jotka suorittavat esim. skannausta.
- Encoders: moduuleiden kooderi, jolla pyritään hämäämään puolustajaa muuttamalla hyökkäystyökaluja vaarattoman näköisiksi tai vaikeasti havaittaviksi.
- Meterpreter: hyötykuorma, joka injektoidaan kohteen työmuistiin. Hyötykuorma sisältää hyödynnettäviä funktioita/komentoja (esim. ``meterpreter> arp`` listaa kohteeseen kytketyt ip- ja mac-osoitteet).

#### Benefits of penetration testing using Metasploit

Metasploitin hyödyt verrattuna manuaalisiin tekniikoihin:

- Avoin lähdekoodi, jota aktiivisesti kehitetään.
  - Omien moduuleiden lisääminen mahdollista
- Tukee laajojen järjestelmien testausta.
  - Osoiteavaruudet nimeämiskäytäntöjen mukaan.
- Hyötykuormien vaihtaminen ja määrittely helppoa.
- "Puhdas poistuminen". On tärkeää, ettei kaada järjestelmää, josta yrittää poistua.
  - Edesauttaa uudelleenpääsyä järjestelmään.
- Tiedustelutiedon kerääminen.
  - Mahdollisuus kerätä tietoa taustaprosessina automatisoidusti tietokantaan.

#### Case study – reaching the domain controller

- Tiedustelu
  - Metasploitin ``db_nmap``, skannauksen tulokset tietokantaan.
  - Skannaamalla löytyy kohteen käyttöjärjestelmä.
  - Käyttöjärjestelmä tietäen ajetaan skripti, joka löytää tunnetun haavoittuvuuden.
- Threat Modeling
  - Metasploit ``search``-toiminto listaa käytössä olevia moduuleja tunnettuun haavoittuvuuteen.
  - ``vulns`` tallentaa tiedot haavoittuvuuksista tietokantaan
- Hyväksikäyttö ja pääsyn saanti
  - ``use <exploit/path/to/module>``, lataa moduulin.
  - ``show options`` listaa käytössä olevat parametrit ja luokittelee vaaditut/vaihtoehtoiset parametrit.
  - ``exploit``, lippu ``-j`` ajaa hyökkäyksen taustalla.
- Post-exploitation kung-fu
  - Siirtämällä hyökkäysprosessi powershellistä johonkin vähemmän epäilyttävään parannetaan hyökkäyksen piilossa pysymistä
  - Hyökkääjä tekaisee itsenä adminiksi päästäkseen domain controlleriin
  - Active Directorysta saadaa kaikkien käyttäjien salasanatiivisteet (``smart_hashdump``)
  - mimikatzilla saa selkotekstinä käyttäjätunnuksia

## a) Harjoittelemme omassa virtuaaliverkossa, jossa on Kali ja Metaspoitable. Osoita testein, että 1) koneet eivät saa yhteyttä Internetiin 2) Koneet saavat yhteyden toisiinsa.

![image](https://github.com/user-attachments/assets/e16175fa-4d51-457a-ad6c-9fa8eca6f18f)![image](https://github.com/user-attachments/assets/e821aa16-23d2-4c84-9382-374c6c37dc63)
> Kuvat 1 & 2. Kali eikä Metasploitable saa yhteyttä Internetiin.

![image](https://github.com/user-attachments/assets/68f43f25-9c11-4e3b-8483-adf743219ea8)![image](https://github.com/user-attachments/assets/44804a7f-27e6-46df-a9be-98de9f696e14)
> Kuvat 3 & 4. Koneet saavat yhteyden toisiinsa.



## Lähteet

Jaswal, N. 2020. Mastering Metasploit. Packt Publishing. Luettavissa: https://learning.oreilly.com/library/view/mastering-metasploit/9781838980078/B15076_01_Final_ASB_ePub.xhtml#_idTextAnchor017 Luettu: 2024-11-02

Karvinen, T. 2024. Tunkeutumistestaus. H2 Social sploit. Luettavissa: https://terokarvinen.com/tunkeutumistestaus/#h2-social-sploit Luettu: 2024-11-02
