# 1 SQL injection - SQL injection vulnerability in WHERE clause allowing retrieval of hidden data 

Tehtävässä osa tuotteista oli piilotettu, ja yritin saada piilotetut tuotteet näkyviin. Asetin tehtävässä URL-osoitteen loppuosan Burp Suitessa “/filter?category=’+or+1=1--’”. SQL:ssä “--” on kommentin merkki. Kommentti mahdollisti osoitteen lopun jättämisen pois SQL-kyselystä. Se tarkoitti tehtävässä, että “..AND released = 1” jäivät pois SQL kyselystä, jolloin hakuehto SQL:ssä tuli vain alun mukaan, eli kysely palvelimella oli “SELECT * FROM product WHERE category = ‘’ OR 1=1--’ AND released = 1”. Koska 1=1 on aina totta ja valittuna on kaikki product-taulun tuotteet (*), palautetaan kaikki tuotteet.  

Opin, että toteutus palvelimella on huono sillä se ei validoi URL-parametrien arvoja vaan siirtää URL-osoitteen parametrien arvot suoraan SQL-kyselyyn. Vastaavia toteutuksia tulee välttää. Vaikeinta oli hahmottaa lopullinen kysely. 

# 2 SQL injection - SQL injection vulnerability allowing login bypass 

Tehtävässä tehtiin SQL-hyökkäys samaa logiikkaa noudattaen, kuin tehtävässä “SQL injection vulnerability in WHERE clause allowing retrieval of hidden data”. Palvelin palauttaa normaalisti käyttäjän, jolla username=-jotain- ja password=-jotain-. SQL kysely on tällöin suurin piirtein “SELECT * FROM users WHERE username = ‘jotain’ AND password=’jotain’. Tehtävässä username kenttään syötettiin “admistrator’--”. Tämä tarkoittaa, että kun SQL-kysely muodostetaan, kysely käsittelee kommenttina loppuosan eli salasanakentän, jolloin se on “SELECT * FROM users WHERE username=’administrator’ --’ AND password=’jotain’”. 

Opin, että toteutus palvelimella on huono sillä se ei validoi URL-parametrien arvoja vaan siirtää URL-osoitteen parametrien arvot suoraan SQL-kyselyyn. Vastaavia toteutuksia tulee välttää. Vaikeinta oli hahmottaa lopullinen kysely. Tehtävä oli melko samanlainen kuin ensimmäinen. 

# 3 Access control - Unprotected admin functionality 

Tehtävässä hyökättiin robots.txt-tiedoston kautta admin-paneeliin, jota ei ollut suojattu. URL-osoitteeseen lisättiin “../robots.txt”, jolloin saatiin tieto admin-paneelin osoitteen loppuosasta. Tämä loppuosa lisättiin URL-osoitteeseen ja päästiin käsiksi paneeliin. Tämän jälkeen voitiin poistaa käyttäjä.  

Opin, että huono (tai puutteellinen) kulunvalvonta (access control) voi aiheuttaa vakavia turvallisuusuhkia. Tehtävässä oli erityisen huono kulunvalvonta, joka mahdollisti helpon pääsyn admin-käyttäjän toimintoihin myös muilla käyttäjillä. Tällaisia toteutuksia tulisi välttää. Huono kulunvalvonta on yleinen turvallisuusheikkous. 

# 4 Access control - Unprotected admin functionality with unpredictable URL 

Tehtävässä hyökättiin huonosti toteutetun toiminnallisuuden kautta admin-paneeliin. Admin paneelin URL-osoitteen loppuosa löytyi suoraan javascript-koodin pätkästä, joka oli nähtävissä kehittäjätyökaluilla if-lausekkeessa “adminPanelTag.setAttribute(‘href’, ‘tässä loppuosa’);” 

Opin, että javascriptin ja muiden käyttäjille nähtäväksi jäävien tiedostojen, tekstien yms. kanssa tulee olla tarkka, etteivät ne mahdollista luvatonta pääsyä vääriin paikkoihin, esimerkiksi näyttämällä “salaisen” osoitteen. Vaikka “salaisia” osoitteita olisikin, tulisi niihin tehdä oikeaoppinen käyttäjän varmennus. Vaikeinta oli löytää url-osoitteen loppuosa.  

# 5 Authentication - Username enumeration via different responses 

