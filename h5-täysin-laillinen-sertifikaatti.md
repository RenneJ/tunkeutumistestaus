# [H5 Täysin Laillinen Sertifikaatti](https://terokarvinen.com/tunkeutumistestaus/#h5-taysin-laillinen-sertifikaatti) (Karvinen 2024)

## x) Lue/katso ja tiivistä.

### OWASP Top 10:2021 (OWASP 2021)

[**A01:2021 – Broken Access Control**](https://owasp.org/Top10/A01_2021-Broken_Access_Control/)

- nostettu ykkösisjalle viidenneltä
- lyhyesti: huonosti määritellyt käyttöoikeudet
  - "deny by default", "least privilege" ei käytössä
  - http-pyynnön muokkaaminen kiertää määritykset (esim. `url.com/admin` ei pääsyä, mutta `url.com/admin/controls` ok)

Hieman back end ohjelmointia tehneenä voin sanoa, ettei käyttöoikeuksien määritteleminen oikein ole hankalaa. Mutta, jos se täytyy tehdä tarkasti 999 kertaa, onnistuuko se 1000:nnella kerralla?

[**A10:2021 – Server-Side Request Forgery (SSRF)**](https://owasp.org/Top10/A10_2021-Server-Side_Request_Forgery_%28SSRF%29/)

- tapahtuu, kun web-ohjelma hakee dataa käyttäjän syöttämästä url:ista validoimatta sitä ensin
  - palvelin hakee väärästä (vaarallisesta) osoitteesta resurssin
- suojatuminen:
  - verkon segmentointi (lieventää riskiä noutaa dataa väärästä paikasta)
  - putsaa ja validoi käyttäjän syöte
  - deny by default, sallitut kohteet eksplisiittisesti listattu
  - ei "raakaa" vastausta takaisin asiakkaalle

### PortSwigger Academy (PortSwigger 2024)

[**Insecure direct object references (IDOR)**](https://portswigger.net/web-security/access-control/idor)

- suora pääsy tietokannan tietueisiin tai tiedostojärjestelmään url-parametriä muokkaamalla
- osa "access controlia"

[**Path traversal**](https://portswigger.net/web-security/file-path-traversal)

- kulkemista hakemistojen välillä palvelimella, joka pyörittää verkkosovellusta
- esim. `url.com/getfile?filename=../../../etc/passwd`
  - suorittaa `getfile` endpointtia vastaavan metodin
  - metodin parametri `filename` saa arvokseen tiedostopolun
  - `../../../etc/passwd` on suhteellinen polku, joka nousee tiedostohakemistossa kolme kohtaa ylöspäin juureen ja yhden alaspäin `etc` hakemistoon ja noutaa tiedoston `passwd`
[
**Server-side template injection**](https://portswigger.net/web-security/server-side-template-injection)

- käyttäjän syöte käsitellään palvelimella, mikä vaikuttaa template-prosessoijaan ja sitä kautta näytettävään html-tiedostoon
- jos template-kielien yleisesti käytetyt merkit `${{<%[%'"}}%\` nostavat virheilmoituksen käyttäjän syötteestä saattaa kohde olla haavoittuvainen
- jos laskutoimitus (esim. `{ 7 * 7 }`) evaluoidaan oikein palautettavaan html-tiedostoon, on kohde melko varmasti altis tälle haavoittuvuudelle
- paras puolustuskeino on estää käyttäjää antamasta syötettä, joka muokkaa tai lisää uuden templaten

[**Server-side request forgery (SSRF)**](https://portswigger.net/web-security/ssrf)

- hyökkääjä manipuloi palvelinta ottamaan yhteyden epätarkoituksenmukaiseen kohteeseen
  - sisäverkon palvelu tai ulkopuolinen
- hyökkääjä voi muokata http-pyynnön näyttämään admin-sivun
  - jos pyyntö tulee palvelimelle luotetusta lähteestä kuten `localhost`, hyökkääjä on onnistunut kiertämään mahdolliset muut suojamekanismit
  - pyyntöä voi muokata myös reitittymättömään osoitteeseen
- puolustus:
  - blacklist: "blokkaa nämä"
  - whitelist: "ainoastaan nämä ok"

Miksi [esimerkissä](https://portswigger.net/web-security/ssrf) on POST pyyntö? Esimerkissä puhutaan ainoastaan tiedon noutamisesta.

[**Cross-site scripting**](https://portswigger.net/web-security/cross-site-scripting)

- haavoittuvuus, jossa hyökkääjä voi syöttää haitallisen skriptin web-ohjelmaan
- jos `alert()` toimii, on kohde haavoittuvainen (`print()` chromella)
- XSS tyypit:
  - reflected: skripti http-pyynnössä
  - stored: skripti sovelluksen tietokannasta
  - DOM-based: skripti asiakaspään koodissa
- puolustus:
  - putsaa käyttäjän syöte

Yhteenvetona kaikille yllä oleville tiivistelmille: **ÄLÄ LUOTA KÄYTTÄJÄÄN!**

## a) Totally Legit Sertificate. Asenna OWASP ZAP, generoi CA-sertifikaatti ja asenna se selaimeesi. Laita ZAP proxyksi selaimeesi. Laita ZAP sieppaamaan myös kuvat, niitä tarvitaan tämän kerran kotitehtävissä. Osoita, että hakupyynnöt ilmestyvät ZAP:n käyttöliittymään.

Asensin virtuaalikoneeseen ZAPin edellisellä opetuskerralla (2024-11-22).

Lataa sopiva paketti [täältä](https://www.zaproxy.org/download/). Itse käytin java-pakettia (Cross Platform Package). Kyseessä on java-ohjelma, joten jre (java runtime environment) vaaditaan (Javatpoint 2024).

**Cross Platform Packagen asennus.**

    wget https://github.com/zaproxy/zaproxy/releases/download/v2.15.0/ZAP_2.15.0_Crossplatform.zip    # lataa
    unzip ../ZAP_2.15.0_Crossplatform.zip    # pura
    cd ZAP_2.15.0
    java -jar zap-2.15.0.jar    # aja

Vaikka en ensimmäisellä kerralla tehnyt asennusta tismalleen näin (en käyttänyt `wget` enkä `unzip`), testasin komentojen toimivuuden (VirtualBox 7.0 Kali rolling ja host Ubuntu 22.04LTS). Ohjelma lähti onnistuneesti pyörimään.

![image](https://github.com/user-attachments/assets/140cd0a2-b02a-4b75-a67a-872831753417)
> Kuva 1. ZAP aukeaa. Ensimmäinen asennus.

**Sertifikaatin lisääminen selaimeen.**

Tools -> Options -> Network -> Server Certificates -> Save

![image](https://github.com/user-attachments/assets/853a8fe2-045c-463f-8715-d5f3540dc6c3)
> Kuva 2. ZAP 2.15.0 näkymä sertifikaatin luomisesta.

Minulla oli sertifikaatti valmiina. Painike "Generate" luo uuden. Painamalla "Save" sertifikaatti tallentuu tiedostojärjestelmään, mikä vaaditan sertifikaatin lisäämiseksi selaimeen.

Seuraavaksi selaimessa (Firefox). Settings tai url-kenttään "about:preferences" -> hakukenttään "cert" -> View Certificates -> Import -> valitse tallennettu sertifikaatti (oletusnimi zap_root_ca.cer) -> Open.

![image](https://github.com/user-attachments/assets/394ec323-402c-4649-a67f-0b5a696ea1f3)
> Kuva 3. Sertifikaatti tallennettu Firefoxiin.

**Proxyn lisääminen Firefoxiin**

Seuraavaksi pistetään Firefoxilla lähetetyt pyynnöt menemään proxyn kautta.

Settings tai url-kenttään "about:preferences" -> hakukenttään "proxy" ja ao. kuvan mukaiset määritykset. 

![image](https://github.com/user-attachments/assets/c74856e6-6757-45c0-b432-e3bad8b0c039)
> Kuva 4. Proxy setting Firefoxissa.

Kuvan alareunassa on ilmoitukset localhostin toimimattomuudesta. Kokeilin aluksi NATattua liittymää (10.0.2.15) localhostin sijaan mutta se ei toiminut. Tämän jälkeen kokeilin käynnistää Firefoxin ZAPin kautta, jolloin sain kaapattua liikennettä proxyyn. Tarkistin proxy-asetukset ZAP-Firefoxista ja toistin ne toiseen Firefox-instanssiin. Kas näin se localhost vaan toimikn.

**Kuvat mukaan ZAPiin**

En lähtenyt ZAPin käyttöliittymästä etsimään ratkaisua vaan turvauduin heti internetin apuun. Hakusanoilla "get images in zap proxy" sain [tämän](https://stackoverflow.com/a/46686815) vastauksen. Noudatin niitä ja virkistin selaimen pyynnön (Ctrl + F5).

![image](https://github.com/user-attachments/assets/4337e68a-9c43-4c1f-a485-36ddb92b5cd1)
> Kuva 5. Response. Content-type: image/svg+xml.

## b) Kettumaista. Asenna "FoxyProxy Standard" Firefox Addon, ja lisää ZAP proxyksi siihen. Käytä FoxyProxyn "Patterns" -toimintoa, niin että vain valitsemasi weppisivut ohjataan Proxyyn.

Otetaan proxy ensin pois päältä Firefoxissa. Navigoidaan takaisin kuvassa 4 näkyvään proxy-asetusten määrittelyikkunaan ja valitaan "No proxy".

Seuraavaksi joko palapelikuvaketta oikeassa yläreunassa tai url-kenttään "about:addons" -> hakukenttään "foxyproxy" -> Enter.

![image](https://github.com/user-attachments/assets/dc199f48-3caa-4a8a-8a41-3d339ac71e6a)
> Kuva 6. FoxyProxy Standard.

Painetaan "Add to Firefox". FoxyProxy asennettu.

![image](https://github.com/user-attachments/assets/8dfb492e-5848-40bc-84bf-646c1385c0de)
> Kuva 7. FoxyProxy kuvake toolbariin. Paina "Pin to toolbar".

Painamalla FoxyProxy -kuvaketta ja valitsemalla "Options" pääsee FoxyProxyn kontrollipaneeliin. Välilehdeltä "Proxies" pääsee lisäämään uuden proxyn (ks. kuva alla).

![image](https://github.com/user-attachments/assets/e3cee22a-4189-46dd-a341-26eaa10a350f)
> Kuva 8. Muista valita väri ZAPin teemaan sopivaksi!

Seuraavaksi laitetaan proxyttavaksi ainoastaan localhost ja PortSwigger labs.

Tässä vaiheessa testasin, että ZAP nappaa liikennettä kuten aiemmin. Näin olikin ja opin/päättelin, että vastaus `304 Not Modified` tarkoittaa sitä, että kuvia ei haeta toistamiseen. Pakotettu uudelleenhaku (Ctrl + F5) tekee pyynnön juurta jaksain ja kuvat noudetaan.

Syötetään FoxyProxyn käyttöliittymässä sääntö, joka ohjaa proxyyn ainoastaan PortSwiggerin labraan menevät pyynnöt ja sieltä saapuvat vastaukset.

![image](https://github.com/user-attachments/assets/1d7cc385-aad9-4244-8873-ba3adce730f7)
> Kuva 9. Säännön lisäys.

Painamalla Save lisätään sääntö.

**HUOM!** Sääntöperusteinen proxyttaminen otetaan käyttöön klikkaamalla FoxyProxy -kuvaketta ja valitsemalla "Proxy by Patterns". 

![image](https://github.com/user-attachments/assets/c84747f1-36ce-482d-890c-2f99fd68c035)
> Kuva 10. Sääntö- tai malliperusteinen proxy käytössä.

![image](https://github.com/user-attachments/assets/0154dd87-89ef-42cf-9d16-b6cbaeada2f1)
> Kuva 11. Pyyntöä githubille ei kierrätetty ZAPin kautta.

## PortSwigger Labs. Ratkaise tehtävät. Selitä ratkaisusi: mitä palvelimella tapahtuu, mitä eri osat tekevät, miten hyökkäys löytyi, mistä vika johtuu. Kannattaa käyttää ZAPia, vaikka malliratkaisut käyttävät harjoitusten tekijän maksullista ohjelmaa. Malliratkaisun kopioiminen ZAP:n tai selaimeen ei ole vastaus tehtävään, vaan ratkaisu ja haavoittuvuuden etsiminen on selitettävä ja perusteltava.

## c) [Insecure Direct Object Reference (IDOR)](https://portswigger.net/web-security/access-control/lab-insecure-direct-object-references)

Tämä PortSwigger haaste onnistui helposti. Tehtäväannossa sanottiin, että chat-lokit ovat turvattomasti haettavissa tiedostojärjestelmästä. Siispä lähetin chattiin viestin, sain vastauksen ja latasin lokitiedoston käyttöliittymästä. Lähetin viestin siksi, että saisin tarkan urlin, jota muokata. Right-klikkaamalla ladattua tiedostoa selaimen "Downloads" listauksessa saa näppärästi kopioitua urlin. Yksi vaihtoehto on etsiä url ZAPista (kuva 13).

![image](https://github.com/user-attachments/assets/4388c16e-e30c-4049-9393-1f9798724e5e)
> Kuva 12. Urli talteen.

![image](https://github.com/user-attachments/assets/be65b0e4-4ccf-46fc-92dc-5793aaf72d7d)
> Kuva 13. GET-pyyntö ZAPissa. Ks. pyynnön url, eka rivi.

Hmm.... tiedoston nimi on 2.txt. Kokeillaan muuttaa urliin sen tilalle 1.txt.

Näin sain toisen käyttäjän keskustelun itselleni ja keskustelusta löytyi käyttäjän salasana selkokielisenä. Käyttäjänimi olikin annettu tehtävänannossa.

![image](https://github.com/user-attachments/assets/e8dba883-f639-4d34-b7a6-7b1d05e3aa0a)
> Kuva 14. Solved!

Haavoittuvuus johtuu siitä, että kuka tahansa asiakas pääsee käsiksi palvelimen tiedostojärjestelmään, muokkaamalla  `/download-transcript/` jälkeistä osaa selaimen url-kentässä. 

## d) [File path traversal, simple case](https://portswigger.net/web-security/file-path-traversal/lab-simple)

Tehtävänannossa sanottiin, että tuotteiden kuvien näyttämisessä on haavoittuvuus. Joten ensimmäiseksi avasin tuotekuvan linkin navigoimalla tuotesivulle ja right-klikkaamalla kuvaa ja "Open Image in New Tab".

![image](https://github.com/user-attachments/assets/82f2b47a-5b03-4715-b8b4-7db62b344e2e)
> Kuva 15. Tuotekuvan url.

Eka yritys hyväksikäyttää haavoittuvuutta.

![image](https://github.com/user-attachments/assets/5cc5270a-087f-47ec-a686-b593c653c8b1)
> Kuva 16. Absoluuttinen polku ei toimi.

Kokeilin seuraavaksi lisäillä `/etc/passwd` eteen `../` eli käyttää suhteellista polkua. Lisäilin niitä yhden kerrallaan ja sain kuvan 16 mukaisen vastauksen, kunnes...

![image](https://github.com/user-attachments/assets/0eef2308-e31e-4a48-8615-5e73b2054525)
> Kuva 17. Vastaus sisältää errorin.

Jotain tapahtui. Mutta selaimeen ei tulostunut `/etc/passwd` sisältöä.

Tutkaillaan pyyntö ZAPissa. Oikean pyynnön etsiminen helpottui, kun poistin url-kentän History-näkymästä ja laitoin sen tilalle "Path and Query".

Kesti hetken aikaa tajuta, että vastaus (response) sisälsi kuvan lisäksi myös tekstiä.

![image](https://github.com/user-attachments/assets/c0e3ab4d-eb84-4a8f-b716-bc3d92ac52db)
> Kuva 18. Vastaus sisältää muutakin kuin kuvan.

![image](https://github.com/user-attachments/assets/2cf7c4b1-8635-4466-b091-ca8d10f9bef5)
> Kuva 19. Solved!

Haavoittuvuus johtuu siitä, että käyttäjän syöte url-kenttään määrittää hakemistopolun tai sen osan.

## e) [File path traversal, traversal sequences blocked with absolute path bypass](https://portswigger.net/web-security/file-path-traversal/lab-absolute-path-bypass)

Tämä kohta on mielestäni helpompi kuin edellinen. Tässä kohtaa puolustaja on estänyt liikkumisen hakemistojen välillä (`../`) tehden absoluuttisen polun käyttämisen mahdolliseksi. Kokeilin käyttää absoluuttista polkua edellisessä kohdassa aivan ensin, koska se olisi ollut minusta yksinkertaisin tapa päästä käsiksi kohdetiedostoon. Tässä kohtaa ei tarvitse edes vaivautua selvittämään verkkopalvelimen hakemistorakennetta, tyyliin `/var/www/foo/bar`.

![image](https://github.com/user-attachments/assets/a785b3e7-709c-49ec-9e1d-601ff8476b62)
> Kuva 20. Absoluuttinen polku toimii.

Puolustajan tapa ei ole mielestäni vaikeuttanut hyökkääjän toimia. Päinvastoin, se on jopa helpompaa.

Haavoittuvuus johtuu siitä, että käyttäjän syöte url-kenttään määrittää hakemistopolun tai sen osan.

## f) [File path traversal, traversal sequences stripped non-recursively](https://portswigger.net/web-security/file-path-traversal/lab-sequences-stripped-non-recursively)

Aloitin analysoimalla mitä tarkoittaa "traversal sequences stripped non-recursively".

![image](https://github.com/user-attachments/assets/cf4df423-1c43-480e-a229-e378d801fd4f)
> Kuva 21. Kuvakaappauksen lähde: https://portswigger.net/web-security/file-path-traversal#reading-arbitrary-files-via-path-traversal

Käyttämissäni ohjelmointikielissä merkkijonoille löytyy `strip()` (tai jokin vastaava) metodi, joka poistaa merkkijonosta halutun merkkijonon osan (substringin tai regex).

Tässä haasteessa path traversal -haavoittuvuutta on pyritty estämään (olettamani mukaan) poistamalla käyttäjän syötteestä `../` merkkijono. Jos kokeilee kohdan d) mukaista syötettä `../../../etc/passwd`, palvelinpäässä riisuttaisiin siitä jokainen `../` substring. PortSwiggerin [materiaalissa](https://portswigger.net/web-security/file-path-traversal#reading-arbitrary-files-via-path-traversal) neuvotaan kokeilemaan `....//`. Tällöin `strip()` poistaisi kyseisestä merkkijonosta merkit 3-5 jättäen jäljelle tismalleen saman merkkijonon. Eli, jos tätä metodia ei kutsuta rekursiivisesti, `....//` muutetaan `../`.

![image](https://github.com/user-attachments/assets/5a72a0c0-e032-47f9-8715-7cb0e1c933ba)
> Kuva 22. Pyyntö ja palvelimen vastaus ZAPissa.

![image](https://github.com/user-attachments/assets/026118e3-f30f-4ad1-b559-aad404cd7c6f)
> Kuva 23. Solved!

Haavoittuvuus johtuu siitä, että käyttäjän syöte url-kenttään määrittää hakemistopolun tai sen osan.



## Lähteet

Javatpoint 2024. What is JRE? Luettavissa: https://www.javatpoint.com/java-jre Luettu: 2024-11-23

Karvinen, T. 2024. Tunkeutumistestaus. H5 Täysin Laillinen Sertifikaatti. Luettavissa: https://terokarvinen.com/tunkeutumistestaus/#h5-taysin-laillinen-sertifikaatti Luettu: 2024-11-23

OWASP 2021. OWASP Top 10:2021 . Luettavissa: https://owasp.org/Top10/ Luettu: 2024-11-23

PortSwigger 2024. Luettavissa: https://portswigger.net/web-security/all-topics Luettu: 2024-11-23
