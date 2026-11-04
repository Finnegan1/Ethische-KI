# W05 Demodaten je Thema

I050 Ethische und vertrauenswürdige KI · HTW Dresden · Übung W05 · Fassung 02.11.2026

## Wofür

Für Gruppen, denen der eigene Datensatz aus W04 fehlt: keine W04-CSV und keine W04-Werte zum Neuerzeugen. Wer die eigene W04-CSV oder die eigenen W04-Werte hat, arbeitet damit weiter und nutzt diese Dateien nicht.

## Was es ist

Neun Datensätze, einer je Thema T01–T09, im selben Format wie der CSV-Export des W04-Arbeitsblatts:

- Kopf: Gruppenmerkmal (mit Bedeutung von a und b), zwei eignungsbezogene Merkmale, ein Proxy, `y_altdaten`, `y_wahr`, `teil`
- je Fall: Gruppe `a` oder `b`, drei Merkmalswerte mit vier Nachkommastellen, Altentscheidung und wahre Eignung (0 oder 1), `training` oder `messung` (70 zu 30)
- UTF-8, Komma als Trennzeichen, Punkt als Dezimalzeichen

## Herkunft

Vollständig synthetisch. Erzeugt mit dem Generator aus W04 (im W05-Arbeitsblatt unverändert übernommen): wahre Eignung nach Basisrate der Gruppe; Merkmal 1 = 1,2 · Eignung + Rauschen, Merkmal 2 = 0,8 · Eignung + Rauschen, Proxy = 2,5 · Proxy-Stärke · (Gruppe b) + Rauschen; Label-Bias erfasst diesen Anteil der Geeigneten in b als ungeeignet. Die Merkmalsnamen sind Beispiele, keine Messungen. Es gibt keinen Bezug zu realen Personen, auch nicht zu Studierenden dieses Kurses.

## Werte je Thema

Seed: Hunderter plus Themennummer, für T01–T03 Seed 42 wie die Muster-Baselines aus W04.

| Datei | Ŷ = 1 bedeutet | Gruppenmerkmal (a; b) | Merkmal 1 · Merkmal 2 · Proxy | Basisrate a · b | Proxy-Stärke | Fälle | Anteil b | Label-Bias | Seed |
|---|---|---|---|---|---|---|---|---|---|
| W05_Demodaten_T01.csv | Kredit gewährt | Staatsangehörigkeit (deutsch; andere) | Einkommen · Zahlungshistorie · Postleitzahlgebiet | 0,70 · 0,55 | 0,8 | 5.000 | 0,40 | 0 | 42 |
| W05_Demodaten_T02.csv | Rabatt angezeigt | Altersgruppe (unter 50; 50 und älter) | Kaufhistorie · Klickverhalten · Endgerät | 0,40 · 0,25 | 0,6 | 10.000 | 0,40 | 0 | 42 |
| W05_Demodaten_T03.csv | zum Gespräch eingeladen | Altersgruppe (unter 45; 45 und älter) | Berufserfahrung · Qualifikation · Abschlussjahrgang | 0,50 · 0,30 | 0,8 | 5.000 | 0,40 | 0 | 42 |
| W05_Demodaten_T04.csv | an einen Menschen weitergegeben | Sprache der Anfrage (Deutsch; andere) | Angabenvollständigkeit · Dringlichkeit · Sprachabweichung | 0,35 · 0,25 | 0,6 | 5.000 | 0,30 | 0 | 304 |
| W05_Demodaten_T05.csv | Angebot in die engere Wahl | Anbietergröße (groß; klein) | Preisvorteil · Lieferzuverlässigkeit · Fehlende Produktangaben | 0,45 · 0,30 | 0,7 | 5.000 | 0,50 | 0 | 305 |
| W05_Demodaten_T06.csv | Datensatz als vollständig angenommen | Herkunftsregion (EU; außerhalb der EU) | Nachweisdichte · Feldvollständigkeit · Übersetzungsanteil | 0,60 · 0,45 | 0,8 | 5.000 | 0,35 | 0,10 | 306 |
| W05_Demodaten_T07.csv | Person erkannt, Ausweichen ausgelöst | Körpergröße (ab 1,60 m; unter 1,60 m) | Warnkleidung · Bewegungskontrast · Verdeckungsgrad | 0,75 · 0,60 | 0,6 | 10.000 | 0,20 | 0 | 207 |
| W05_Demodaten_T08.csv | Bild freigegeben | Art der Quelle (Agentur; Bürgerjournalismus) | Content Credentials · Metadatenvollständigkeit · Kompressionsgrad | 0,65 · 0,45 | 0,9 | 5.000 | 0,30 | 0,15 | 108 |
| W05_Demodaten_T09.csv | kein Abbruchrisiko gemeldet | Studienform (Vollzeit; berufsbegleitend) | Abgabequote · Teilnahme · Abendaktivität | 0,70 · 0,55 | 0,7 | 2.000 | 0,40 | 0,20 | 209 |

Bei T06, T08 und T09 ist Ŷ = 1 das günstige Ergebnis. Wer Ŷ = 1 in W04 als belastende Meldung definiert hat, notiert die Umdeutung in Teil A.

## So nutzen Sie die Dateien

1. Im Arbeitsblatt `W05_Arbeitsblatt_Uebung.html`, Teil B, Schritt 0, unter „Weg c“ das Thema wählen. Das Blatt setzt Werte, Seed und Merkmalsnamen und erzeugt denselben Datensatz ohne Datei.
2. Alternativ die CSV Ihres Themas unter „Weg a“ laden und den Seed aus der Tabelle eintragen. Beide Wege ergeben denselben Datensatz und dieselben Zahlen.
3. In der Ergebnisdatei steht dann „Demodaten Txx (synthetisch)“; so bleibt nachvollziehbar, mit welchem Datensatz gerechnet wurde.

Die Dateien enthalten keine Lösung. Die Merkmalsnamen ersetzen nicht Ihre eigene Festlegung in Teil A.
