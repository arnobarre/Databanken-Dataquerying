# BASIS
#### Opgave 1: Maak een lijst met alle teams waarvan speler 27 de aanvoerder is.

```sql
SELECT * 
FROM teams 
WHERE spelersnr = 27;
``````



#### Opgave 2: Maak een lijst met alle mannelijke spelers.

```sql
SELECT spelersnr, naam, jaartoe 
FROM spelers 
Where geslacht LIKE 'M'
``````



#### Opgave 3: Geef een lijst met alle boetes kleiner dan 75 euro.
Zorg dat de oudste boete bovenaan staat.

```sql
SELECT datum, bedrag
FROM boetes
WHERE bedrag < 75
ORDER BY datum
``````



#### Opgave 4: Geef een lijst van wedstrijden die gespeeld zijn door team 1 en waarbij ofwel het aantal gewonnen of het aantal verloren sets gelijk is aan nul.

```sql
SELECT wedstrijdnr, teamnr
FROM wedstrijden
WHERE teamnr = 1 
AND gewonnen = 0 
OR verloren = 0
``````



#### Opgave 5: Schrijf de query die het aantal rijen in boetes telt.

```sql
select count(boetes)
from boetes
``````



#### Opgave 6: Schrijf de query die het aantal gespeelde wedstrijden telt.

```sql
select count(wedstrijden)
from wedstrijden
``````



#### Opgave 7: Schrijf de query die het aantal teams in de tweede divisie toont.

```sql
SELECT COUNT(DIVISIE)
FROM TEAMS
WHERE DIVISIE LIKE 'tweede'
``````



#### Opgave 8: Geef een lijst van de spelers hun postcodes.

```sql
SELECT postcode
FROM spelers
``````



#### Opgave 9: Schrijf de query die je het hoogst genoteerde boetebedrag geeft

```sql
select MAX(bedrag)
FROM boetes
``````



#### Opgave 10: Schrijf de query die je het jaar geeft waarop het eerste lid toegetreden is.

```sql
SELECT min(jaartoe)
FROM spelers
``````



__________________________________________________________________________________________________________________
# BASIS 2
#### Opgave 1: Maak een lijst van alle wedstrijden, die gespeeld werden voor het tweede team (teamnr: 2) en die gewonnen zijn door een speler van onze club (let op de benamingen van de kolomkoppen).

```sql
select wedstrijdnr, spelersnr, gewonnen as gewonnen_sets, verloren as verloren_sets
from wedstrijden
where teamnr = 2 
and gewonnen > verloren and exists
(select spelersnr from spelers);
``````



#### Opgave 2: Maak een overzicht van alle wedstrijden van speler met nummer 6 waarbij je berekent met welk aantal sets verschil hij de wedstrijd gewonnen of verloren heeft.

```sql
SELECT spelersnr, gewonnen, verloren, gewonnen-verloren as verschil
FROM wedstrijden
WHERE spelersnr = 6
``````



#### Opgave 3: Maak een lijst van alle actieve bestuursleden. Een actief bestuurslid is een bestuurslid met een lege eind_datum. Sorteer op begin_datum oplopend.

```sql
SELECT spelersnr, functie
FROM bestuursleden
WHERE eind_datum is null
ORDER BY begin_datum
``````



#### Opgave 4: Maak een lijst met de wedstrijden van speler nummer 6 waarbij je aangeeft of de wedstrijd gewonnen (toon: 'gewonnen') of verloren (toon: verloren) werd door de betreffende speler.
```sql
select spelersnr, wedstrijdnr, gewonnen, verloren, (CASE when gewonnen-verloren < 0 THEN 'verloren' ELSE 'gewonnen' END) as resultaat
from wedstrijden
where spelersnr = 6
``````



#### Opgave 5: Maak een lijst van alle boetes met een boetebedrag boven 75 euro die betaald werden in 1980, 1981, 1982 of 1983.
#### Let op: 
#### 1) de titels van de kolommen moeten gebruikersvriendelijk zijn en geen afkortingen bevatten (spelersnr wordt speler, jaar van de boete verschijnt als jaar en het bedrag wordt weergegeven als bedrag).
#### 2) Geef geen date als jaar terug, maar een int.

