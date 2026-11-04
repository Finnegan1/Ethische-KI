# W04 – Fairness- und Bias-Analyse

**Gruppe 10 · T01: Kreditwürdigkeitsprüfung mittels maschinellen Lernens**

## Kapitel 2: Fairness- und Bias-Analyse

### 1. Entscheidung und Betroffene

Das Modell entscheidet über die Bewilligung eines Kredits: $\hat{Y}=1$ bedeutet Zusage, $\hat{Y}=0$ Ablehnung. $Y=1$ bezeichnet Personen, die den Kredit zu den vereinbarten Bedingungen ohne Zahlungsausfall zurückzahlen würden. Verglichen werden Antragstellende zwischen **35 und 64 Jahren (a)** und zwischen **18 und 34 Jahren (b)**. Betroffen sind die Antragstellenden und die kreditgebende Bank.

Als mögliche Bias-Quelle betrachten wir historisch selektierte Kreditdaten: Rückzahlungsverläufe liegen nur für bewilligte Kredite vor. Wurden jüngere Personen früher seltener berücksichtigt, könnten ihre Rückzahlungsmöglichkeiten unterschätzt werden. Die Kredithistorie kann dabei als Altersproxy wirken. Das ist eine Hypothese, kein nachgewiesener Befund.

### 2. Metrik und Schutzgut

Wir wählen **Equalized Odds**: Beide Altersgruppen sollen bei gleicher tatsächlicher Rückzahlungsfähigkeit gleiche Zusagewahrscheinlichkeiten haben, also gleiche TPR und FPR. Gleiche TPR schützt vor ungleich verteilten unberechtigten Ablehnungen; gleiche FPR berücksichtigt Ausfallrisiken der Bank und mögliche Überschuldung der Kreditnehmenden.

Gemessen wird der größere Abstand zwischen den gruppenspezifischen TPR beziehungsweise FPR. Ergänzend betrachten wir die Differenz der Bewilligungsraten, also die **demografische Parität**. Bei ungleichen Basisraten können gleiche Fehlerraten trotzdem unterschiedliche Bewilligungsraten ergeben. Gleiche Zusagequoten allein beantworten unsere Fairness-Frage deshalb nicht.

### 3. Daten und verfügbare Merkmale

Der synthetische Datensatz enthält Altersgruppe, Haushaltsüberschuss, Zahlungszuverlässigkeit, einen **Index kurzer Kredithistorie**, Altdatenlabel und wahre Rückzahlungsfähigkeit. Hohe Proxy-Werte stehen für eine kurze Kredithistorie. Die Altersgruppe liegt für Training und Fairness-Auswertung vor, wird aber nicht als direktes Modellmerkmal verwendet.

Unsere Baseline ist eine Szenarioannahme: **85 % Rückzahlungsfähige in a, 65 % in b**, Proxy-Stärke **0,80**, Anteil b **40 %**, Label-Bias **0**, Seed **42**. Der Abstand soll Unterschiede zwischen den Fairness-Metriken sichtbar machen, nicht reale Altersunterschiede belegen. Von 5.000 Fällen dienen 3.500 dem Training und 1.500 der Messung. Die Messdaten enthalten 900 Personen aus a und 600 aus b. Im realen Einsatz wären Rückzahlungslabels früher abgelehnter Personen gerade nicht verfügbar; im synthetischen Datensatz sind sie bekannt.

### 4. Eingriff und Ergebnis

Da Daten und Training zugänglich sind, ergänzen wir die logistische Regression um einen **Fairness-Strafterm mit Stärke $\mu=30$**. Er nähert die mittleren Scores beider Altersgruppen getrennt nach Label an, garantiert aber keine gleichen Fehlerraten. Die Entscheidungsschwelle bleibt für beide Gruppen bei **0,50**. Beide Modelle werden auf denselben Messfällen verglichen.

| Kennzahl | Basismodell | Nach Eingriff |
|---|---:|---:|
| Bewilligungsrate a | 91,67 % | 90,11 % |
| Bewilligungsrate b | 73,33 % | 80,50 % |
| TPR a | 97,14 % | 95,83 % |
| TPR b | 90,51 % | 94,87 % |
| FPR a | 59,85 % | 56,82 % |
| FPR b | 41,43 % | 53,81 % |
| Differenz Chancen | 0,1842 | 0,0301 |
| Differenz Parität | 0,1833 | 0,0961 |
| Gesamtgenauigkeit | 85,00 % | 84,00 % |

Die falschen Ablehnungen jüngerer Personen sinken von **37 auf 20**, ihre Fehlbewilligungen steigen jedoch von **87 auf 113**. In a steigen die falschen Ablehnungen von 22 auf 32, während die Fehlbewilligungen von 79 auf 75 sinken. Insgesamt entstehen **15 zusätzliche Fehler**. Die Gruppenabstände werden kleiner, aber nicht sämtliche Fehlerraten besser.

### 5. Grenzen und Bewertung

Die Daten sind vollständig synthetisch; die vermutete historische Selektionslücke wird nicht nachgebildet. Die Auswertung beruht auf einem Seed und einem Split. Die Stärke 30 wurde nicht unabhängig validiert. Für die FPR stehen **132 negative Fälle in a und 210 in b** zur Verfügung. Ein stabiler Nachweis würde weitere, unabhängig ausgewertete Daten erfordern.

Bei Label-Bias 0 stimmen Altdatenlabels und wahre Eignung überein. Bereits die pauschale Bewilligung aller Anträge erreicht auf den Messdaten **77,20 %** Genauigkeit. Die Gesamtgenauigkeit muss daher zusammen mit den Fehlerraten bewertet werden.

Der Eingriff verringert die Gruppenabstände deutlich, erfüllt Equalized Odds aber nicht exakt. Die Genauigkeit sinkt um einen Prozentpunkt. Nach dem Eingriff wird weiterhin mehr als die Hälfte der nicht rückzahlungsfähigen Personen jeder Gruppe bewilligt. Für einen realen Einsatz reicht das Ergebnis nicht aus: Ähnliche Fehlerraten ersetzen keine zuverlässige Kreditprüfung.

## Zeile für die Vergleichsmatrix

G10 · T01 · Equalized Odds · Training · Differenz Chancen 0,18 → 0,03 · Genauigkeit 0,85 → 0,84

## Abgabedateien

- [[WO-4/Übung/W04_G10_Ergebnis.txt|Ergebnisdatei aus dem Arbeitsblatt]]
- [[WO-4/Übung/W04_G10_Datensatz.csv|Datensatz für W05]]
- [[WO-4/Übung/W04_G10_Arbeitsblatt.pdf|Ausgefülltes Arbeitsblatt als PDF]]
