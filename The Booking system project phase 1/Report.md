# Penetraatiotestausraportti

## Johdanto
Raportin tarkoituksena oli löytää haavoittuvuuksia The Booking system project -palvelimesta. Haavoittuvuuksia arvioitiin manuaalisilla testeillä sekä ZapProxy-työkalulla, jonka avulla luotu raportti löytyy liitteistä. Testausympäristönä toimii Virtual box, johon on asennettu Kali-käyttöjärjestelmä sekä Docker. Hyökkäykset toteutettiin ZapProxy-työkalulla sekä manuaalisilla toimilla ja lisäksi kokonaisuutta arvioitiin silmämääräisesti. Testaus rajattiin /register-päätepisteeseen ja aikataulu rajattiin 15.2.2025-21.2.2025 välille. 

## Yhteenveto

21.2.2025
Kriittisiä virheitä ei löytynyt. Kuitenkin kuka tahansa onnistui rekisteröitymään adminina, mikä aiheuttaa tietoturvakysymyksiä. Admin-rekisteröityminen ei välttämättä ole haluttu toiminnallisuus asiakkaalla, ja sen voisi poistaa. Sähköpostin validointia voisi parantaa tarkastamalla specifimmin osoitteen. Asiakkaan user-agent arvolla ei ole merkitystä palvelimelle. Tarviiko olla edes?

17.2.2025
~~Merkittävimmät kolme löydöstä olivat salasanan salaamattomuus tietokannassa, syötteen validoimattomuus ja palvelinvirheiden vuotaminen asiakkaalle. Koska salasanoja ei salata, se altistaa ohjelmiston käyttäjätietojen väärinkäytölle. On erittäin suositeltavaa lisätä salausalgoritmit käsittelemään salasanoja. Koska syötteitä ei validoida, voi asiakkaalla tai palvelimella ilmetä odottamattomia ongelmia tai jopa tietovuotoja. Kehitystyössä tulisi kehittää näitä varten omat validointikäytännöt suojaamaan esimerkiksi XSS- tai SQL-injektiohyökkäyksiltä ja tietokantaan ei tulisi tallentaa epämääräisiä tietoja. Palvelinvirheitä varten tulisi kehittää poikkeamien hallintamekanismi, joka ei paljasta palvelinvirheitä asiakkaalle.~~

## Aktiiviset löydökset ja löydöksien kategorisointi
Löydökset on kategorisoitu tyypin mukaan ja värikoodattu seuraavalla tavalla:

$\color{red}{\textsf{Punainen (kriittinen):}}$ Poikkeamat ja haavoittuvuudet, jotka voivat johtaa merkittäviin tietoturvaloukkauksiin tai järjestelmän kompromettoitumiseen.

$\color{yellow}{\textsf{Keltainen (keskitaso):}}$ Haavoittuvuudet, jotka voivat aiheuttaa vakavia tietoturvaongelmia, mutta vaativat tiettyjä olosuhteita.

$\color{green}{\textsf{Vihreä (matala):}}$ Haavoittuvuudet, jotka aiheuttavat vähäisiä tietoturvaongelmia tai vaativat täsmällisiä olosuhteita.

### Syötteen validointi
#### $\color{green}{\textsf{11. Sähköpostiosoitteen validointi on heikko (matala) }}$
Ongelma: Sähköpostiosoite voi loppua epämääräisiin merkkeisiin.

Testi: Asettamalla sähköposti esimerkiksi "oar@gmail.com212112' ja rekisteröitymällä muutoin normaalisti, sähköpostiosoite hyväksytään ja tallennetaan tietokantaan

### Käyttäjätietojen validointi
#### $\color{yellow}{\textsf{11. Kuka tahansa voi rekisteröityä adminina (keskitaso) }}$
Ongelma: Kuka tahansa voi rekisteröityä adminina. 

Testi: Rekisteröityessä käyttäjä voi valita rooliksi adminin. Kun hän näin tekee rekisteröinti onnistuu normaalisti.

### Muut
#### $\color{green}{\textsf{11. User Agent Fuzzer (matala) }}$
Ongelma: User-Agent -arvojen vaihto ei vaikuta lopputulokseen. Jos ohjelmiston on tarkoitus toimia erilaisilla alustoilla ja sen takia saada erilaisessa muodossa vastauksia riippuen laitteistosta/sovelluksesta, voidaan joutua tekemään uusia ratkaisuja.

Testi: Vaihtamalla user-agent -otsakkeen arvoa, mitään muutosta ei tapahdu.

## Korjatut lyödökset ja löydöksien kategorisointi
Löydökset on kategorisoitu tyypin mukaan ja värikoodattu seuraavalla tavalla:

$\color{cyan}{\textsf{Syaani (korjattu):}}$ Korjatut haavoittuvuudet.

### Tietojen salaus 
#### $\color{cyan}{\textsf{1. Salasanaa ei ole salattu tietokantaan (kriittinen) (korjattu 21.2.2025)}}$
Ongelma: Salasanat on tallennettu tietokantaan ilman salausta. Tämä voi johtaa tietomurtoihin, käyttäjätilien kaappaukseen ja tietovuotoihin. Tämä rikkoo myös tietosuojakäytäntöjä kuten GDPR.

Testi: Suorittamalla kyselyn SELECT * FROM xyz123_users; voidaan lukea salasanat suoraan

### Syötteen validointi
#### $\color{cyan}{\textsf{2. Tietokantaan voi lisätä koodia (kriittinen) (korjattu 21.2.2025)}}$
Ongelma: Rekisteröinnin yhteydessä tietokantaan voi tallentaa koodia, joka voi olla haitallista. Tämä voi aiheuttaa vakavia ongelmia sivustolla.

