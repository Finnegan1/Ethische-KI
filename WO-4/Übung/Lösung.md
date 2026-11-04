# Infos

- wir sind gruppe 10
- wir haben das thema T1 - die Kreditwürdigkeit mittels Maschinellem Lernen

## Teil A – Schritt 1: Entscheidung und Merkmal

### Vorschlag für die Entscheidung

- $\hat{Y}=1$: Der beantragte Kredit wird bewilligt.
- $\hat{Y}=0$: Der beantragte Kredit wird abgelehnt.
- Davon zu unterscheiden ist die tatsächliche Kreditwürdigkeit $Y$: $Y=1$ bedeutet im vereinfachten Szenario, dass die Person den Kredit zu den festgelegten Bedingungen innerhalb der vereinbarten Laufzeit ohne Zahlungsausfall zurückzahlen würde.
- Zwei mögliche Fehler: rückzahlungsfähige Personen ablehnen (falsch-negativ) oder nicht rückzahlungsfähigen Personen einen Kredit bewilligen (falsch-positiv).

### Ideen für das Gruppenmerkmal A

| Merkmal | Gruppe a | Gruppe b | Möglicher Proxy als Hypothese |
|---|---|---|---|
| Geschlecht | männliche Antragsteller | weibliche Antragstellerinnen | Erwerbsunterbrechungen bei ungleich verteilter Sorgearbeit |
| Alter | 35–64 Jahre | 18–34 Jahre | Länge der bisherigen Kredithistorie |
| Migrationsgeschichte | ohne Migrationsgeschichte | mit Migrationsgeschichte | Wohngebiet beziehungsweise Postleitzahl |

> [!note] Vorläufige Wahl: Alter
> Wir untersuchen zunächst das Merkmal $A =$ Altersgruppe: Gruppe a umfasst 35–64-jährige, Gruppe b 18–34-jährige Antragstellende. Leitfrage: Werden tatsächlich rückzahlungsfähige jüngere Personen häufiger fälschlich abgelehnt als tatsächlich rückzahlungsfähige Personen der älteren Vergleichsgruppe? Als möglichen Proxy betrachten wir die Länge der bisherigen Kredithistorie. Eine kürzere Historie bedeutet nicht automatisch geringere Rückzahlungsfähigkeit; ihr möglicher Informationswert ist von einer ungerechtfertigten Benachteiligung zu unterscheiden. Die Altersgrenzen sind eine Arbeitsannahme; Personen ab 65 Jahren sind in diesem vereinfachten Vergleich nicht erfasst. Proxy-Zusammenhänge und die in Schritt 5 festgelegten unterschiedlichen Basisraten sind Szenarioannahmen, keine empirischen Befunde. Alle drei Optionen bleiben für eine spätere Änderung in der Tabelle erhalten.

## Teil A – Schritt 2: Bias-Quelle

### Gewählte Ebene: Daten

Als Arbeitshypothese untersuchen wir **historisch selektierte Kreditdaten und eine mögliche Repräsentationslücke**. Rückzahlungsverläufe sind nur für tatsächlich vergebene Kredite beobachtbar. Falls jüngere Antragstellende früher seltener Kredite erhalten haben, können rückzahlungsfähige Personen dieser Altersgruppe in den Trainingsdaten unterrepräsentiert sein. Das Modell könnte dadurch einen Zusammenhang zwischen kurzer Kredithistorie und mangelnder Rückzahlungsfähigkeit lernen, der für die heutige Gesamtheit der Antragstellenden nicht gerechtfertigt ist.

Die Länge der bisherigen Kredithistorie kann dabei als **Proxy für das Alter** wirken. Das Weglassen des Alters allein würde diesen möglichen Einfluss nicht beseitigen.

### Formulierung für das Arbeitsblatt

- **Ebene:** Daten.
- **In einem Satz:** „Wenn rückzahlungsfähige 18–34-Jährige in historisch selektierten Kreditdaten unterrepräsentiert sind, kann das Modell über die Kredithistorie als Altersproxy ihre Rückzahlungsfähigkeit systematisch unterschätzen.“

### Einordnung gegenüber den anderen Ebenen

