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



## Lähteet
Karvinen, T. 2024. Tunkeutumistestaus. [https://terokarvinen.com/tunkeutumistestaus/](https://terokarvinen.com/tunkeutumistestaus/). Luettu: 2024-10-25

Vouzis, P. 2020. How to Analyze Network Packets with ngrep. [https://netbeez.net/blog/linux-analyze-network-ngrep/](https://netbeez.net/blog/linux-analyze-network-ngrep/). Luettu: 2024-10-25