Testi: Kun käyttäjänimeksi asettaa esimerkiksi "<script>alert('XSS')</script>" ja rekisteröityy, tietokantaan tallennetaan käyttäjä tällä käyttäjänimellä. Käyttäjänimeä ei validoida.

#### $\color{cyan}{\textsf{3. Path traversal (kriittinen) (korjattu 21.2.2025)}}$
Ongelma: Hyökkääjä voi manipuloida esimerkiksi username-tietoja niin, että se voi aiheuttaa odottamattomia tuloksia, kuten pääsyn muuhun järjestelmän sisältöön. Ei välttämättä ole tässä tapauksessa suuri riski, että näin voisi tapahtua.

Testi: Kun käyttäjänimeksi asettaa esimerkiksi "../../../../../../../../../../../../../../../../" ja rekisteröityy, tietokantaan tallennetaan käyttäjä tällä käyttäjänimellä. Käyttäjänimeä ei validoida.

#### $\color{cyan}{\textsf{4. Format string error (keskitaso) (korjattu 21.2.2025)}}$
Ongelma: Parametreja ei tarkisteta, joka voi aiheuttaa vakavia ongelmia palvelimella. Palvelin käsittelee syötteet komentona.

Testi: Kun username-parametriksi asetetaan esimerkiksi "ZAP%25n%25s%25n%25s%25n%25s%25n%25s%25n%25s%25n%25s%25n%25s%25n%25s%25n%25s%25n%25s%25n%25s%25n%25s%25n%25s%25n%25s%25n%25s%25n%25s%25n%25s%25n%25s%25n%25s%25n%25s%0A", saadaan "internal server error"-virhe.

#### $\color{cyan}{\textsf{5. SQL injection (kriittinen) (korjattu 21.2.2025)}}$
Ongelma: SQL injektointi voi olla mahdollista, sillä syötettyjä parametrejä ei valitoida. Tietokantaan voi näin ollen päästä mahdollisesti haitallisia arvoja, jotka voivat myöhemmin aiheuttaa odottamattomia ongelmia.

Testi: Kun käyttäjänimeksi asettaa esimerkiksi "ssa' AND '1'='1' -- " ja rekisteröityy, tietokantaan tallennetaan käyttäjä tällä käyttäjänimellä. Käyttäjänimeä ei validoida.

### Virheenkäsittely ja lokitus
#### $\color{cyan}{\textsf{6. Saman käyttäjänimen rekisteröinti kahdesti aiheuttaa virheen (keskitaso) (korjattu 21.2.2025)}}$
Ongelma: Saman käyttäjänimen rekisteröinti kahdesti aiheuttaa palvelinvirheen, mikä indikoi siitä että palvelin ei tarkista onko käyttäjä jo olemassa. Palvelinvirhe ei välttämättä ole haluttu virhe, sillä se saattaa paljastaa palvelimen vikoja. Statuskoodi voisi olla joko 409 tai muu riippuen halutusta lopputuloksesta. 

Testi: Kun käyttäjänimeksi asettaa esimerkiksi "mauri", rekisteröityy ja rekisteröityy uudelleen samalla nimellä, palvelin palauttaa virheviestin "Error during registration" statuskoodilla 500.

### Muut
#### $\color{cyan}{\textsf{7. CSP Header not set (keskitaso) (korjattu 21.2.2025)}}$
Ongelma: CSP-otsaketta ei ole asetettu. Tämä voi mahdollistaa esimerkiksi XSS-hyökkäykset, kun sisällön lähteitä ei rajata.

Testi: Lähettäessä HTTP-pyyntö, otsaketta ei saada vastauksessa.
#### $\color{cyan}{\textsf{8. Missing anti-clickjacking header (keskitaso) (korjattu 21.2.2025)}}$
Ongelma: Vastaus ei suojaa Clickjacking-hyökkäyksiltä. Sen tulisi sisältää otsakkeet Content-Security-Policy ja frame-ancestors -direktiivi tai X-Frame-options.

Testi: Lähettäessä HTTP-pyyntö, otsaketta ei saada vastauksessa.

#### $\color{cyan}{\textsf{9. Application error disclosure (matala) (korjattu 21.2.2025)}}$
Ongelma: Palvelin palauttaa virheviestin, joka voi sisältää sensitiivistä tietoa palvelimen toiminnasta. Statuskoodi 500 tarkoittaa palvelin virhettä (internal server error) ja sitä ei välttämättä haluta näyttää asiakkaille.

Testi: Asettaessa rekisteröinnin yhteydessä rooliksi admin ja lähetettäessä lomake, saadaan virhe viesti ja koodi "Error during registration", 500.

#### $\color{cyan}{\textsf{10. X-Content-Type-Options Header Missing (matala) (korjattu 21.2.2025)}}$
Ongelma: X-Content-Type-Options -otsake puuttuu. Tämä voi altistaa MIME-sniffing -tarkistuksille. Selain siis saattaa yrittää automaattisesti arvata vastauksen sisällön tyypin, vaikka palvelin on määrittänyt sen, jolloin selain voi tulkita sisältöä väärin. Joissakin tapauksissa tämä voi aiheuttaa turvallisuusriskejä.

Testi: Kun käyttäjä tekee HTTP-pyynnön, palvelimen vastauksessa ei ole määritelty X-Content-Options -otsaketta 'nosniff' -tilaan.

## Liitteet
[ZapProxy raportti - 17-2-2025 Zap Report.md] https://github.com/liskomes/musical-waffle/blob/main/The%20Booking%20system%20project%20phase%201/17-2-2025%20Zap%20Report.md

[ZapProxy raportti - 21-2-2025 Zap Report.md] https://github.com/liskomes/musical-waffle/blob/main/The%20Booking%20system%20project%20phase%201/21-2-2025%20Zap%20Report.md
