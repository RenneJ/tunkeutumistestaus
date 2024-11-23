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

### PortSwigger Academy

[**Insecure Direct Object References (IDOR)**](https://portswigger.net/web-security/access-control/idor)



## Lähteet

Karvinen, T. 2024. Tunkeutumistestaus. H5 Täysin Laillinen Sertifikaatti. Luettavissa: https://terokarvinen.com/tunkeutumistestaus/#h5-taysin-laillinen-sertifikaatti Luettu: 2024-11-23

OWASP 2021. OWASP Top 10:2021 . Luettavissa: https://owasp.org/Top10/ Luettu: 2024-11-23

PortSwigger 2024. Luettavissa: https://portswigger.net/web-security/all-topics Luettu: 2024-11-23
