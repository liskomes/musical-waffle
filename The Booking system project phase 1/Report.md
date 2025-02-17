# Penetraatiotestausraportti

## Johdanto

## Yhteenveto

## Lyödökset ja löydöksien kategoriointi

### $\color{red}{\textsf{1. Salasanaa ei ole salattu tietokantaan}}$
Ongelma: Salasanat on tallennettu tietokantaan ilman salausta. Tämä voi johtaa tietomurtoihin, käyttäjätilien kaappaukseen ja tietovuotoihin. Tämä rikkoo myös tietosuojakäytäntöjä kuten GDPR.

Testi: Suorittamalla kyselyn SELECT * FROM xyz123_users; voidaan lukea salasanat suoraan

### $\color{red}{\textsf{2. Tietokantaan voi lisätä HTML-syntaksia}}$
Ongelma: Rekisteröinnin yhteydessä tietokantaan voi tallentaa koodia, joka voi olla haitallista. Tämä voi aiheuttaa vakavia ongelmia sivustolla.

Testi: Kun käyttäjänimeksi asettaa esimerkiksi "<script>alert('XSS')</script>" ja rekisteröityy, tietokantaan tallennetaan käyttäjä tällä käyttäjänimellä. Käyttäjänimeä ei validoida.

## Liitteet