```sql
select spelersnr as speler, EXTRACT (YEAR from datum) as jaar, bedrag
from boetes
where bedrag > 75 and ((EXTRACT (YEAR from datum) = 1980) 
or (EXTRACT (YEAR from datum) = 1981) 
or (EXTRACT (YEAR from datum) = 1982) 
or (EXTRACT (YEAR from datum) = 1983))
``````



#### Opgave 6: Maak een lijst van alle vrouwelijke aanvoerders van een team. Hierbij toon je voor deze spelers het spelersnummer en de volledige naam.

```sql
SELECT teams.spelersnr, naam, voorletters
FROM teams, spelers
WHERE geslacht = 'V' 
AND spelers.spelersnr = teams.spelersnr
``````



#### Opgave 7: Geef een lijst met het spelersnummer, de naam van de speler, de datum van de boete van al de spelers die in 1980 een boete gekregen hebben en in Rijswijk wonen. Zorg voor de juiste opmaak van de datum in het resultaat (niet YYYY-MM-DD maar DD/MM/YYYY) waarbij je de to_char functie gebruikt. Sorteer op spelersnr.

```sql
select spelers.spelersnr, voorletters, naam, to_char(datum, 'DD/MM/YYYY') as boetedatum
from spelers, boetes
where plaats ilike 'Rijswijk' 
and (EXTRACT (year from datum)) = 1980 
and boetes.spelersnr = spelers.spelersnr
order by spelersnr
``````



#### Opgave 8: Maak een lijst van alle spelers die al een wedstrijd gespeeld hebben. Alleen spelers met een 'e' (hoofdletter of kleine letter) in de naam moeten getoond worden. Zorg dat deze laatste voorwaarde met één WHERE-conditie gerealiseerd wordt. Let ook op de juiste kolomhoofdingen. Sorteer op spelersnr en zorg dat elke speler maar één keer voorkomt. Gebruik geen concat, maar wel ||.

```sql
select distinct spelers.spelersnr, (spelers.voorletters ||' ' || spelers.naam) as spelersnaam
from spelers, wedstrijden
where naam ilike '%E%' 
and spelers.spelersnr = wedstrijden.spelersnr
order by spelersnr
``````



#### Opgave 9: Geef alle boetes groter dan 25 euro voor aanvoerders die meegespeeld hebben in een winnende wedstrijd met het team waarvan ze aanvoerder zijn. Sorteer op datum van de boete (de tekstuele waarde, niet de echte datum).

```sql
select distinct boetes.spelersnr, boetes.betalingsnr, to_char(boetes.datum, 'DD/MM/YYYY') as boetedatum
from boetes, teams, wedstrijden
where (bedrag>25)
and (wedstrijden.spelersnr = teams.spelersnr)
and (wedstrijden.gewonnen>wedstrijden.verloren)
and (wedstrijden.spelersnr = boetes.spelersnr)
and(teams.teamnr = wedstrijden.teamnr)
order by boetedatum
``````



#### Opgave 10: Geef een overzicht van alle divisies waar wedstrijden voor gespeeld zijn met spelers die actief bestuurslid zijn en minstens één boete hebben. Zorg dat elke divisie maar één keer voorkomt en sorteer de lijst alfabetisch.

```sql
SELECT distinct divisie
FROM teams AS t, bestuursleden AS bl, boetes AS b, wedstrijden AS w
WHERE bl.eind_datum IS NULL
AND b.betalingsnr IS NOT NULL
AND w.spelersnr = bl.spelersnr
AND t.teamnr = w.teamnr
AND w.spelersnr = b.spelersnr
ORDER BY t.divisie ASC
``````



__________________________________________________________________________________________________________________
# JOINS
#### Opgave 1: Geef een lijst met het spelersnummer, de naam van de speler, de datum van de boete en het bedrag van de boete van al de spelers die een boete gekregen hebben met een bedrag groter dan 45,50 euro en in Rijswijk wonen.
Sorteer op spelersnr en het volgnummer van de boete.
Gebruik expliciete joins.