| Ebene | Bezug zu unserem Szenario |
|---|---|
| **Daten – unser Schwerpunkt** | Die Auswahl früherer Kreditnehmender kann dazu führen, dass die Trainingsdaten heutige Antragstellende nicht angemessen abbilden. |
| Label | Ein zusätzlicher Label-Bias läge vor, wenn frühere Ablehnungen als „nicht rückzahlungsfähig“ codiert würden, obwohl eine Rückzahlung gar nicht beobachtet wurde. Das unterstellen wir bislang nicht. |
| Modell | Ein auf Gesamtgenauigkeit optimiertes Modell kann schlechtere Ergebnisse für eine unterrepräsentierte Altersgruppe verdecken. |
| Betrieb | Wiederholte Ablehnungen können die Datenlücke verstärken: Ohne Kredit entstehen keine neuen Rückzahlungsnachweise. |

> [!warning] Hypothese, noch kein Nachweis
> Eine kurze Kredithistorie oder eine Korrelation mit dem Alter beweist allein keine ungerechtfertigte Benachteiligung. Entscheidend ist, ob tatsächlich rückzahlungsfähige jüngere Personen systematisch schlechter beurteilt werden. Wir haben bisher weder reale Kreditdaten noch entsprechende Messergebnisse; die unterschiedlichen Basisraten in Schritt 5 sind bewusst gewählte Szenarioannahmen.

### Bezug zur Vorlesung und zur späteren Simulation

- [[WO-4/Vorlesung/W04_Vorlesung.pdf|W04-Vorlesung]]: Folie 13 zu historischer Verzerrung, Folie 16 zu Feedback-Schleifen und Folie 23 zu Proxys trotz Weglassen des geschützten Merkmals.
- Das Arbeitsblatt erzeugt synthetische Merkmale, Gruppen und Labels, aber keine echten Kredithistorien oder historisch selektierten Kreditvergaben. Die Benennung eines Merkmals als „Kredithistorie“ ändert den Datengenerator nicht. Die Simulation kann daher einen angenommenen Proxy-Zusammenhang und dessen Auswirkungen illustrieren, unsere historische Erklärung aber nicht empirisch bestätigen.

## Teil A – Schritt 3: Zielmetrik

### Hauptmetrik: Equalized Odds (angeglichene Chancen)

Wir wählen **Equalized Odds**, weil wir die Verteilung von Fehlentscheidungen zwischen den Altersgruppen untersuchen wollen, nicht nur die Zahl der Kreditzusagen. Dabei bleibt Gruppe a die Altersgruppe 35–64 Jahre und Gruppe b die Altersgruppe 18–34 Jahre. $\hat{Y}=1$ bedeutet Kreditbewilligung; $Y=1$ bezeichnet die tatsächliche Rückzahlungsfähigkeit unter den festgelegten Kreditbedingungen.

| Kennzahl je Altersgruppe $g$ | Definition | Bedeutung für unsere Kreditentscheidung |
|---|---|---|
| Richtig-Positiv-Rate (TPR) | $P(\hat{Y}=1\mid Y=1,A=g)$ | Anteil der tatsächlich Rückzahlungsfähigen, deren Antrag bewilligt wird. |
| Falsch-Negativ-Rate (FNR) | $P(\hat{Y}=0\mid Y=1,A=g)=1-\mathrm{TPR}_g$ | Anteil der tatsächlich Rückzahlungsfähigen, deren Antrag zu Unrecht abgelehnt wird. |
| Falsch-Positiv-Rate (FPR) | $P(\hat{Y}=1\mid Y=0,A=g)$ | Anteil der tatsächlich Nicht-Rückzahlungsfähigen, deren Antrag dennoch bewilligt wird. |

Equalized Odds fordert:

$$
\mathrm{TPR}_a=\mathrm{TPR}_b
\quad\text{und}\quad
\mathrm{FPR}_a=\mathrm{FPR}_b.
$$

Das Arbeitsblatt verdichtet beide Unterschiede zur **„Differenz Chancen“**:

$$
\Delta_{\mathrm{EO}}
=\max\left(
|\mathrm{TPR}_a-\mathrm{TPR}_b|,
|\mathrm{FPR}_a-\mathrm{FPR}_b|
\right).
$$

**Ziel:** Diese Differenz verringern; der Idealwert ist $0$. Die Einzelraten berichten wir trotzdem, damit Richtung und Art der Fehler sichtbar bleiben.

### Begründung: Wer trägt welchen Fehler?

