# Lösungen DBS 02.2015

Alle Lösungen ohne Gewähr!

## Aufgabe 1

a)
1. Mitarbeiter, Medizinisches- und Verwaltungspersonal:  

oben Partitionierung:  
```
jedes Medizinisches- oder Verwaltungspersonal ist ein Mitarbeiter,  
aber nicht jeder Mitarbeiter ist Medizinisches- oder Verwaltungspersonal.  
```
unten Generalisierung:  
```
nicht jedes Medizinisches- oder Verwaltungspersonal ist ein Mitarbeiter,  
aber jeder Mitarbeiter ist Medizinisches- oder Verwaltungspersonal.  
```
2. Medizinisches Personal, Arzt, Pflegepersonal:  

oben totale Partitionierung:  
```
jedes Medizinische Personal ist Arzt oder Pflegepersonal  
```
unten Partitionierung:  
```
jedes Medizinische Personal kann Arzt oder Pflegepersonal sein (aber muss nicht).  
```
3. bis 6. Patient, Medikament, Arzt  

**(ab hier unsicher, fehlen noch Unterschiede bitte schreiben um zu ergänzen!)**  
--> Aus Folien: N entspricht etwa \*, also [0,\*]  
oben:  
```
Ein Patient kann von einem Arzt behandelt werden ohne Mediakamente zu bekommen. (2)   
Ein Patient kann ein Medikament bekommen ohne einen Arzt zu haben.  (1)
```

unten:   
```
verabreicht(Medikament, Arzt):
Ein Medikament wird von einem oder mehreren Ärzten verabreicht. (1)
Es gibt Ärzte die keine Medikamente verabreichen.
bekommt(Medikament, Patient):
Ein Patient bekommt mindestens ein Medikament. (2)
Ein Medikament kann von keinem Arzt verabreicht werden.
```


b)   

| Nr. | Antwort |  
|---|---|
|1.|glaube wahr|  
|2.|wahr|    
|3.|wahr|  
|4.|falsch|  
|5.|falsch|  
|6.|wahr|  
|7.|wahr|  
|8.|falsch|
|9.|falsch|  
|10.|wahr|  


## Aufgabe 2

a)

```
Menge aller möglichen Schlüssel:
K={{C,D},{A,C},{C,E}}
F(A)+ = {A->E, A->F, A->A}
{A}F* = {A,E,F}
```

b)
```
F1:
A->C
B->D

F2:
A->BC
B->C
C->D

F3:
AB->CD
```

c)
```
Redundante FD:
F ist Menge von FDs
f ist FD
f Element von F
f ist redundant wenn:
F - {f} ist äquivalent zu F

Attribut überflüssig:
Attribute aus F überflüssig, wenn unwesentlich
(Aus Folien: Super Klasse Definition xD)
Ich glaube das ist richtig:
a ist Attribut
a ist Attribut was in FD f enthalten
FD g = f - a (also Attribut aus FD f herausgenommen, g ist f ohne a)
dann muss: F - f + g äquivalent zu F sein wenn Attribut a überflüssig
(wir haben in F FD f durch FD g ersetzt)

Nach Synthese:
A  -> BED
F  -> CD
C -> EFB

Alle weggefallenen FDs sind redundant
und alle weggefallenen Attribute sind überflüssig :)
```

## Aufgabe 3

a)  
```sql
CREATE VIEW Teilnehmerzahl AS  
SELECT F.name AS Kursname, count(A.k_id) as Teilnehmer  
FROM Fitnesskurse F  
INNER JOIN Abo A on F.id = A.f_id  
GROUP BY A.f_id
```

b)  
```sql
DELETE Abo  
WHERE k_id = 1  
AND f_id = 2  
LIMIT = 1  (nur eine der beiden Zeilen löschen!)

ALTER TABLE Abo  
ADD CONSTRAINT unique_abo UNIQUE(f_id,k_id)
```
c)
```sql
SELECT K.name AS NAME
FROM Kunde K  
INNER JOIN Abo A ON A.k_id = K.id  
INNER JOIN Fitnesskurse F ON A.f_id = F.id  
GROUP BY K.id  
HAVING K.alter <= 25  
AND sum(F.preis) <= 150  
```

d)
**brauche Lösung**

e)  
```sql
ALTER TABLE Kunde  
ADD email VARCHAR2(25) NULL  
```

f)  
```sql
(Nicht sicher ob CREATE VIEW notwendig)
CREATE VIEW Yoga AS
SELECT K1.name AS PartnerA, K2.Name AS PartnerB
FROM Kunde K1
INNER JOIN Kunde K2 ON K1.id < K2.id
WHERE ABS(K1.alter - K2.alter) <= 5
AND K1.geschlecht = K2.geschlecht
```