```sql
select S.spelersnr, naam, datum, bedrag
from 
	boetes as B 
inner join spelers as S ON S.spelersnr = B.spelersnr
where bedrag > 45.50 
and S.plaats = 'Rijswijk'
order by S.spelersnr
``````



#### Opgave 2: Geef het spelersnummer, het jaar van toetreden en bondsnummer van alle spelers en de teams waarvoor ze kapitein zijn. Ook spelers die geen teamkapitein zijn, moeten getoond worden.
Sorteer op spelersnr aflopend.

```sql
SELECT S.spelersnr, S.jaartoe, S.bondsnr, T.teamnr
FROM 
	spelers S
LEFT OUTER JOIN teams T
ON S.spelersnr = T.spelersnr
ORDER BY S.spelersnr DESC
``````



#### Opgave 3: Geef voor alle spelers die bestuurslid zijn (of geweest zijn) een lijst van de wedstrijdnummers van alle wedstrijden die ze gespeeld hebben, en het verschil in sets waarmee ze gewonnen hebben. Bestuursleden zonder wedstrijd moeten ook in het resultaat voorkomen. Sorteer op bestuurslidfunctie (oplopend), verschil (aflopend), spelersnr (oplopend) en wedstrijdnr (oplopend)

```sql
select s.spelersnr, naam, functie, wedstrijdnr, gewonnen - verloren as verschil
from 
	spelers s 
inner join bestuursleden
	using(spelersnr)
	

left outer join wedstrijden w
	using(spelersnr)
	
order by functie asc, verschil desc, spelersnr asc, wedstrijdnr asc
``````



#### Opgave 4: Geef voor alle vrouwelijke spelers die in Den Haag, Zoetermeer of Leiden wonen het spelersnummer, hun woonplaats en een lijst van de teams waarvoor ze ooit gespeeld hebben.  Sorteer op spelersnr

```sql
select s.spelersnr, s.plaats, w.teamnr
from spelers s left outer join wedstrijden w
	on s.spelersnr = w.spelersnr
where geslacht = 'V' 
and (plaats = 'Den Haag' 
or plaats = 'Zoetermeer' 
or plaats = 'Leiden')
order by spelersnr
``````



#### Opgave 5: Geef voor elke wedstrijd het wedstrijdnummer en de volledige naam van de aanvoerder van het team waarvoor de wedstrijd werd gespeeld. Sorteer je resultaat volgens het wedstrijdnummer in oplopende volgorde.

```sql
select wedstrijdnr, naam, voorletters
from
	teams AS t
inner join wedstrijden AS w ON t.teamnr = w.teamnr
inner join spelers AS s ON t.spelersnr = s.spelersnr
order by wedstrijdnr asc
``````



#### Opgave 6: Geef voor elke mannelijke speler wiens naam minstens 2 keer de letter 'e' bevat een lijst van de functies die hij op dit moment uitoefent. Ook mannelijke spelers zonder huidige functie moeten getoond worden. Sorteer op spelersnr.

```sql
SELECT s.naam, s.geslacht, bs.functie
FROM 
	spelers AS s
LEFT OUTER JOIN bestuursleden AS bs ON bs.spelersnr = s.spelersnr AND bs.eind_datum IS NULL
WHERE s.naam ilike '%e%e%' 
AND s.geslacht = 'M'
ORDER BY s.spelersnr
``````



#### Opgave 7: Geef een alfabetisch gesorteerde lijst van de namen van alle leden van de tennisvereniging die ofwel een recreatiespeler zijn (speelt geen wedstrijden) ofwel een wedstrijdspeler die nog geen wedstrijden gespeeld heeft.

```sql
SELECT S.naam
FROM 
	spelers S 
LEFT OUTER JOIN wedstrijden W ON S.spelersnr = W.spelersnr
WHERE W.gewonnen is null 
AND W.verloren is null
ORDER BY S.naam
``````



#### Opgave 8: Geef voor alle huidige bestuurleden hun functie en de lijst van boetes die voor hen werd betaald. Omdat je dit wil vergelijken met de boetebedragen die betaald werden voor spelers die niet in het bestuur zitten, wil je deze boetebedragen ook opnemen in de tweede kolom van je resultaat. Sorteer je antwoord eerst op functie en daarna op het boetebedrag.

```sql
SELECT functie, bedrag
FROM 
	bestuursleden 