Tehtävässä selvitettiin salasana ja käyttäjänimi käyttäjälle. Aluksi tehtiin indruder-hyökkäys sivulle, millä selvitettiin käyttäjänimi. Burp Suiten Intruder osioon siirrettiin aluksi login-pyyntö. Pyynnössä aluksi username-parametrin arvoa muutettiin sellaiseen muotoon, että se otti vastaan listan arvoja, jotka määriteltiin tehtävänannossa. Pyyntö lähetettiin tämän jälkeen, jokaisella username-arvolla, ja vastauksista poimittiin arvo, jossa vastauksen pituus (length) oli poikkeava. Tätä voitiin pitää oikeana käyttäjätunnuksena. Tämän jälkeen sama tehtiin salasanalle (tehtävänannon lista salasanoista otettiin käyttöön), mutta niin että oikea username-arvo oli käytössä. Kun vastaus palautti statuskoodin 302 (found), oli löytynyt oikea salasana ja käyttäjätunnus, jolloin voitiin kirjautua sisään. 

Tehtävä oli mielenkiintoinen. Opin että oikeilla työkaluilla voidaan helpottaa murtautumista järjestelmään toisen käyttäjätunnuksilla. Erityisesti oikeaa käyttäjätunnusta haettaessa vastauksen pituuden valjastaminen tietomurtokäyttöön oli yllättävää itselle. Myös tällaiset “pienet” asiat voivat helpottaa rikollista toimintaa. Vaikeinta oli hahmottaa, miten automatisoin hyökkäyksen. 

# 6 Authentication - 2FA simple bypass 

Tehtävässä oli huonosti toteutettu kaksivaiheinen tunnistautuminen. Kun käyttäjätunnus ja salasana olivat tiedossa, voitiin tiputtaa yksi pyyntö (login2, eli toinen vaihe) pois, jolloin palvelin salli tilille siirtymisen ilman toista tunnistusvaihetta eli sähköpostivarmennusta. Tilille siirryttiin osoiteriviä muuttamalla. 

Opin, että huonosti toteutettu kaksivaiheinen tunnistautuminen on melkein sama, kuin yksivaiheinen tunnistautuminen käytännön tasolla. Vaikeinta oli hahmottaa milloin osoitteen voi muuttaa.  

# 7 Access control - User role can be modified in user profile 

Tehtävässä muutettiin käyttäjän roolia. Tämä tapahtui, koska sähköpostinvaihtopyyntö hyväksyi muitakin arvoja kuin pelkästään sähköpostin json-bodyyn. Kun json-bodyyn lisäsi -”roleid”: 2- vaihtui sähköpostin ohella käyttäjän rooli adminiksi, joka mahdollisti admin-paneeliin pääsyn ja carlos-käyttäjän tuhoamisen.  

Opin, että huonosti toteutetussa palvelimen pyyntöjen käsittelyssä, voi esiintyä haavoittuvaisuuksia. Palvelimella tulisi validoida pyynnöissä tuleva data, ja tarvittaessa poistaa tai sivuuttaa kenttiä, joita ei tule päivittää tai evätä pyynnöt. Vaikein osuus oli oikean pyynnön löytäminen. 

# 8 Authentication - Password reset broken logic 

Tässä tehtävässä tarkoitus oli resetoida Carloksen salasana. Tämä tapahtui resetoimalla aluksi oma salasana ja käyttämällä tähän käytettyä pyyntölomaketta pohjana Carloksen salasanan vaihdolle. Tämä tapahtui vaihtamalla väliaikainen password-tokeni url-parametristä ja bodystä. Lisäksi username vaihdettiin Carloksen käyttäjätunnukseksi. Tämän jälkeen pyyntö lähetettiin ja Carloksen salasana oli vaihdettu. 

Opin, että palvelimen ohjelmoinnissa tulee kiinnittää huomiota myös, tokenin ja käyttäjätunnuksen yhteen linkittämiseen, samoin kuin myös sen kertakäyttöisyyteen. Huonosti toteutettuna tästä voi muuten seurata tietoturva-aukkoja. Vaikeinta oli huomata, että tokenia voi muuttaa. 

# 9 Access control - User role controlled by request parameter 

Tehtävässä muutettiin admin-keksin arvo trueksi. Tämä mahdollisti pääsyn admin-paneeliin. Admin-paneelista voitiin poistaa carlos-käyttäjä. Keksin arvo vaihdettiin kehittäjätyökaluilla (F12) navigoimalla Application->Cookies->https://.... ja muuttamalla Adminin arvo. Viittaus keksiin löytyi my-acccount-pyynnöstä, jonka avulla voitiin päätellä keksin olemassaolo. 