- **Falsch-negativ:** Eine rückzahlungsfähige Person erhält keinen Kredit und verliert damit eine Finanzierungsmöglichkeit. Unser Schwerpunkt ist, dass jüngere Personen dieses Risiko nicht häufiger tragen als die ältere Vergleichsgruppe. Gleiche TPR bedeutet zugleich gleiche FNR; nur diese Bedingung heißt Equal Opportunity.
- **Falsch-positiv:** Eine nicht rückzahlungsfähige Person erhält einen Kredit. Die Bank trägt ein Ausfallrisiko; die betroffene Person kann durch Zahlungsprobleme und Überschuldung geschädigt werden. Deshalb berücksichtigen wir zusätzlich gleiche FPR und wählen das umfassendere Kriterium Equalized Odds.
- **Wertentscheidung:** Wir priorisieren den Schutz vor ungerechtfertigten Ablehnungen, ohne das Risiko ungeeigneter Kreditvergaben auszublenden. Equalized Odds selbst gewichtet den Ablehnungsfehler jedoch nicht stärker: Die „Differenz Chancen“ verwendet den größeren der beiden Abstände.

### Zweite Vergleichsmetrik: Demografische Parität

Zusätzlich berichten wir die **„Differenz Parität“**, also den Abstand der Bewilligungsraten:

$$
\Delta_{\mathrm{DP}}
=\left|P(\hat{Y}=1\mid A=a)-P(\hat{Y}=1\mid A=b)\right|.
$$

Diese Kennzahl zeigt ungleichen Kreditzugang, unterscheidet aber nicht zwischen rückzahlungsfähigen und nicht rückzahlungsfähigen Personen. Gleiche Bewilligungsraten allein würden unsere Frage nach ungerechtfertigten Ablehnungen deshalb nicht beantworten. Bei den in Schritt 5 angenommenen unterschiedlichen Basisraten können trotz gleicher TPR und FPR unterschiedliche Bewilligungsraten bestehen.

Die **Gesamtgenauigkeit** berichten wir zusätzlich vor und nach dem Eingriff, um dessen Preis zu beziffern. Gleiche Fehlerraten allein genügen nicht: Auch die Ablehnung sämtlicher Anträge würde Equalized Odds erfüllen, ohne einen sinnvollen Kreditzugang zu ermöglichen.

### Formulierung für das Arbeitsblatt

- **Zielmetrik:** Equalized Odds (gleiche TPR und FPR).
- **Begründung – Wer trägt welchen Fehler?** „Rückzahlungsfähige 18–34-Jährige sollen nicht häufiger zu Unrecht abgelehnt werden als rückzahlungsfähige 35–64-Jährige (gleiche TPR/FNR). Zugleich sollen Fehlbewilligungen nicht einseitig eine Altersgruppe treffen (gleiche FPR), da sie Ausfallrisiken für die Bank und mögliche Überschuldung für Kreditnehmende bedeuten.“

> [!warning] Grenze der Zielmetrik
> TPR und FPR benötigen verlässliche Rückzahlungslabels. Gerade bei früher abgelehnten Anträgen wissen wir nicht, ob die Personen einen bewilligten Kredit zurückgezahlt hätten. Eine Messung nur an früheren Kreditnehmenden kann deshalb die in Schritt 2 beschriebene Selektionsverzerrung übersehen. Die synthetisch bekannte Rückzahlungsfähigkeit im Arbeitsblatt löst dieses Problem nicht für reale Kreditdaten. Wir behaupten noch keine gemessene Benachteiligung und keine erreichte Fairness.

**Grundlage:** [[WO-4/Vorlesung/W04_Vorlesung.pdf|W04-Vorlesung]], Folien 19–21 zur Parität und den Fehlerraten sowie Folie 30 zur Metrikwahl als Wertentscheidung; [[WO-4/Vorlesung/W04_Glossar_Fairness_Bias.html|W04-Glossar]], Einträge zu Equalized Odds und demografischer Parität.

## Teil A – Schritt 4: Eingriff

### Gewählter Eingriff: Training mit Fairness-Bedingung

Wir wählen im Arbeitsblatt **„Training (Fairness-Bedingung)“**, also In-Processing. Das Modell soll neben guten Vorhersagen auch eine geringere Ungleichheit zwischen den Altersgruppen anstreben. Die Zielmetrik bleibt **Equalized Odds**.

### Begründung über den Zugriff