FULL OUTER JOIN boetes ON (bestuursleden.spelersnr = boetes.spelersnr AND eind_datum IS NULL)
ORDER BY functie, bedrag
``````



#### Opgave 9: Geef van elke speler wonend in Rijswijk het spelersnr, de naam, de lijst met boetebedragen (indien er zijn) en de lijst met teams waarvoor hij/zij een wedstrijd gespeeld heeft (indien hij er gespeeld heeft). Geef het resultaat volgens oplopend spelersnr, teamnr en bedrag

```sql
select s.spelersnr, s.naam, b.bedrag, w.teamnr
from
	spelers AS s
left outer join boetes AS b ON s.spelersnr = b.spelersnr
left outer join wedstrijden AS w ON s.spelersnr = w.spelersnr

where plaats = 'Rijswijk'
order by spelersnr asc, teamnr asc, bedrag asc
``````



#### Opgave 10: Geef een lijst van alle spelers die nog geen wedstrijd gespeeld hebben. Sorteer op spelersnr

```sql
SELECT spelers.spelersnr, naam
FROM 
	spelers 
LEFT OUTER JOIN wedstrijden ON spelers.spelersnr = wedstrijden.spelersnr
WHERE gewonnen IS NULL 
AND verloren IS NULL
ORDER BY spelersnr
``````



__________________________________________________________________________________________________________________
# JOINS 2
#### Opgave 1: Geef alle spelers die geen boete gekregen hebben en niet in Den Haag wonen. Sorteer op jaar van toetreden.

```sql
select spelers.spelersnr, naam, plaats
from 
	spelers 
left outer join boetes ON spelers.spelersnr = boetes.spelersnr
where plaats != 'Den Haag' 
and boetes.bedrag is null
order by jaartoe
``````



#### Opgave 2: Geef het spelersnummer en bondsnummer van alle spelers die jonger zijn dan de speler met bondsnummer 8467. Sorteer op spelersnr

```sql
select s1.spelersnr, s1.bondsnr
from spelers as s1, spelers as s2
where s2.bondsnr = '8467'
and s1.geb_datum > s2.geb_datum
``````



#### Opgave 3: Geef een lijst van alle huidige bestuursleden die nog geen boete gekregen hebben, maar wel al minstens één wedstrijd verloren hebben. Sorteer op spelersnr en wedstrijdnr.

```sql
SELECT S.spelersnr, naam, BS.functie, begin_datum, W.wedstrijdnr
FROM 
	bestuursleden BS 
JOIN spelers S ON BS.spelersnr = S.spelersnr
JOIN wedstrijden W ON BS.spelersnr = W.spelersnr
AND verloren > gewonnen
LEFT OUTER JOIN boetes B ON BS.spelersnr = B.spelersnr

WHERE B.bedrag IS NULL
AND eind_datum IS NULL
ORDER BY S.spelersnr, W.wedstrijdnr
``````



#### Opgave 4: Geef een lijst van alle mogelijke dubbelcombinaties tussen spelers onderling. Sorteer op "eerste speler" en vervolgens op "tweede speler".

```sql
select s1.voorletters || ' ' || s1.naam as "eerste speler", s2.voorletters || ' ' || s2.naam as "tweede speler"
from spelers AS s1
inner join spelers AS s2 ON s1.spelersnr <> s2.spelersnr
order by "eerste speler", "tweede speler"
``````



#### Opgave 5: Geef een lijst van alle mogelijke dubbelcombinaties van spelers die op dit moment niet in hetzelfde team spelen. Orden eerst naar speler1, dan naar speler2.

```sql
select	s1.voorletters || ' ' || s1.naam as speler1,
		s2.voorletters || ' ' || s2.naam as speler2
from 
	spelers s1
inner join spelers s2 on (s1.spelersnr != s2.spelersnr)
inner join teams te on (s1.spelersnr != s2.spelersnr and te.spelersnr != s1.spelersnr)
group by s1.spelersnr,s2.spelersnr
order by speler1, speler2
``````



__________________________________________________________________________________________________________________
# BASIS RUIMTE
#### Opgave 1: Geef de naam (= voornaam familienaam) van alle klanten die ooit al hebben deelgenomen aan een reis. Sorteer alfabetisch en zorg dat elke persoon slechts 1 maal voorkomt.

```sql
SELECT DISTINCT k.vnaam ||' '|| k.naam AS "naam"
FROM klanten k, deelnames d
WHERE k.klantnr = d.klantnr 
AND d.reisnr IS NOT NULL
ORDER BY naam
``````