Tehtävässä opin, että keksien kanssa täytyy olla varovainen. Ensimmäinen ongelma tehtävän toteutuksessa oli, että asiakkaalla oli valta hallita roolia. Toinen ongelma oli, että Admin-keksi oli ylipäätään olemassa. Vaikeinta tehtävässä oli löytää keksi. 

# 10 Access control - User ID controlled by request parameter 

Tehtävänä oli selvittää Carloksen API-avain. Tehtävässä kirjauduttiin Carloksen tunnuksille muuttamalla url-osoitteen loppuosaa -> “../my-account?id=carlos”. Tämä antoi sivun, joka sisälsi API-avaimen, joka kuului Carlokselle. Avaimen avulla “varmennettiin” Carloksen käyttäjä. 

Tehtävä oli lyhyt. Opin, että joskus kehittäjillä voi lipsahtaa vähän lapsekkaitakin virheitä, jotka aiheuttavat suuria tietoturvaongelmia. Opin, että palvelimen toteuttamiseen tulee käyttää aikaa, jotta virheitä voidaan välttää. 

# 11 Access control - User ID controlled by request parameter, with unpredictable user IDs 

Tehtävänä oli selvittää Carloksen API-avain. Tehtävässä haettiin blogipostaus, jonka oli kirjoittanut Carlos. Kun tämä löytyi, klikattiin Carloksen nimeä, joka johti pyyntöön, missä oli Carloksen ID. Nyt kun ID tiedettiin, voitiin käyttää “../my-accound?id=<löytynyt id>”. Tämän jälkeen vastauksena tuli sivu, joka sisälsi API-avaimen. 

Opin tehtävästä, että käyttäjien id:n käsittelyssä tulee olla tarkka. Jos sen näyttäminen on pakollista, tulee se turvata toisella arvolla, ettei sitä voi käyttää suoraan. Vaikeinta oli löytää blogipostaus, jonka oli kirjoittanut Carlos. 

# 12 Access control - User ID controlled by request parameter with data leakage in redirect 

Tehtävänä oli selvittää Carloksen API-avain. Avain löytyi kirjautumalla aluksi sisään. Sisäänkirjautumisen jälkeen vaihdettiin pyynnön URL-osoitteen loppuosa “../my-account?id=carlos”. Tämä uudelleenohjasi käyttäjän kirjautumissivulle. Kuitenkin my-account-sivu kerkesi vuotaa API-avaimen Carlos-käyttäjältä ennen uudelleenohjausta, joka voitiin hakea html-tiedostosta.  

Opin tehtävästä, että uudelleenohjaus ei välttämättä ole paras tapa hoitaa tehtävässä tapahtuvaa tapahtumaa. Jos uudelleenohjaus tehdään, se tulisi suorittaa ennen kuin sivusto tai käyttäjätietoja näytetään. Uskoisin kuitenkin, että parempi tapa on usein tarkistaa ensin käyttäjätiedot ja lähettää sen mukaan sivusto tai data käyttäjälle. Tehtävä oli aika samanlainen kuin ylemmät, joten vaikeinta osuutta on hankala sanoa. 

# 13 Access control - User ID controlled by request parameter with password disclosure 

Tehtävänä oli kirjautua administratorina ja poistaa käyttäjä carlos. Tämä tapahtui muokkaamalla pyyntöä “my-account” niin että se oli “../my-account?id=administrator”. Kun pyyntö lähetettiin Burp Suitella, se palautti vastauksen, jonka html-tiedosto sisälsi kentän, jossa luki adminin salasana. Nyt kun tiedettiin adminin käyttäjä ja salasana, voitiin kirjautua sisään ja poistaa carlos admin-paneelin kautta. 

Opin tehtävässä, että salasanakentissä ei tulisi olla vanhaa salasanaa valmiiksi kirjoitettuna, sillä se voi lisätä tietovuotojen riskiä. Vaikka salasanakentät olisivatkin sellaisia, että ne piilottavat kirjaimet, voidaan salasana tarkistaa kehittäjätyökaluilla html-tiedostosta. Vaikeinta tehtävässä oli sisäänkirjautuminen admin-käyttäjällä. 

# 14 Access control - Insecure direct object references 

