# Lösungen DBS 07.2015

Alle Lösungen ohne Gewähr!

## Aufgabe 1

a)

```
Zwei Partitionierungen (Mehrfache Spezialisierung):
Fahrzeug -> Landfahrzeug
Fahrzeug -> Wasserfahrzeug
(Pfeil gleiche Richtung wie im EER-Modell)
Durch doppelte Partitionierung kann Fahrezeug beides sein.
Partitionierung bedeutet hier das Fahrezeug Landfahrzeug oder Wasserfahrzeug
sein kann, aber nicht sein muss.
```
*Inhalt: ER-Modell*

b)

| Nr. | Antwort |  
|---|---|
|1.|falsch|  
|2.|wahr|    
|3.|wahr|  
|4.|wahr|  
|5.|keine Ahnung|  
|6.|wahr|  

*für 5. bitte Lösung schicken*

c)

Hier steht **fett** für Primärschlüssel  
*Kursiv* steht für Fremdschlüssel

Assistent(**Name**, Fachgebiet, *Professor.Name* [NOT NULL])  
Professor(**Name**, Lehrstuhl, *Raum* [UNIQUE, NOT NULL])  
Raum(**Raum-Nr**, Größe, *Geb-Nr* [NOT NULL])    
Gebäude(**Geb-Nr**, Anzahl-Etagen)  
**hat**(**Raum-Nr**, Geb-Nr)

nicht sicher ob man noch **hat** in Raum stecken sollte...  
[ arbeitet-für(**Assistent.Name**, Professor.Name) ]  <--- in Assistent  
[ hat-Büro(**Name**, Raum-Nr)  ] <--- in Professor

*bitte auf Korrektheit überprüfen  
habe mich an 2. Übung Aufgabe 4 orientiert  
Inhalt: 2.Übungsfolien und ERM2RM-Vorlesung  
SQL-DDL bei Vorlesung 3 Datenmodell  
Die Aufgabe ist richtig kacke omg*

d)

*bitte Lösung schreiben*

## Aufgabe 2

a)

```
Outlier ist definiert als Element des Datenbestands,  
das in bestimmter Hinsicht vom restlichen Datenbestand erheblich abweicht.
Objekt O, das in Datenbestand T enthalten ist,  
ist ein DB(p,D)-Outlier, wenn der Abstand von O zu mindestens p Prozent  
der Objekte in T größer ist als D.
```

b)

```
Der Algorithmus aus der Vorlesung ist die kNN-Suche.

k-nächsterNachbar(Baum, Anfrage, k)
  kListe ← Liste von k-Nachbarn
  WHILE (k > 0) DO
    nachbar ← nächsterNachbar(Baum,Anfrage)
    kListe.add(nachbar)
    Baum.remove(nachbar)
    k= k - 1
  END WHILE
  return kListe

nächsterNachbar(Baum, Anfrage)
  PQ ← new PQueue
  Region ← Wurzelknoten des kdB-Baums
  Distance ← Abstand(Region, Anfrage)
  Einfügen(PQ, Distance, Region)
  WHILE (true) DO
    e ← Head von PQ
    IF (e ist Datenpunkt) THEN
      BEGIN; Gib e zurück; RETURN; END
    ELSE
      Traversierung(e, PQ, Anfrage)
  END WHILE


Traversierung(Element, Queue, Anfrage)
IF (Element ist Blatt) THEN
  FOR EACH (Datenpunkt in Element) DO
    Distance ← Abstand(Datenpunkt, Anfrage)
    Einfügen(Queue, Distance, Datenpunkt)
  END FOR
ELSE
  FOR EACH (Kind von Element) DO
    Distance ← Abstand(Kind, Anfrage)
    Einfügen(Queue, Distance, Kind)
  END FOR
END IF

Einfügen(PQ, Priorität, einzufügendes Objekt)
  (muss man glaube ich nicht pseudocoden)
```

c)
Gleiche Aufgabe wie auf 1. Übungsblatt A1

```
c1)
Dichte Objekte = 6
Dichte-erreichbare Objekte = 1,4,5,7
Clustering Resultat:
C1={6,1,4,5,7}

c2)
Dichte Objekte = 1,3,4,5,6,7,8,9,10
Dichte-erreichbare Objekte = None
Clustering Resultat:
C1={1,6,4,5,7} von der 1 kleinste Nachbar 6, danach dann 4 und so weiter
C2={3,8,9,10}

c3)
Anzahl der Dichte-erreichbare Punkte stets 0,
da theoritsch mit MinPts=1 jeder Dichte-erreichbare Punkt
auch gleich dichter Punkt ist, da selbe Bedingung erfüllt wird.
Daraus folgt das der Punkt dicht und nicht dichte-erreichbar ist.
```

d)
```
𝑃[𝑄,𝑟]=1−(1−𝑉𝑜𝑙𝑢𝑚𝑒(𝑠𝑝ℎ𝑒𝑟𝑒𝑑(𝑄,𝑟) ∩ Ω))^𝑁
```