Im Arbeitsblatt haben wir Zugriff auf die synthetischen Trainingsdaten, die Altersgruppenzuordnung und das Training der logistischen Regression. Wir können daher die Trainingsoptimierung verändern und das Modell erneut trainieren. Diese Wahl gilt für unser Laborszenario; ein entsprechender Zugriff auf ein reales Banksystem ist damit nicht belegt.

Die Altersgruppe wird zum Berechnen der Fairness-Bedingung und zur gruppenweisen Auswertung benötigt. Sie wird in dieser Implementierung nicht als direktes Eingangsmerkmal der logistischen Regression verwendet. Nach dem Training gilt für beide Gruppen dieselbe Entscheidungsschwelle von $0{,}50$.

### Was das Arbeitsblatt tatsächlich verändert

Bei gewähltem Equalized-Odds-Ziel ergänzt das Training einen Strafterm für Unterschiede der **mittleren vorhergesagten Scores** zwischen den Altersgruppen, getrennt nach den vorhandenen Labels:

- Unter den als rückzahlungsfähig erfassten Personen sollen die mittleren Scores der Gruppen a und b ähnlicher werden.
- Dasselbe gilt getrennt für die als nicht rückzahlungsfähig erfassten Personen.
- Der Parameter $\mu$ steuert das Gewicht dieses Fairness-Strafterms gegenüber dem Vorhersagefehler. Eine größere Stärke garantiert weder eine bessere Testgenauigkeit noch eine kleinere „Differenz Chancen“ auf den Messdaten.

> [!warning] Annäherung, keine Garantie
> Gleiche mittlere Scores bedeuten nicht automatisch gleiche TPR und FPR bei einer festen Entscheidungsschwelle. Das Verfahren zielt auf eine Annäherung an Equalized Odds, erzwingt das Kriterium aber nicht exakt. Ob der Eingriff hilft, müssen wir in Teil B anhand der tatsächlichen Fehlerraten prüfen.

### Warum nicht die anderen angebotenen Eingriffe?

| Eingriff | Einordnung für unsere bisherigen Entscheidungen |
|---|---|
| Daten: Zusammenhang mit A entfernen | Würde im Arbeitsblatt lineare Zusammenhänge der Merkmale mit der Altersgruppe entfernen. Das kann Proxy-Einflüsse verringern, aber auch vorhersagerelevante Information entfernen; fehlende Rückzahlungsverläufe werden dadurch nicht ergänzt und Equalized Odds wird nicht gezielt optimiert. |
| **Training: Fairness-Bedingung – unsere Wahl** | Nutzt unseren vorhandenen Trainingszugriff, um das gewählte Fairness-Ziel bereits beim Lernen zu berücksichtigen. |
| Nach dem Training: Schwelle je Gruppe | Wäre eine Alternative bei Zugriff auf Scores, Altersgruppen und Auswertungslabels ohne veränderbares Training. Das Arbeitsblatt kann dafür getrennte Schwellen verwenden; auch das garantiert keine exakte Erfüllung von Equalized Odds. |

### Formulierung für das Arbeitsblatt

- **Eingriff:** Training (Fairness-Bedingung).
- **Begründung über den Zugriff:** „Im Laborszenario haben wir Zugriff auf Trainingsdaten, Altersgruppen und das Modelltraining. Daher ergänzen wir das Training um eine Fairness-Bedingung mit dem Ziel Equalized Odds. Die Altersgruppe dient der Fairness-Optimierung und Auswertung, nicht als direktes Modellmerkmal; für beide Gruppen bleibt die Entscheidungsschwelle gleich.“

### Was wir später prüfen – und was der Eingriff nicht löst

In Teil B vergleichen wir das Basismodell und das veränderte Modell auf denselben zurückgehaltenen Messdaten: TPR und FPR je Altersgruppe, „Differenz Chancen“, „Differenz Parität“ und Gesamtgenauigkeit. Insbesondere prüfen wir, ob die FNR jüngerer Personen sinkt; eine kleinere Lücke allein wäre kein ausreichender Erfolg, wenn nur die ältere Gruppe schlechter behandelt wird. Die Stärke $\mu$ und mögliche Versuche müssen dokumentiert werden; die Messdaten sollen nicht zur nachträglichen Auswahl einer günstig aussehenden Einstellung dienen.

