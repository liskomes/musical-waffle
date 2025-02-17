# Penetraatiotestausraportti

## Johdanto

## Yhteenveto

## Lyödökset ja löydöksien kategoriointi

### $\color{red}{\textsf{1. Salasanaa ei ole salattu tietokantaan}}$
Ongelma: Salasanat on tallennettu tietokantaan ilman salausta. Tämä voi johtaa tietomurtoihin, käyttäjätilien kaappaukseen ja tietovuotoihin. Tämä rikkoo myös tietosuojakäytäntöjä kuten GDPR.

Testi: Suorittamalla kyselyn SELECT * FROM xyz123_users; voidaan lukea salasanat suoraan

### $\color{red}{\textsf{2. Tietokantaan voi lisätä koodia}}$
Ongelma: Rekisteröinnin yhteydessä tietokantaan voi tallentaa koodia, joka voi olla haitallista. Tämä voi aiheuttaa vakavia ongelmia sivustolla.

Testi: Kun käyttäjänimeksi asettaa esimerkiksi "<script>alert('XSS')</script>" ja rekisteröityy, tietokantaan tallennetaan käyttäjä tällä käyttäjänimellä. Käyttäjänimeä ei validoida.

### $\color{red}{\textsf{3. Path traversal}}$
Ongelma: Hyökkääjä voi manipuloida esimerkiksi username-tietoja niin, että se voi aiheuttaa odottamattomia tuloksia, kuten pääsyn muuhun järjestelmän sisältöön. Ei välttämättä ole tässä tapauksessa suuri riski, että näin voisi tapahtua.

Testi: Kun käyttäjänimeksi asettaa esimerkiksi "../../../../../../../../../../../../../../../../" ja rekisteröityy, tietokantaan tallennetaan käyttäjä tällä käyttäjänimellä. Käyttäjänimeä ei validoida.

### $\color{red}{\textsf{3. SQL injection}}$
Ongelma: SQL injektointi voi olla mahdollista, sillä syötettyjä parametrejä ei valitoida. Tietokantaan voi näin ollen päästä mahdollisesti haitallisia arvoja, jotka voivat myöhemmin aiheuttaa odottamattomia ongelmia.

Testi: Kun käyttäjänimeksi asettaa esimerkiksi "ssa' AND '1'='1' -- " ja rekisteröityy, tietokantaan tallennetaan käyttäjä tällä käyttäjänimellä. Käyttäjänimeä ei validoida.

## Liitteet
