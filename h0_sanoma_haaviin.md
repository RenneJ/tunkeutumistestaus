# [h0 Sanoma haaviin](https://terokarvinen.com/tunkeutumistestaus/#h0-sanoma-haaviin)
Tässä ensimmäisessä harjoitteessa, Tero Karvisen järjestämällä opintojaksolla (Karvinen 2024), tavoitteena on siepata verkkoliikennettä sekä analysoida sitä.

Tämä tehtävä on tehty perusläppärillä (amd64) ja käyttöjärjestelmä on Ubuntu 22.04.

Tehtävänannossa vinkkinä annettiin mm. ngrep ja Wireshark. Valitsin ngrepin.

	$ sudo apt update
	$ sudo apt install ngrep
 	$ man ngrep

Manuaalisivujen sisältö oli sen verran hankala ymmärtää, että tarvitsin selkokielisempää opastusta.

[Tällä](https://netbeez.net/blog/linux-analyze-network-ngrep/) (Vouzis 2020) sivulla on nopeasti selitetty ngrepin käyttö.

1. Kokeillaan ohjelmaa. Ngrep vaatii sudo-oikeudet.

![Screenshot from 2024-10-25 10-26-01](https://github.com/user-attachments/assets/b7029107-bb6b-473d-8377-dd8d00814eef)

Mitään ei tulostu, koska icmp-paketteja ei liiku.

2. Käynnistetään toinen terminaali ja pingataan verkko-osoitetta (google.com).

![Screenshot from 2024-10-25 10-32-08](https://github.com/user-attachments/assets/0b757118-88ff-4c62-bd93-57a63d23f0c8)

Nyt tulostuu dataa. Icmp-paketit eivät sisällä hyötykuormaa (payload), joten oleellinen tieto on ip-osoitteiden 10.213.106.113 ja 142.250.74.14 välillä. Icmp-paketti lähetetään ja sen vastaanottamisesta saadaan vastaus.

## Lähteet
Karvinen, T. 2024. Tunkeutumistestaus. [https://terokarvinen.com/tunkeutumistestaus/](https://terokarvinen.com/tunkeutumistestaus/). Luettu: 2024-10-25

Vouzis, P. 2020. How to Analyze Network Packets with ngrep. [https://netbeez.net/blog/linux-analyze-network-ngrep/](https://netbeez.net/blog/linux-analyze-network-ngrep/). Luettu: 2024-10-25