*Inhalt 2.Vorlesung*

## Aufgabe 3

a)

```sql
SELECT p.name AS Nachname, p.vorname AS Vorname,
count(b.f_id) AS Anzahl_Buchungen, sum(preis) AS Kosten   
FROM Passagiere p
INNER JOIN Buchungen b ON p.id = b.p_id
INNER JOIN Flugstrecke f ON b.f_id = f.id
GROUP BY p.vorname, p.name
```

b)

```sql
CREATE VIEW Lowcost AS
SELECT f.abflug, f.flugziel, f.preis
FROM Flugstrecke f
WHERE f.preis < 50
AND f.dauer > 2
```

c)

```sql
DELETE FROM Buchungen b
WHERE b.p_id IS IN (SELECT f.id FROM Flugstrecke f
WHERE f.firma = "LuftBW")

DELETE FROM Flugstrecke f
WHERE firma = "LuftBW"
```

d)

```sql
SELECT Stadt, (AnzAbflug + AnzFlugziel) as Gesamtanzahl an Passagieren
FROM
(SELECT f.abflug as Stadt, count(*) as AnzAbflug
FROM Flugstrecke f
INNER JOIN Buchungen on f.id = b.f_id
GROUP BY f.abflug)
INNER JOIN
(SELECT f.flugziel as Ziel, count(*) as AnzFlugziel
FROM Flugstrecke f
INNER JOIN Buchungen on f.id = b.f_id
GROUP BY f.flugziel)
ON Stadt = Ziel
```

e)

```sql
Meine Idee noch nicht fertig:
(SELECT * FROM Flugstrecke f1
INNER JOIN Flugstrecke f2
ON f1.id IS NOT f2.id
WHERE f2.abflug = f1.flugziel
AND f1.abflug IS NOT f2.flugziel)

(SELECT * FROM Flugstrecke f1
INNER JOIN Flugstrecke f2 ON f1.id IS NOT f2.id
INNER JOIN Flugstrecke f3 ON f1.id IS NOT f3.id AND f2.id IS NOT f3.id
WHERE f1.flugziel = f2.abflug
AND f2.flugziel = f2.abflug
AND f1.abflug IS NOT f2.flugziel
AND f1.abflug IS NOT f3.flugziel
AND f2.abflug IS NOT f3.flugziel)

Rauls Lösung:
SELECT t4.c1 as Abflug,
	(CASE  WHEN t4.c1 = t4.c4 THEN t4.c3 ELSE t4.c4 END) as Flugziel,
	(CASE WHEN t4.c1 = t4.c4 THEN 2 ELSE 3 END) as Anzahl_Zwischenhalte,
	(CASE WHEN t4.c1 = t4.c4 THEN 
	(SELECT SUM(Flugstrecken.preis) 
  FROM Flugstrecken 
  WHERE (Flugstrecken.abflug = t4.c1 AND Flugstrecken.flugziel = t4.c2) 
  OR (Flugstrecken.abflug = t4.c2 AND Flugstrecken.flugziel = t4.c3)) 
	ELSE 
	(SELECT SUM(Flugstrecken.preis) 
  FROM Flugstrecken 
  WHERE (Flugstrecken.abflug = t4.c1 AND Flugstrecken.flugziel = t4.c2) 
  OR (Flugstrecken.abflug = t4.c2 AND Flugstrecken.flugziel = t4.c3) 
  OR (Flugstrecken.abflug = t4.c3 AND Flugstrecken.flugziel = t4.c4))
		END) as Kosten
FROM 
(SELECT t1.abflug as c1, t1.flugziel as c2, t2.flugziel as c3, t3.flugziel as c4 FROM Flugstrecken t1
JOIN Flugstrecken t2 ON t1.flugziel = t2.abflug
JOIN Flugstrecken t3 ON t2.flugziel = t3.abflug
WHERE t1.abflug <> t2.flugziel) t4
```

## Aufgabe 4

a)

| Nr. | Antwort |  
|---|---|
|1.|nicht ableitbar|  
|2.|ableitbar|    
|3.|ableitbar|  
|4.|nicht ableitbar|  
|5.|nicht ableitbar|  

b)

```
K = {{B,G},{B,F},{A,F},{A,G},{A,C,E},{B,C,E},{D,C,E},{D,F},{D,G},{F,B,C},{G,B,C}}
R befindet sich in 1NF, da die Attribute
der Relation atomar sind (aus Aufgabenstellung).
R befindet sich nicht mehr in 2NF, da Nichtprimattribute
von einer echten Teilmenge der Schlüsselkandidaten abhängen.
Bsp. AF ist Schlüssel und B hängt nur von A ab.
```


c)

```
H1 = w1(x) r2(x) r3(x) r4(x) c1 c2 c3 c4
H2 = w1(x) w2(x) w3(x) w4(x) w1(x) w2(x) w3(x) c1 c2 c3 c4
H3 = w1(x) r2(x) r4(x) w4(y) r3(y) r1(y) c1 c2 c3 c4
```