**Die Ursache aus Schritt 2 bleibt bestehen:** Eine Fairness-Bedingung kann die Auswirkungen verzerrter Daten begrenzen, aber keine fehlenden Rückzahlungsnachweise rekonstruieren. Für einen realen Einsatz wären zusätzlich die Repräsentativität der Daten und die Verlässlichkeit der Labels zu prüfen; ein besserer Fairness-Messwert allein ist keine Freigabe des Kreditsystems.

**Grundlage:** [[WO-4/Vorlesung/W04_Vorlesung.pdf|W04-Vorlesung]], Folie 31 zur Wahl des Eingriffs nach Zugriff; Umsetzung im [[WO-4/Übung/W04_Arbeitsblatt_Uebung.html|W04-Arbeitsblatt]], Trainingsverfahren mit Fairness-Strafterm.

## Teil A – Schritt 5: Baseline

### Festgelegte Annahmen für die synthetischen Daten

Die Baseline beschreibt unsere Ausgangslage **vor dem Fairness-Eingriff**. Die Werte sind ein **informed guess für ein synthetisches Szenario**, keine belegten Daten über reale Altersgruppen. Wir setzen 85 % Rückzahlungsfähige in Gruppe a und 65 % in Gruppe b an. Der bewusst gewählte Abstand von 20 Prozentpunkten soll Unterschiede und Konflikte zwischen den Fairness-Metriken sichtbar machen. Die übrigen Einstellungen bleiben unverändert.

| Feld im Arbeitsblatt | Unsere Eingabe | Begründung und Bedeutung |
|---|---|---|
| Basisrate Gruppe a: 35–64 Jahre | **0,85** | Für das Szenario nehmen wir an, dass 85 % der Personen dieser Gruppe den Kredit vertragsgemäß zurückzahlen könnten. |
| Basisrate Gruppe b: 18–34 Jahre | **0,65** | Für das Szenario nehmen wir 65 % Rückzahlungsfähige an, etwa bei einer weniger gefestigten finanziellen Situation beim Berufseinstieg. Das ist eine didaktische Modellannahme, keine belegte Eigenschaft jüngerer Personen und keine automatische Folge einer kurzen Kredithistorie. |
| Proxy: Merkmal, das die Gruppe verrät | **Index kurzer Kredithistorie** | Hohe Werte stehen für eine kurze Kredithistorie. So passt die Benennung zum Generator, der für Gruppe b im Mittel höhere Proxy-Werte erzeugt. |
| Proxy-Stärke | **0,80** | Wir nehmen einen deutlichen Zusammenhang des Proxy-Merkmals mit der Altersgruppe an. Der Wert ist ein Generatorparameter, kein Korrelationskoeffizient und keine Angabe „80 % Erkennungsgenauigkeit“. |
| Fallzahl | **5.000** | Ergibt 3.500 Trainingsfälle und 1.500 zurückgehaltene Messfälle beim vorgegebenen 70/30-Split. |
| Anteil der Gruppe b | **0,40** | Im Erwartungswert sind 40 % der Fälle jüngere Antragstellende. Die tatsächlichen Gruppengrößen entstehen zufällig. |
| Label-Bias | **0,00** | Keine zusätzliche absichtliche Falschbeschriftung rückzahlungsfähiger jüngerer Personen; wir hatten in Schritt 2 Daten-Selektion, nicht falsche Labels, als Schwerpunkt gewählt. |
| Zufallswert (Seed) | **42** | Hält die Datenerzeugung reproduzierbar. |
| Fachmerkmal 1 | **Haushaltsüberschuss** | Synthetischer Indikator: höhere Werte stehen für mehr finanziellen Spielraum nach laufenden Ausgaben. |
| Fachmerkmal 2 | **Zahlungszuverlässigkeit** | Synthetischer Indikator: höhere Werte stehen für zuverlässigeres Zahlungsverhalten. |

Die beiden Fachmerkmale und der Proxy sind im Arbeitsblatt verrauschte numerische Größen. Die Bezeichnungen geben ihnen einen fachlichen Interpretationsrahmen; es werden weder echte Eurobeträge noch tatsächlich beobachtete Kreditverläufe erzeugt.

### Basisrate ist nicht Bewilligungsrate

Die **Basisrate** beschreibt die tatsächliche Rückzahlungsfähigkeit:

$$
P(Y=1\mid A=a)=0{,}85,\qquad
P(Y=1\mid A=b)=0{,}65.
$$

