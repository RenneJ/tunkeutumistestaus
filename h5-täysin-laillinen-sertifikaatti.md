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
[
**Cross-site scripting**](https://portswigger.net/web-security/cross-site-scripting)

- haavoittuvuus, jossa hyökkääjä voi syöttää haitallisen skriptin web-ohjelmaan
- jos `alert()` toimii, on kohde haavoittuvainen (`print()` chromella)
- XSS tyypit:
  - reflected: skripti http-pyynnössä
  - stored: skripti sovelluksen tietokannasta
  - DOM-based: skripti asiakaspään koodissa
- puolustus:
  - putsaa käyttäjän syöte

Yhteenvetona kaikille yllä oleville tiivistelmille: **ÄLÄ LUOTA KÄYTTÄJÄÄN!**

## Lähteet

Karvinen, T. 2024. Tunkeutumistestaus. H5 Täysin Laillinen Sertifikaatti. Luettavissa: https://terokarvinen.com/tunkeutumistestaus/#h5-taysin-laillinen-sertifikaatti Luettu: 2024-11-23

OWASP 2021. OWASP Top 10:2021 . Luettavissa: https://owasp.org/Top10/ Luettu: 2024-11-23

PortSwigger 2024. Luettavissa: https://portswigger.net/web-security/all-topics Luettu: 2024-11-23