## Aufgabe 4

a)  
```
Definition RC(Rücksetzbar):
Commit für Ti erst dann erlauben wenn Tj, von der Ti liest, committed hat.

Definition Serialisierbar:
Als serialisierbar bezeichnet man eine Historie, wenn sie zum selben Ergebnis
führt wie eine nacheinander (seriell) ausgeführte Historie über dieselben
Transaktionen.

T1 < T2:
H1: r1[x] r1[y] w1[x] w1[y] r1[x](!) w2[x] w2[y] r2[z] w2[z] r2[x] c2 c1
Klar und deutlich zu sehen ist, das T1 < T2 gilt.
History ist serialisierbar, da Transaktionen hintereinander angehängt und
nur Commit von T1 an das Ende verschoben.
RC ist nicht erfüllt da T2 vor T1 commited und T2 von T1 liest.
Bedingung zu beachten hier:
r1[x](!) nach w1[x] und vor w2[x]
Diese Bedingung macht es unmöglich eine weitere History dieses Typs zu finden
neben der angegebenen, da eine verschiebung von Elementen aus T1
immer ein verschieben von r1[x](!) mit sich bringt. Dadurch würde r1[x](!) nach
w2[x] kommen was die Bedingung der Serialisierbarkeit zerstört.
Also beträgt die Anzahl der möglichen Histories 1.

T2 < T1:
H1: w2[x] w2[y] r2[z] w2[z] r2[x] r1[x] r1[y] w1[x] w1[y] r1[x] c1 c2
Klar und deutlich zu sehen ist, das T2 < T1 gilt.
History ist serialisierbar, da Transaktionen hintereinander angehängt und
nur Commit von T2 an das Ende verschoben.
RC ist nicht erfüllt da T1 vor T2 commited und T1 von T2 liest.
Was für weitere Histories zu beachten ist, ist das r2[x] immer vor w1[x] sein
muss, w2[x] vor r1[x], w2[y] vor r1[y], zwischen w1[x] und dem hinteren r1[x]
darf nicht von T2 geschrieben werden, zwischen w2[x] und r2[x]
darf nicht von T1 geschrieben werden, um die Serialisierbarkeit zu garantieren.
Mit diesen Einschränkungen gibt es 14 Möglichkeiten, da man nur r1[x] und r1[y]
beliebig verschieben kann und r1[x] vor r1[y] laufen muss.
Weitere Beispiele (vergleiche mit H1 oben):
H2: w2[x] w2[y] r2[z] w2[z] r1[x](<--verschoben) r2[x] r1[y] w1[x] w1[y] r1[x] c1 c2
H3: w2[x] w2[y] r2[z] w2[z] r1[x] r1[y](<--verschoben) r2[x] w1[x] w1[y] r1[x] c1 c2
H4: und so weiter...
```

b)
```
Definition Nicht Serialisierbar:
Als nicht serialisierbar bezeichnet man eine Historie, wenn sie nicht zum
selben Ergebnis führt wie eine nacheinander (seriell) ausgeführte
Historie über dieselben Transaktionen.

Definition STRICT:
Beim Schreiben Element x darauf achten dass alle Transaktionen
die X verändern haben committed oder aborted sind.

H1: r1[x] w2[x] w2[y] r2[z] w2[z] r2[x] c2 r1[y] w1[x] w1[y] r1[x] c1
Was ist passiert:
T1 an T2 angehängt um Striktheit zu ermöglichen.
r1[x] vor w2[x] um History nicht serialisierbar zu machen.
(Ausreichend?)
```

c)
```
Um weitere Histories zu finden die die gewollten Eigenschaften erfüllen:
T1 kann nur schreiben nach Commit von T2. Im oberen Beispiel(H1 Teil b) ) hat man
also nur die Möglichkeit r1[x] r1[y] nach vorne zu bewegen.
Außerdem muss entweder r1[x] vor w2[x] oder r1[y] vor w2[y].
Somit ergeben sich für diese Form 3 Möglichkeiten.
Es gibt noch Form (hier noch serialisierbar, aber STRICT)
r1[x] r1[y] w1[x] w1[y] r1[x](!) c1 w2[x] w2[y] r2[z] w2[z] r2[x] c2
Da wie besprochen w2[x] nach c1 kommen muss kein spielraum zum verschieben hier.
Also insgesamt 3 verschiedene Formen.
```
