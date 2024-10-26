# H1 Hacker's Journey

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

Eli esim: **nmapilla** selvitetään avoimet ip-osoitteet + portit ja EyeWitnessillä katsotaan miten ne vastaavat pyyntöön (löytyikö secret admin page?).

## Lähteet

Hutchins, E.M., Cloppert, M.J., Amin R.M. 2011. Intelligence-Driven Computer Network Defense Informed by Analysis of Adversary Campaigns and Intrusion Kill Chains. Lockheed Martin Corporation. Luettavissa: [https://lockheedmartin.com/content/dam/lockheed-martin/rms/documents/cyber/LM-White-Paper-Intel-Driven-Defense.pdf](https://lockheedmartin.com/content/dam/lockheed-martin/rms/documents/cyber/LM-White-Paper-Intel-Driven-Defense.pdf). Luettu: 2024-26-10

Hyppönen, M. & Tuominen, T. 2024-08-15. Tapaus Vastaamo, vieraana Marko Leponen. Herrasmieshakkerit. © 2019 F-Secure. Kuunneltavissa: [https://podcasts.apple.com/fi/podcast/tapaus-vastaamo-vieraana-marko-leponen-0x33/id1479000931?i=1000665442473](https://podcasts.apple.com/fi/podcast/tapaus-vastaamo-vieraana-marko-leponen-0x33/id1479000931?i=1000665442473). Kuunneltu: 2024-26-10.

Santos, O., Taylor, R., Sternstein, J., McCoy, C. 2019. The Art of Hacking (Video Collection). O'Reilly. Katsottavissa, vaatii kirjautumisen sekä maksun tai 10pv kokeilujakson hyväksymisen: [https://learning.oreilly.com/videos/the-art-of/9780135767849/9780135767849-SPTT_04_03/](https://learning.oreilly.com/videos/the-art-of/9780135767849/9780135767849-SPTT_04_03/). Katsottu: 2024-26-10
