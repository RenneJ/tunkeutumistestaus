# [H7 Hakkeroimaan oppii hakkeroimalla](https://terokarvinen.com/tunkeutumistestaus/#h7-hakkeroimaan-oppii-hakkeroimalla) (Karvinen 2024)

## x) Lue/katso ja tiivistä.

![image](https://github.com/user-attachments/assets/5ad65f7f-3ed2-4be2-b299-912c1090c92f)
> Kuva 1. Jufo-info, IEEE Access. Lähde: https://jfp.csc.fi/jufoportaali?Jufo_ID=78297

### A Systematic Literature Review on the Characteristics and Effectiveness of Web Application Vulnerability Scanners (Alazmi & De Leon 2022)

- Alazmi ja De Leon arvioivat artikkelissaan WVS:ien (Web Vulnerability Scanner) laatua ja tehokkuutta
  - WVS:iä käytetään web-sovellusten haavoittuvuuksien etsimiseen
  - artikkelissa tekijöiden havainnot 90:stä tieteellisestä julkaisusta
  - julkaisuissa 30 eri WVS:ää
    - näistä vain 12 oli arvioitu ("original evaluation" tehty; lienee viittaavan tieteelliseen arviointiin ko. työkalusta)
    - testasivat OWASP TOP10 listalta (pääasiassa) kahta haavoittuvuutta (injection ja XSS)
    - arvioitujen WVS:ien välillä myös suuria eroja haavoittuvuuksien havaitsemisessa
- artikkeleiden valinta tutkimuskohteiksi:
  - neljä hakukonetta, neljä avainsanaa
    - Google Scholar, IEEE Xplore, ACM Digital Library, SpringerLink
    - "vulnerability scanner", "web application vulnerability scanner", "penetration testing tool", "injection tool"
  - artikkeleita karsittiin otsikon, abstractin ja koko tekstin mukaan
    - aluksi 320 artikkelia, karsinnan jälkeen 90
    - näistä 90:stä vain 15:ssä tehtiin vertailua WVS:ien ominaisuuksien/tehokkuuden välillä
    - Alazmin ja De Leonin päätelmät perustuvat näihin 15:een artikkeliin (ja huomattavaan aineiston puutteeseen)
- 15:ssä vertailututkimuksissa eroja tuloksissa suuresti
  - esim. yksi tutkimus toteaa OWASP ZAPin havaitsevan 100% SQL-injektioista ja toisessa sanotaan havaitsemis-% olevan 0

## Lähteet

Alazmi, S. & De Leon, D.C. 2022. A Systematic Literature Review on the Characteristics and Effectiveness of Web Application Vulnerability Scanners. IEEE Access. Luettavissa: https://ieeexplore.ieee.org/abstract/document/9739725 Luettu: 2024-12-10

Karvinen, T. 2024. Hakkeroimaan oppii hakkeroimalla. Tunkeutumistestaus. Luettavissa: https://terokarvinen.com/tunkeutumistestaus/#h7-hakkeroimaan-oppii-hakkeroimalla Luettu: 2024-12-10