Die **Bewilligungsrate** $P(\hat{Y}=1\mid A=g)$ entsteht dagegen erst durch das Modell. Unsere Eingaben sind keine Zielquoten von 85 % beziehungsweise 65 % für Kreditzusagen. Die beobachteten Basisraten in der erzeugten Stichprobe können von den eingestellten Wahrscheinlichkeiten abweichen.

Bei ideal gleichen Fehlerraten gilt für jede Gruppe:

$$
P(\hat{Y}=1\mid A=g)
=\mathrm{TPR}\cdot P(Y=1\mid A=g)
+\mathrm{FPR}\cdot\left(1-P(Y=1\mid A=g)\right).
$$

Bei unseren ungleichen Basisraten können auch bei ideal gleichen TPR und FPR unterschiedliche Bewilligungsraten entstehen. Der Abstand beträgt dann $0{,}20\cdot|\mathrm{TPR}-\mathrm{FPR}|$. So lässt sich untersuchen, warum Equalized Odds und demografische Parität unterschiedliche Anforderungen stellen. Eine niedrigere Bewilligungsrate jüngerer Personen allein wäre noch kein Nachweis ungerechtfertigter Benachteiligung.

### Vorab festgelegte Einstellung für den Eingriff

Als Startwert war **$\mu=10$** vorgesehen; für die vorherige Auswertung war im Browser bereits **$\mu=30$** eingestellt. Für die neue Baseline wurde die Stärke **30** beibehalten. Die Daten wurden mit den geänderten Basisraten neu erzeugt; anschließend wurden Basismodell und Eingriff auf demselben neuen Datensatz ausgeführt. Ziel bleibt Equalized Odds, die Entscheidungsschwelle für beide Gruppen $0{,}50$. Die Auswertung bleibt explorativ, da weder Szenario noch Trainingsstärke unabhängig validiert wurden.

### Grenzen dieser Baseline

> [!warning] Keine künstliche Bestätigung unserer Bias-Hypothese
> Die unterschiedlichen eingestellten Basisraten sind bewusste Simulationsannahmen, keine empirisch nachgewiesenen Altersunterschiede. Sie sind für sich genommen kein Beleg für Diskriminierung. Ebenso beweisen 40 % jüngere Personen ohne Vergleich mit der Zielpopulation keine Unterrepräsentation.

Bei Label-Bias $0$ stimmen die erzeugten Altdatenlabels mit der synthetisch wahren Eignung überein. Der Generator erzeugt keine fehlenden Rückzahlungslabels, keine selektive historische Kreditvergabe und keinen zusätzlichen direkten Abzug vom Score jüngerer Personen. Bei ungleichen Basisraten kann der Gruppenproxy für die Vorhersage informativ werden. Ob dadurch ungerechtfertigt ungleiche Fehlerraten entstehen, ist anhand der Ergebnisse zu prüfen.

Wir prüfen daher **ergebnisoffen**, welche Unterschiede das Basismodell bei Auswahlrate, TPR und FPR zeigt und was der Trainingseingriff verändert. Eine geringe Ausgangslücke oder ein wirkungsloser beziehungsweise nachteiliger Eingriff wären ebenfalls zu berichten, statt die Annahmen nachträglich so zu verändern, dass die gewünschte Benachteiligung erscheint.

Bei unseren Basisraten und dem angenommenen Gruppenanteil erreicht die triviale Entscheidung „alle Kredite bewilligen“ im Erwartungswert $0{,}60\cdot0{,}85+0{,}40\cdot0{,}65=0{,}77$, also 77 % Genauigkeit. Die Gesamtgenauigkeit ist daher nur zusammen mit den Fehlerraten aussagekräftig. In den 1.500 Messfällen erwarten wir etwa 345 tatsächlich nicht rückzahlungsfähige Personen: etwa 135 aus Gruppe a und 210 aus Gruppe b. Die tatsächlichen Fallzahlen entstehen bei der Datenerzeugung.


## Teil B – Durchführung und Auswertung

### 1. Daten erzeugen und Ausgangslage festhalten

Die Daten wurden nach Änderung der Basisraten neu erzeugt: **5.000 Fälle**, Seed **42**, Basisraten a **0,85** und b **0,65**, Proxy-Stärke **0,80**, Anteil b **0,40** und Label-Bias **0**. Für den Vorher-nachher-Vergleich wurde derselbe neue Datensatz verwendet.