#### Opgave 2: Geef voor klant met klantnr 121 het aantal reizen dat hij heeft gemaakt.

```sql
select count(reisnr) AS aantal_reizen
from 
	klanten AS k 
inner join deelnames AS d ON k.klantnr = d.klantnr
where d.klantnr = 121
``````



#### Opgave 3: Geef voor klant met klantnr 121 het aantal hemelobjecten dat hij effectief heeft bezocht over al zijn reizen heen. Hint: als hij de maan tweemaal bezocht heeft, tel je die dubbel.

```sql
select count(objectnaam) as aantal_bezoeken
from deelnames as d inner join bezoeken as b on d.reisnr = b.reisnr
where klantnr = 121
``````



#### Opgave 4: Geef voor klant met klantnr 121 het aantal unieke hemelobjecten dat hij effectief heeft bezocht over al zijn reizen heen.
```sql
select count (distinct (h.objectnaam)) as aantal_bezoeken
from reizen AS r, bezoeken AS b, deelnames AS d, hemelobjecten AS h
where d.klantnr = 121 
and d.reisnr = r.reisnr 
and b.reisnr = r.reisnr 
and h.objectnaam = b.objectnaam
``````



#### Opgave 5: Geef het bedrag van de duurste reis als "duurste"
```sql
SELECT max(prijs) AS duurste
FROM reizen
``````



#### Opgave 6: Geef van alle reizen die langer dan 400 dagen duurden, een lijst van deelnemers. Output is reisnr, klantnr. Sorteer eerst op reisduur en dan op klantnr

```sql
select d.reisnr, d.klantnr
from deelnames AS d
inner join reizen AS r ON d.reisnr = r.reisnr and reisduur > 400
``````



#### Opgave 7: Geef de totale verblijfsduur van alle bezoeken samen aan Mars

```sql
SELECT SUM(verblijfsduur) AS sum 
FROM bezoeken 
WHERE objectnaam = 'Mars';
``````



#### Opgave 8: Geef het aantal satellieten van Jupiter

```sql
SELECT  COUNT(objectnaam)
FROM hemelobjecten
WHERE satellietvan = 'Jupiter'
``````



#### Opgave 9: Geef het aantal satellieten van Jupiter en Saturnus samen

```sql
SELECT  COUNT(objectnaam)
FROM hemelobjecten
WHERE satellietvan = 'Jupiter' 
OR satellietvan = 'Saturnus'
``````



#### Opgave 10: Geef de totale som van alle afstanden tot hun planeet van alle satellieten van Saturnus

```sql
SELECT SUM(afstand) AS "totale afstand"
FROM hemelobjecten
WHERE satellietvan = 'Saturnus'
``````



__________________________________________________________________________________________________________________
# RUIMTE 1
#### Opgave 1: Welke reizigers hebben al meer dan 1 reis ondernomen waarvoor ze meer dan 2,5 miljoen euro moesten betalen? Sorteer op naam

```sql
select k.naam, Count(r.reisnr) AS aantal_reizen
from 
	deelnames AS d
inner join klanten AS k ON d.klantnr = k.klantnr
inner join reizen AS r ON d.reisnr = r.reisnr and prijs > 2.5

group by naam
Having count(r.reisnr) > 1
order by naam
``````



#### Opgave 2: Bereken voor de klant wiens naam begint met G en eindigt met s hoeveel hij in totaal al besteed heeft aan reizen.

```sql
select k.naam, SUM(r.prijs) AS sum
from 
	klanten AS k
inner join deelnames AS d ON k.klantnr = d.klantnr
inner join reizen AS r ON d.reisnr = r.reisnr

where k.naam ILIKE 'g%s'
group by k.naam
``````



#### Opgave 3: Maak een lijst met een overzicht van de reizen en het aantal deelnemers van elke reis. Orden de lijst dalend op basis van het aantal deelnemers, als er eenzelfde aantal deelnemers is, moeten deze stijgend geordend worden volgens reisnummer.

```sql
select r.reisnr, count(d.klantnr) AS deelnemers
from 
	deelnames AS d
