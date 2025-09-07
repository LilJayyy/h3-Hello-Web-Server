# h3-Hello-Web-Server
 
## Sisältö
* [x) Artikkeli](#x-artikkeli)
* [a) Apache-weppipalvelimen testaus](#a-apache-weppipalvelimen-testaus)
* [b) Lokien analysointi](#b-lokien-analysointi)
* [c) Etusivun uusiminen - uusi name based virtual host](#c-etusivun-uusiminen---uusi-name-based-virtual-host)


### Koneen tekniset tiedot
* Prosessori: Intel Core i5-8265U CPU @ 1.60 GHz (1.80 GHz turbo, 8 ydintä)
* RAM: 16 GB (15,7 GB käytettävissä)
* Järjestelmä: Windows 11 Pro 64-bittinen (x64-suoritin)
* Näytönohjain: Intel UHD Graphics 620
* Tallennustila: 237 GB, josta 158 GB vapaana
* DirectX-versio: DirectX 12


# x) Artikkeli


# a) Apache-weppipalvelimen testaus

Apache-webpalvelin oli keretty asentaa oppitunnilla. Siirryin 5.9.2025 kello 19.52 tehtävän ensimmäiseen vaiheeseen eli testaamaan että webpalvein vastaa localhost-osoitteesta.

* Avasin virtuaalikoneen ja seuraavaksi internet-selaimen
* syötin osoitekenttään: http://localhost
  - Apache-webpavelin vastasi localhost osoitteesta ja käyttää oletuksena porttia 80.

![test](images/test.png)

_Onnistunut testaus_ 


# b) Lokien analysointi
Lokien analysoinnissa hyödynsin Heinosen (2025), Sumo logicin, sekä stack overflown ohjeita. Etenin seuraavasti:

Suoritin alla olevan komennon Terminalissa:
* `sudo tail -f /var/log/apache2/access.log` komentorivi Terminaliin
  
![loki](images/loki.png)

 **Lokiriveissä havaitsin seuraavat asiat**

* **127.0.0.1** - IP-osoite (Client IP)
  
* **[05/Sep/2025:20:52:18 +0300]** - Päivämäärä ja kllonaika, jolloin pyyntö vastaanotettu
  
*  **GET /favicon.ico HTTP/1.1** - Pyynnön tyyppi(GET), kohde (/favicon.ico) ja HTTP versio (1.1.)
  
*  **404** - HTTP-statuskoodi - palvelin ei löytänyt pyydettyä resurssia (mutta en hätääntynyt, sllä sivu latautui normaalisti auki)
  
*  **488** - Objektin koko joka palautui asiakkaalle / Object size returned to client
  
*  **Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0** - loppukäyttäjän(end user) käyttämä selaintieto


# c) Etusivun uusiminen - uusi name based virtual host 

Käytin tässä tehävässä apuna Karvisen (2018), Heinosen (2025) ja Fritsch (2009) ohjeita.

1. Disabloidaan ensin vanha web-palvelin `kilppari.com` jonka kävin testimielessä tunnin jälkeen tekemässä:

- `sudo a2dissite kilppari.example.com`

![dis](images/dis.png)

2. Hakemiston/kansion luominen ensin kotihakemistoon ja web-sivun sisällön kirjoitus index.html tiedostoon micro-editorilla. 

- `mkdir -p hattu.example.com`
 
- `micro hattu.example.com/index.html`


![luo](images/luo.png)

3. Tässä kohtaa aloitin kirjoittamaan web-palvelimen/sivun sisältöä.

Oli tärkeää havaita mitä asiaa tein - sillä voi helposti mennä sekaisin luullen, että kirjoittaa Apache-konfiguraatiota tässä kohtaa.

 - micro hattu.example.com/index.html` -komennolla kirjoitetaan siis web-sivun sisältö, eli:

**<html>** -Juurielimentti, www-sivun aloittava tagi. Kertoo selaimelle sen olevan HTML-tiedosto.
**<head>** -Pääosa, headeri. Määritellään otsikko web-palvelimelle/sivulle.
**<title>** -Otsikko selaimen ikkunassa 
**<body>** -Sivurungon aloittava tagi
**<p>** -Kappaleen vaihto

![hat](images/hat.png)

- Lopuksi vielä ctrl + S ja ctrl + Q
  
4. Lähdin sitten Apachen konfiguraation pariin seuraavasti edeten:
   
- `cd /etc/apache2/sites-available/`
  
- `sudoedit /etc/apache2/sites-available/hattu.example.conf`

![ha](images/ha.png)


![kay](images/kay.png)

5. Enabloin vielä web-palvelimen seuraavasti edeten:

* `sudo a2ensite hattu.example.com`
 
* `sudo systemctl restart apache2`

6. Lopuksi vielä testasin avaamalla verkkoselaimen:
   
- `hattu.example.com`

- `curl hattu.example.com`

**Virhetilanne** 

![hh](images/hh.png)

- Kuten kuvassa ilmenee, **"curl: (6) Could not resolve host: curl http://hattu.example.com.**

- Kävin läpi Heinosen (2025) ohjeet uudelleen, jossa ilmenikin yksi kohta, jonka olin tunnilla unohtanut olevan tarpeen tehdä. Apache oli kuitenkin konfiguroitu ja oli aktiivisena.
 
- `/etc/hosts-tiedostoon piti käydä lisäämässä rivi (eli kohdistamaan hostname IP-osoitteeseen) alla olevan kuvan mukaisesti.

![et](images/et.png)


**Lopputulos**


Karvisen (2025) ohjeissa luki selkeästi "Vinkit" -osiossa kotisivu kielletty -(403 Forbidden) virhetilanteen korjaava komento. Toki tämä jäi huomaamatta - mutta sentään se tuli havaittua lopulta. 

![onn](images/onn.png)
_Onnistuminen_


# e) Validin HTML5 -sivun luominen

<html>
	<head>
	<title> Tervetuloa Hattulaan! </title>
	</head>
	<body>
		<h1>Hatussa on hyvä olla</h1>
		<p> Hattu pattu hattu pattu </p>
	</body>
</html>	


## Lähteet

Fritsch S. 2009. Verkkosivu. _Ubuntu manuals_ Luettavissa: https://manpages.ubuntu.com/manpages/questing/en/man8/a2ensite.8.html Luettu: 05.09.2025.

Linuxize. 2023. Artikkeli. _Chown Command in Linux (File Ownership)_ Luettavissa: https://linuxize.com/post/linux-chown-command/ Luettu 05.09.2025

Girvin, D. 2025. Artikkeli. _Understanding the Apache access log: how to view, locate, and analyze_ Luettavissa:  https://www.sumologic.com/blog/apache-access-log Luettu 05.09.2025.

The Apache Software Foundation. 2023. Artikkeli. _Name-based Virtual Host Support_ Luettavissa: https://httpd.apache.org/docs/2.4/vhosts/name-based.html Luettu: 

Heinonen, J. 2025. Artikkeli. _Apache2_ Luettavissa: https://github.com/johannaheinonen/johanna-test-repo/blob/main/linux-03092025.md Luettu: 03.09.2025.

Karvinen, T. 2018. Artikkeli. _Name Based Virtual Hosts on Apache – Multiple Websites to Single IP Address_ Luettavissa: https://terokarvinen.com/2018/04/10/name-based-virtual-hosts-on-apache-multiple-websites-to-single-ip-address/ Luettu: 05.09.2025.

Karvinen, T. 2025. Verkkosivu. _Linux-palvelimet_ Luettavissa: https://terokarvinen.com/linux-palvelimet/ Luettu 05.09.2025.

Stack overflow. 2022. _Understanding Apache's access log_ Luettavissa: https://stackoverflow.com/questions/9234699/understanding-apaches-access-log Luettu: 05.09.2025

Vahtera, P. 2018. Verkkosivu. _HTML5 ja Web-sivun rakenne_ Luettavissa: https://punomo.fi/html5-ja-web-sivun-rakenne/
Luettu: 05.09.2025.

http://sumologic.com/blog/apache-access-log