| Teilmenge | Gruppe a: 35–64 Jahre | Gruppe b: 18–34 Jahre | Gesamt |
|---|---:|---:|---:|
| Gesamter Datensatz | 2.981 | 2.019 | 5.000 |
| Training | 2.081 | 1.419 | 3.500 |
| Zurückgehaltene Messdaten | 900 | 600 | 1.500 |
| Rückzahlungsfähig in den Messdaten | 768 | 390 | 1.158 |
| Nicht rückzahlungsfähig in den Messdaten | 132 | 210 | 342 |

Die tatsächlichen Basisraten in den Messdaten betragen **85,33 %** in a und **65,00 %** in b. Die Abweichung von den eingestellten Wahrscheinlichkeiten entsteht durch die zufällige Stichprobenziehung.

### 2. Basismodell messen

Das Basismodell ist die vorgegebene logistische Regression ohne direktes Altersgruppenmerkmal. Es wurde auf den 3.500 Trainingsfällen trainiert und bei Schwelle $0{,}50$ auf den 1.500 Messfällen ausgewertet.

| Modell | Gruppe | Bewilligungsrate | TPR | FNR | FPR |
|---|---|---:|---:|---:|---:|
| Basismodell | a: 35–64 Jahre | 91,67 % | 97,14 % | 2,86 % | 59,85 % |
| Basismodell | b: 18–34 Jahre | 73,33 % | 90,51 % | 9,49 % | 41,43 % |
| Nach Trainingseingriff | a: 35–64 Jahre | 90,11 % | 95,83 % | 4,17 % | 56,82 % |
| Nach Trainingseingriff | b: 18–34 Jahre | 80,50 % | 94,87 % | 5,13 % | 53,81 % |

**Interpretation des Basismodells:** Jüngere Personen erhalten insgesamt seltener eine Zusage. Unter den tatsächlich Rückzahlungsfähigen werden 37 von 390 jüngeren Personen abgelehnt, gegenüber 22 von 768 älteren Personen. Die FNR-Lücke beträgt damit rund **6,62 Prozentpunkte**. Die größere Fairness-Lücke liegt bei der FPR: **18,42 Prozentpunkte** Unterschied. Trotz höherer Basisrate in a werden dort auch nicht rückzahlungsfähige Personen häufiger bewilligt. Die Messung belegt nicht die vermutete historische Daten-Selektion, da diese im Generator nicht nachgebildet wird.

### 3. Trainingseingriff anwenden

Ausgeführt wurde **Training mit Fairness-Bedingung, $\mu=30$, Ziel Equalized Odds**. Beide Modelle wurden auf denselben Messfällen bewertet; die Schwelle blieb für beide Altersgruppen bei $0{,}50$.

| Modell und Gruppe | Richtig bewilligt (TP) | Falsch abgelehnt (FN) | Falsch bewilligt (FP) | Richtig abgelehnt (TN) |
|---|---:|---:|---:|---:|
| Basismodell a | 746 | 22 | 79 | 53 |
| Basismodell b | 353 | 37 | 87 | 123 |
| Nach Eingriff a | 736 | 32 | 75 | 57 |
| Nach Eingriff b | 370 | 20 | 113 | 97 |

Die falschen Ablehnungen jüngerer Personen sinken **von 37 auf 20**, zugleich steigen ihre Fehlbewilligungen **von 87 auf 113**. In Gruppe a steigt die Zahl falscher Ablehnungen von 22 auf 32; die Fehlbewilligungen sinken von 79 auf 75. Die FPR nähern sich durch eine Verringerung in a und einen Anstieg in b an. Es handelt sich also nicht um eine Verbesserung sämtlicher Fehlerraten.

### 4. Preis und zweite Metrik bewerten

| Kennzahl | Basismodell | Nach Eingriff | Veränderung |
|---|---:|---:|---|
| Genauigkeit | 85,0000 % | 84,0000 % | −1,0000 Prozentpunkte |
| Differenz Chancen (Equalized Odds) | 0,1842 | 0,0301 | Verbesserung; Abstand sinkt um 15,41 Prozentpunkte |
| Differenz Parität | 0,1833 | 0,0961 | Verbesserung; Abstand sinkt um 8,72 Prozentpunkte |
| Verhältnis der Bewilligungsraten | 0,8000 | 0,8933 | Näher an 1 |
| Fehler insgesamt | 225 | 240 | 15 zusätzliche Fehler |