Tehtävässä haettiin vanha viestihistoria vaihtamalla url-osoitteen lopussa näkyvää tiedostoa. Kun url vaihdettiin “../download-transcript/1.txt” saatiin ladattua nykyiselle käyttäjälle kuulumaton viestihistorialla, jolla päästiin käsiksi carlosin salasanaan. Tämän avulla voitiin kirjautua Carlosin tunnuksilla sisään. 

Opin, että suorien tiedostoviittauksien kanssa tulee olla tarkkana palvelinpuolta kehittäessä. Jos palvelimeen ei implementoida tiedostojen käsittelyn tueksi turvallisia toimintamalleja, se saattaa johtaa vakaviin tietoturvaongelmiin. Tehtävästä tuli myös mieleen path traversal attack, jolla voisi kulkea kansiosta toiseen ja ladata alikansioiden tiedostoja. Vaikeinta oli keksiä oikea tiedostonimi. 

# 15 Path traversal - File path traversal, simple case 

Tehtävässä otettiin tarkkailuun pyyntö, jonka avulla ladattiin kuvat asiakkaalle. Pyyntöä muokattiin niin, että sen parametreista filename:n arvo muuttui muotoon “../../../../etc/passwd”. Tämän avulla päästiin hakemaan alihakemistoista etc-kansio ja sieltä passwd-tiedosto. 

Opin tehtävästä, että jos ja kun palvelimelle implementoidaan tehtävää vastaavaa toiminnallisuutta kuvien tai muiden tiedostojen hakemiseksi, tulee kiinnittää huomiota, ettei tehtävän kaltaisella hyökkäystavalla päässä käsiksi tiedostoihin, jotka eivät saisi olla saatavilla. 

# 16 Information disclosure - Information disclosure in error message 

Tehtävässä avattiin tuote. Tuotteen pyyntöä muokattiin niin, että se aiheutti virheen palvelimelle. Tässä tilanteessa muutin sitä muotoon “../product?productId=”hhh”. Virheilmoituksesta paljastui haavoittuvan frameworkin versionumero. 

Tehtävästä opin, että virheilmoitusten sisältö saattaa paljastaa tietoja, mitä kehittäjä tai palveluntarjoaja ei välttämättä halua käyttäjän tietävän. Virheilmoitusten sisältö saattaa paljastaa dokumentoitua tietoa työkalujen (kirjastot yms.) haavoittuvaisuuksista, jota rikolliset voivat käyttää hyväkseen. Virheilmoitukset saattavat myös paljastaa tietoa siitä, mitä palvelimella tapahtuu. 

# 17 Information disclosure - Information disclosure on debug page 

Tehtävässä tuli löytää SECRET_KEY arvo. Tehtävässä otettiin tarkasteluun etusivun palauttava pyyntö. Vastauksen html-dokumentista löytyi url-osoitteen loppuosa, jolla voitiin avata sivusto, jolta löytyi paljon kriittisiä tietoja, mukaan lukien SECRET_KEY. Voitiin esimerkiksi päätellä, että sivusto käytti php-teknologiaa 

Opin tehtävästä, että komponenttien (ja muutenkin tekstin) kommentointia kannattaa miettiä kahdesti etenkin kehitysvaiheessa, jos siellä on viitteitä kriittisestä tiedosta, kuten kirjastoista ja teknologioista, mitä käytetään. On järkevää siistiä koodi tarpeettomista ja liian informatiivisista kommenteista ennen ohjelman julkaisua. Vaikein osa oli löytää sivusto, jossa avain oli. 

# 18 Information disclosure - Source code disclosure via backup files 

Tehtävässä ovattiin robots.txt url-osoitteen kautta. Tiedosto paljasti, että projektiin oli olemassa backup-tiedosto. Kun backup-tiedosto avattiin url-osoitteesta (../backup), voitiin havaita, että se sisälsi jonkinlaisen koodin jolla luotiin yhteys tietokantaan. Koodista löytyi myös tehtävän vaatima avain. 

Opin tehtävästä, että ylipäätään kaikkien vanhojen ja lopulliseen tuotteeseen liittymättömien tiedostojen tulisi olla saavuttamattomissa käyttäjiltä, eikä niillä tulisi olla linkityksiä projektiin, ellei sitten esimerkiksi adminin tai vastaavan takana. On myös järkevää määrittää manuaalisesti robots.txt-tiedostoa vastaavat tiedostot. 

# Tehtävät yhteensä:

SQL injection: 2kpl
Path traversal: 1kpl
Authentication: 3kpl
Information disclosure: 3kpl
Access control: 9kpl
Yhteensä: 18kpl