inner join reizen AS r ON d.reisnr = r.reisnr
inner join klanten AS k ON d.klantnr = k.klantnr
group by r.reisnr
order by deelnemers desc, reisnr asc
``````



#### Opgave 4: Welke reizen hebben exact drie verschillende hemelobjecten als reisdoel? Sorteer op reisnr.

```sql
XXX
``````



#### Opgave 5: Maak een lijst met een overzicht van de reizen en het aantal deelnemers van elke reis. Orden de lijst dalend op basis van het aantal deelnemers, als er eenzelfde aantal deelnemers is, moeten deze stijgend geordend worden volgens reisnummer. Zorg dat reizen zonder deelnames ook in het resultaat staan.

```sql
select r.reisnr, count(k.naam) AS deelnemers
from
	klanten AS k
right outer join deelnames AS d using(klantnr)
right outer join reizen AS r using (reisnr)
group by r.reisnr
order by deelnemers desc, r.reisnr asc
``````



#### Opgave 6: Op welke planeten verblijft men gemiddeld langer dan 2 dagen? Sorteer op objectnaam.

```sql
XXX
``````



#### Opgave 7: Welke planeten met meer dan 7 manen worden bezocht (met of zonder verblijf)? Sorteer aflopend op basis van het aantal manen. Let erop dat je planeten die meerdere keren bezocht worden, niet dubbel telt.

```sql
XXX
``````



#### Opgave 8: Bereken voor alle hemelobjecten die satellieten hebben, het aantal satellieten per hemelobject. De lijst moet dalend gesorteerd worden op basis van het aantal satellieten van de hemelobjecten en daarna alfabetisch op basis van objectnaam.

```sql
XXX
``````



__________________________________________________________________________________________________________________
## RUIMTE 2
#### Opgave 1: Geef de reizen met meer dan drie klanten. Sorteer op reisnr.

```sql
XXX
``````



#### Opgave 2: Geef alle planeten (gesorteerd op afstand van de zon) en het aantal verschillende reizen die deze planeet bezocht hebben.

```sql
XXX
``````



#### Opgave 3: Geef per klant het totaal aantal dagen dat deze klant in totaal op een planeet zal verblijven. Sorteer op klantnr.

```sql
XXX
``````



#### Opgave 4: Sorteer de klanten aflopend op gemiddelde kostprijs per bezoek (totaalprijs van alle reizen per klant/totaal aantal dagen verblijf op een hemelobject), op twee cijfers na de komma afgerond, daarna op klantnr.

```sql
XXX
``````



#### Opgave 5: Geef de planeten en het aantal verschillende personen die hen bezocht hebben (met of zonder verblijf). Sorteer op objectnaam.

```sql
XXX
``````



__________________________________________________________________________________________________________________
# BASIS EXTRA



__________________________________________________________________________________________________________________
# BASIS AgMT



__________________________________________________________________________________________________________________
# EXTRA OEFENING
#### Opgave 1: Geef een lijst van alle planeten die minstens 1 satelliet hebben op een afstand groter dan 500km. Voor deze planeten toon je het aantal dergelijke satellieten. Geef ook een lijst van de andere planeten. Bij hen zet je als output: "Geen". Sorteer omgekeerd alfabetisch op planeetnaam.

```sql
select	planeet.objectnaam, 
		case 
			when count(sat.objectnaam) = 0 THEN 'Geen' 
			else to_char(count(sat.objectnaam), '999') 
		end AS "ver verwijderde satellieten"
from 
	hemelobjecten AS planeet 
INNER JOIN hemelobjecten ster ON (planeet.satellietvan = ster.objectnaam AND ster.satellietvan is null)
LEFT OUTER JOIN hemelobjecten sat ON (sat.satellietvan = planeet.objectnaam AND sat.afstand > 500)
GROUP BY planeet.objectnaam
ORDER BY planeet.objectnaam desc
``````



__________________________________________________________________________________________________________________
