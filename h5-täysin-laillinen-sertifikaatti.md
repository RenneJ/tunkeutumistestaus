# [H5 Täysin Laillinen Sertifikaatti](https://terokarvinen.com/tunkeutumistestaus/#h5-taysin-laillinen-sertifikaatti) (Karvinen 2024)

## x) Lue/katso ja tiivistä.

### OWASP Top 10:2021

**A01:2021 – Broken Access Control** (OWASP 2021a)

- nostettu ykkösisjalle viidenneltä
- lyhyesti: huonosti määritellyt käyttöoikeudet
  - "deny by default", "least privilege" ei käytössä
  - http-pyynnön muokkaaminen kiertää määritykset (esim. `url.com/admin` ei pääsyä, mutta `url.com/admin/controls` ok)

Hieman back end ohjelmointia tehneenä voin sanoa, ettei käyttöoikeuksien määritteleminen oikein ole hankalaa. Mutta, jos se täytyy tehdä tarkasti 99 kertaa, onnistuuko se 100:nnella kerralla? Tai 1000:nnella?

**A10:2021 – Server-Side Request Forgery (SSRF)** (OWASP 2021b)

- tapahtuu, kun web-ohjelma hakee dataa käyttäjän syöttämästä url:ista validoimatta sitä ensin
  - palvelin hakee väärästä (vaarallisesta) osoitteesta resurssin
- suojatuminen:
  - verkon segmentointi (lieventää riskiä noutaa dataa väärästä paikasta)
  - putsaa ja validoi käyttäjän syöte
  - deny by default, sallitut kohteet eksplisiittisesti listattu
  - ei "raakaa" vastausta takaisin asiakkaalle

### PortSwigger Academy

## Lähteet

Karvinen, T. 2024. Tunkeutumistestaus. H5 Täysin Laillinen Sertifikaatti. Luettavissa: https://terokarvinen.com/tunkeutumistestaus/#h5-taysin-laillinen-sertifikaatti Luettu: 2024-11-23

OWASP 2021a. A01:2021 – Broken Access Control. Luettavissa: https://owasp.org/Top10/A01_2021-Broken_Access_Control/ Luettu: 2024-11-23

OWASP 2021b. A10:2021 – Server-Side Request Forgery (SSRF). Luettavissa: https://owasp.org/Top10/A10_2021-Server-Side_Request_Forgery_%28SSRF%29/ Luettu: 2024-11-23
