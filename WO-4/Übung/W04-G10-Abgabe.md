# W04 – Fairness- und Bias-Analyse

**Gruppe 10 · T01 Kreditwürdigkeitsprüfung · 28.10.2026**

## 1. Entscheidung, Bias und Zielmetrik

Wir untersuchen die Bewilligung eines Kredits: $\hat{Y}=1$ bedeutet Zusage, $Y=1$ tatsächliche Rückzahlungsfähigkeit. Verglichen werden **35–64-Jährige (a)** und **18–34-Jährige (b)**. Als mögliche Bias-Quelle betrachten wir historisch selektierte Kreditdaten: Wenn jüngere Rückzahlungsfähige früher seltener Kredite erhielten, fehlen ihre Rückzahlungsverläufe. Eine kurze Kredithistorie könnte dann als Altersproxy zu ungerechtfertigten Ablehnungen beitragen. Das ist eine Hypothese, kein nachgewiesener Zusammenhang.

Zielmetrik ist **Equalized Odds**: Rückzahlungsfähige sollen unabhängig von der Altersgruppe gleich häufig eine Zusage erhalten (gleiche TPR); nicht Rückzahlungsfähige gleich häufig eine Fehlbewilligung (gleiche FPR). Damit berücksichtigen wir sowohl entgangene Finanzierungsmöglichkeiten als auch Ausfall- und Überschuldungsrisiken. Als zweite Metrik berichten wir die **demografische Parität**, also den Abstand der Bewilligungsraten.

## 2. Datengrundlage und Eingriff

Die Simulation verwendet **5.000 synthetische Fälle**, davon 3.500 zum Training und 1.500 zur Messung. Einstellungen: Basisrate a **0,95**, b **0,90**, Anteil b **0,40**, Proxy-Stärke **0,80**, Label-Bias **0**, Seed **42**. Fachmerkmale sind „Haushaltsüberschuss“ und „Zahlungszuverlässigkeit“, der Proxy heißt „Index kurzer Kredithistorie“. Diese Bezeichnungen stehen für synthetische Größen, nicht für erhobene Kreditdaten.

Im Messdatensatz liegen für a **898 Fälle** vor, davon 858 rückzahlungsfähig; für b **602 Fälle**, davon 536 rückzahlungsfähig. Merkmale, Altersgruppen und Labels sind im Laborszenario vollständig verfügbar. Bei real abgelehnten Anträgen wären die Rückzahlungslabels dagegen nicht beobachtbar.

Wir vergleichen die logistische Regression mit einem **Trainingseingriff mit Fairness-Bedingung, $\mu=30$**. Der Strafterm nähert die mittleren Scores beider Gruppen getrennt nach Label an; gleiche Fehlerraten werden dadurch nicht garantiert. Die Altersgruppe dient der Fairness-Optimierung und Auswertung, nicht als direktes Modellmerkmal. Für beide Gruppen bleibt die Schwelle **0,50**. Die Auswertung ist explorativ: $\mu=30$ war im vorhandenen Arbeitsstand bereits eingestellt und wurde nicht unabhängig validiert.

## 3. Messergebnisse

| Modell | Gruppe | Bewilligungsrate | TPR | FPR |
|---|---|---:|---:|---:|
| Basismodell | a: 35–64 Jahre | 99,44 % | 99,88 % | 90,00 % |
| Basismodell | b: 18–34 Jahre | 96,68 % | 99,07 % | 77,27 % |
| Nach Eingriff | a: 35–64 Jahre | 99,33 % | 99,77 % | 90,00 % |
| Nach Eingriff | b: 18–34 Jahre | 98,67 % | 99,81 % | 89,39 % |

| Kennzahl | Vorher | Nachher |
|---|---:|---:|
| Genauigkeit | 93,80 % | 93,47 % |
| Differenz Chancen: größter Abstand von TPR oder FPR | 0,1273 | 0,0061 |
| Differenz Parität: Abstand der Bewilligungsraten | 0,0277 | 0,0066 |
| Verhältnis der Bewilligungsraten: kleinere/größere | 0,9722 | 0,9933 |

Im Basismodell werden **5 von 536** rückzahlungsfähigen jüngeren Personen fälschlich abgelehnt, gegenüber **1 von 858** älteren. Nach dem Eingriff sind es **1 beziehungsweise 2**. Gleichzeitig steigen die Fehlbewilligungen in b **von 51 auf 59**; in a bleiben sie bei **36**. Die kleinere FPR-Lücke entsteht somit durch mehr Fehlbewilligungen bei Jüngeren, nicht durch eine bessere Erkennung von Ausfallrisiken.

**Preis:** Beide Gruppenabstände sinken, die Genauigkeit fällt jedoch um **0,33 Prozentpunkte**; insgesamt entstehen **98 statt 93 Fehler**. Equalized Odds wird annähernd, nicht exakt erreicht. Eine FPR um 90 % in beiden Gruppen ist trotz kleiner Fairness-Lücke keine brauchbare Kreditprüfung. Einen realen Einsatz würden wir so nicht empfehlen.

## 4. Grenzen und Einordnung

- Die Simulation bildet weder historische Kreditselektion noch fehlende Rückzahlungslabels ab. Sie bestätigt unsere Bias-Hypothese nicht und erlaubt keine Aussage über reale Altersgruppen.
- Die FPR beruht nur auf **40 negativen Fällen in a und 66 in b**. Ein einzelner Fall verändert sie um 2,50 beziehungsweise 1,52 Prozentpunkte. Ein Seed und ein Split reichen nicht für einen stabilen Fairness-Nachweis.
- Bei Label-Bias 0 stimmen Altdatenlabels und wahre Eignung überein. Die zusätzliche Messung gegen die wahre Eignung liefert daher dieselben Ergebnisse.
- Schon „alle Kredite bewilligen“ erreicht auf diesen Messdaten **92,93 % Genauigkeit**. Die hohe Genauigkeit allein verdeckt das Ausfallrisiko.
- Gleiche Fehlerraten erzwingen bei unterschiedlichen Basisraten keine gleichen Bewilligungsraten. Dass hier beide Lücken kleiner werden, ist kein allgemeiner Nachweis ihrer Vereinbarkeit.

## Vergleichsmatrix und Anlagen

G10 · T01 · Equalized Odds · Training · Differenz Chancen 0,13 → 0,01 · Genauigkeit 0,94 → 0,93

Die Matrixzeile folgt der Rundung des Arbeitsblatts. Anlagen für OPAL und W05: [[W04_G10_Ergebnis.txt|Ergebnisdatei]], [[W04_G10_Datensatz.csv|Datensatz]] und [[W04_G10_Arbeitsblatt.pdf|Arbeitsblatt als PDF]].

Grundlage: [[W04_Uebung.pdf|W04-Übung]], Aufgabenkarte 1 und 2 sowie Kapitelkarte; Berechnung im [[W04_Arbeitsblatt_Uebung.html|W04-Arbeitsblatt]].