**Preis für das Arbeitsblatt:** Mit dem Trainingseingriff ($\mu=30$) sinkt die Differenz Chancen von 0,1842 auf 0,0301 und die Differenz Parität von 0,1833 auf 0,0961; die Genauigkeit fällt von 85,00 % auf 84,00 % (15 zusätzliche Fehler), während bei jüngeren Personen die falschen Ablehnungen von 37 auf 20 sinken und die Fehlbewilligungen von 87 auf 113 steigen.

**Bewertung:** Die Gruppenabstände werden kleiner; Equalized Odds ist nicht exakt erfüllt. Die Genauigkeit sinkt um einen Prozentpunkt. Nach dem Eingriff erhalten jüngere Rückzahlungsfähige häufiger einen Kredit, ältere Rückzahlungsfähige werden dagegen häufiger abgelehnt. Die FPR bleiben mit 56,82 % in a und 53,81 % in b hoch. Der Eingriff zeigt den Fairness-Trade-off, rechtfertigt aber keinen realen Einsatz.

### Grenzen der Messung

- **Nur synthetische Annahmen:** Keine empirische Aussage über reale Altersgruppen oder den tatsächlichen Einfluss einer kurzen Kredithistorie; historische Selektionslücken und fehlende Rückzahlungslabels werden nicht nachgebildet.
- **Begrenzte Zahl negativer Fälle:** Die FPR beruht auf 132 nicht rückzahlungsfähigen Personen in a und 210 in b. Ein einzelner Fall verändert sie um rund 0,76 beziehungsweise 0,48 Prozentpunkte. Auch die verbleibende EO-Lücke ist kein stabiler Fairness-Nachweis.
- **Ein Datensatz, ein Split:** Seed 42, keine Wiederholungen mit anderen Seeds und keine unabhängige Validierung der gewählten Stärke 30. Die neue Baseline wurde bewusst zur Untersuchung deutlicherer Unterschiede gewählt; die Auswertung ist explorativ.
- **Hohe Basisraten:** „Allen einen Kredit bewilligen“ erreicht auf diesen Messdaten $1158/1500=77{,}20\,\%$ Genauigkeit. Die Gesamtgenauigkeit muss daher zusammen mit den gruppenspezifischen Fehlerraten bewertet werden.
- **Labels im synthetischen Fall:** Bei Label-Bias 0 stimmen alle Altdatenlabels mit der wahren Eignung überein. Die optionale Auswertung gegen wahre Eignung liefert deshalb dieselben Kennzahlen; für reale abgelehnte Anträge wäre diese Wahrheit gerade nicht bekannt.
- **Keine umfassende Freigabe:** Gruppenfairness ersetzt weder individuelle Beurteilung noch eine Prüfung von Datengrundlage, wirtschaftlichen Schäden und rechtlichen Anforderungen.

### Vergleichsmatrix

G10 · T01 · Equalized Odds · Training · Differenz Chancen 0,18 → 0,03 · Genauigkeit 0,85 → 0,84

Die Matrixzeile verwendet die vorgegebene Rundung des Arbeitsblatts. Für die Interpretation gelten die genaueren Zahlen oben.

### Gesicherte Abgabedateien

- [[WO-4/Übung/W04_G10_Arbeitsblatt.pdf|Ausgefülltes Arbeitsblatt als PDF]] – vier A4-Seiten, einschließlich Teil A und B.
- [[WO-4/Übung/W04_G10_Ergebnis.txt|Ergebnisdatei für OPAL]] – direkter Export aus dem Arbeitsblatt.
- [[WO-4/Übung/W04_G10_Datensatz.csv|Datensatz für OPAL und W05]] – 5.000 Fälle, davon 3.500 Training und 1.500 Messung.
- [[WO-4/Übung/W04_G10_Arbeitsblatt_ausgefuellt.html|Statische ausgefüllte HTML-Fassung]] – Ergebnisse bleiben unabhängig vom lokalen Browserspeicher lesbar; das interaktive Original bleibt unverändert.

Die PDF-Seiten wurden visuell auf Vollständigkeit geprüft; Fallzahlen und Labels im CSV-Export stimmen mit dem ausgeführten Datensatz überein. Für OPAL wurde noch nichts hochgeladen.
