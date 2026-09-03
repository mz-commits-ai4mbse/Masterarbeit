# Thesis Writing and Evidence Rules

## 1. Zweck dieses Dokuments

Dieses Dokument definiert die verbindlichen Regeln für Struktur, Argumentation, Quellenverwendung, Darstellung und Bearbeitung der Masterarbeit.

Es dient als Single Source of Truth für die weitere Ausarbeitung der schriftlichen Arbeit. Änderungen an diesen Regeln erfolgen nur nach expliziter Abstimmung.

Die Masterarbeit untersucht primär die Konzeption einer Informationsverarbeitungsarchitektur zur KI gestützten Überführung heterogener Bestandsinformationen in SysML v2.

Der implementierte Turing Generator dient als prototypische Realisierung und als empirische Grundlage zur Prüfung des entwickelten Konzepts. Die Arbeit ist deshalb keine Produktbeschreibung und kein Entwicklungsbericht.

## 2. Wissenschaftlicher Fokus

Die zentrale Perspektive der Arbeit lautet:

Welche Verarbeitungsschritte, Verantwortlichkeiten, Kontrollmechanismen und Informationsstrukturen sind erforderlich, um ein KI gestütztes System zur kontrollierten Überführung heterogener Bestandsinformationen in SysML v2 zu konzipieren?

Die Beschreibung der prototypischen Umsetzung dient dazu, das entwickelte Konzept nachvollziehbar zu konkretisieren und zu untersuchen.

Die Gliederung der technischen Kapitel orientiert sich daher nicht an Python Modulen, Repository Paketen oder der zeitlichen Reihenfolge der Implementierung.

Der rote Faden folgt stattdessen dem Weg der Engineering Information durch das System und den fachlichen Überlegungen hinter den jeweiligen Verarbeitungsschritten.

## 3. Sprachliche Regeln

Die Arbeit wird auf Deutsch verfasst.

Der Schreibstil ist sachlich, präzise und technisch verständlich. Formulierungen sollen dem persönlichen Schreibstil des Autors entsprechen und nicht unnötig akademisch oder künstlich wirken.

Im Fließtext gelten folgende verbindliche Regeln:

1. Gedankenstriche werden nicht als Satzzeichen verwendet.
2. Formulierungen mit dem unpersönlichen Pronomen „man“ werden vermieden.
3. Unnötige Füllformulierungen werden vermieden.
4. Aussagen werden möglichst konkret formuliert.
5. Wiederholungen werden vermieden.
6. Abkürzungen werden bei der ersten Verwendung eingeführt.
7. Begriffe werden innerhalb der Arbeit konsistent verwendet.
8. Die Beschreibung des Prototyps erfolgt nicht als chronologischer Erfahrungsbericht.

## 4. Wissenschaftliche Aussagen und Quellen

Allgemeine wissenschaftliche Aussagen und Thesen benötigen einen geeigneten Literaturbeleg.

Die Literaturquelle muss im Repository der Masterarbeit vorhanden sein und inhaltlich geprüft worden sein.

Eine Quelle darf nur verwendet werden, wenn ihr Inhalt die jeweilige Aussage tatsächlich unterstützt.

Ein passender Titel, Abstract oder Dateiname allein reicht nicht als Beleg.

Falls für eine benötigte allgemeine Aussage keine geeignete und geprüfte Literaturquelle im Repository vorhanden ist, wird die Aussage zunächst als Quellenlücke behandelt.

Eine solche Aussage darf erst in den finalen Thesis Text aufgenommen werden, nachdem eine geeignete Quelle ergänzt und geprüft wurde.

Die Zitation erfolgt im IEEE Stil mit nummerierten Verweisen in eckigen Klammern, beispielsweise:

[1]

Mehrere Quellen werden entsprechend dem IEEE Stil angegeben.

## 5. Unterschied zwischen Literatur und Projektevidenz

Literatur und Projektevidenz erfüllen unterschiedliche Funktionen.

### 5.1 Literatur

Literatur dient insbesondere zur Begründung von:

1. allgemeinen wissenschaftlichen Aussagen
2. methodischen Entscheidungen
3. theoretischen Konzepten
4. bekannten Problemen und Lösungsansätzen
5. der Forschungslücke
6. der Einordnung der Ergebnisse

### 5.2 Projektevidenz

Das Engineering Repository, akzeptierte ADRs, Testprotokolle, CATIA Modelle, Versuchsergebnisse und andere Projektartefakte dienen als Evidenz für:

1. tatsächlich getroffene Architekturentscheidungen
2. tatsächlich implementierte Funktionen
3. beobachtete Fehlerbilder
4. durchgeführte Tests
5. konkrete Versuchsergebnisse
6. Traceability innerhalb des entwickelten Systems

Projektartefakte ersetzen keine wissenschaftliche Literatur für allgemeine Aussagen.

Ein empirisches Ergebnis des Prototyps benötigt keinen externen Literaturbeleg, wenn ausschließlich das beobachtete Ergebnis beschrieben wird.

Eine Verallgemeinerung aus diesem Ergebnis benötigt dagegen Literatur oder muss ausdrücklich auf den untersuchten Proof of Concept begrenzt werden.

## 6. Architekturentscheidungen

Die im Engineering Repository dokumentierten Architecture Decision Records bilden die nachvollziehbare Entwicklung des Architekturkonzepts ab.

Sie dienen insbesondere zur Rekonstruktion von:

1. Ausgangsproblemen
2. untersuchten Alternativen
3. getroffenen Entscheidungen
4. verworfenen Ansätzen
5. empirisch ausgelösten Architekturkorrekturen

Die Thesis übernimmt ADR Inhalte nicht als interne Dokumentation.

Stattdessen werden die zugrunde liegenden fachlichen Überlegungen wissenschaftlich eingeordnet und in die Argumentation der Arbeit integriert.

## 7. Forschungsfragen

Die mit dem betreuenden Professor abgestimmten Forschungsfragen bleiben unverändert.

Eine nachträgliche Anpassung der Forschungsfragen an die Ergebnisse der prototypischen Untersuchung erfolgt nicht.

Falls die Untersuchung zeigt, dass eine Forschungsfrage zu eng formuliert ist oder eine ursprünglich angenommene Lösung nicht hinreichend ist, wird dies bei der Beantwortung der jeweiligen Forschungsfrage diskutiert.

Eine mögliche Präzisierung wird als Ergebnis der Arbeit vorgeschlagen und begründet.

## 8. Literaturrecherche

Die durchgeführte Literaturrecherche wird im Haupttext methodisch nachvollziehbar, aber bewusst kompakt dargestellt.

Im Haupttext werden insbesondere beschrieben:

1. Ziel der Recherche
2. Suchstrategie
3. verwendete Datenbanken
4. Auswahl und Ausschlusslogik
5. grundlegender Screeningprozess
6. Ergebnis der Recherche
7. Ableitung des Forschungsbedarfs

Detaillierte Zwischenergebnisse dienen überwiegend dem methodischen Nachweis und werden in den Anhang verschoben.

Hierzu können insbesondere gehören:

1. vollständige Suchstrings
2. detaillierte Screeningtabellen
3. umfangreiche Trefferlisten
4. Zwischenstände der Auswahl
5. zusätzliche Dokumentation des Rechercheprozesses
6. PRISMA Darstellung, sofern diese nicht unmittelbar für die Argumentation im Haupttext benötigt wird

Der Haupttext enthält primär Argumentation.

Der Anhang enthält ergänzenden Nachweis.

## 9. Tabellen und Abbildungen

Tabellen und Abbildungen werden nur verwendet, wenn sie mindestens eine der folgenden Funktionen erfüllen:

1. unmittelbare Begründung einer Aussage
2. verständlichere Darstellung eines komplexen Zusammenhangs
3. exemplarische Erläuterung eines Sachverhalts
4. notwendige Darstellung eines Ergebnisses

Darstellungen, die ausschließlich dem Nachweis dienen, werden nach Möglichkeit in den Anhang verschoben.

### 9.1 Tabellen

Alle Tabellen verwenden ein einheitliches Design.

Tabellen erhalten immer eine nummerierte Überschrift oberhalb der Tabelle.

Jede Tabelle erhält ein eindeutiges Label.

Jede Tabelle wird im Fließtext ausdrücklich referenziert.

Tabellen werden nicht eingefügt, ohne dass ihr Inhalt im Text eingeordnet wird.

### 9.2 Abbildungen

Abbildungen erhalten immer eine nummerierte Bildunterschrift unterhalb der Abbildung.

Jede Abbildung erhält ein eindeutiges Label.

Jede Abbildung wird im Fließtext ausdrücklich referenziert.

Abbildungen werden nicht ausschließlich zur optischen Auflockerung verwendet.

## 10. Trennung von Methodik, Konzept, Umsetzung, Ergebnis und Diskussion

Die Arbeit trennt folgende Ebenen konsequent voneinander.

### Methodik

Die Methodik beschreibt, wie das Architekturkonzept entwickelt, begründet und überprüft wird.

Hierzu gehören unter anderem:

1. Forschungsdesign
2. Literaturmethodik
3. modellbasierte Architekturentwicklung
4. RFL beziehungsweise RFLP basierte Strukturierung im tatsächlich angewandten Umfang
5. Umgang mit Traceability und Provenance
6. Human Authority
7. Multi Persona Verarbeitung
8. Varianz und Stabilitätsbetrachtung
9. Entwicklungsstrategie des Proof of Concept
10. Verifikations und Validierungsstrategie

### Architekturkonzept

Das Architekturkapitel beschreibt das resultierende Konzept.

Im Mittelpunkt stehen:

1. erforderliche Verarbeitungsschritte
2. Verantwortlichkeiten
3. Informationsobjekte
4. Kontrollgrenzen
5. Schnittstellen
6. Human Authority
7. funktionale und logische Struktur
8. Zusammenspiel der Subsysteme

### Prototypische Realisierung

Die prototypische Realisierung beschreibt, wie das entwickelte Konzept technisch umgesetzt wurde.

Der Informationsfluss bildet den roten Faden.

Die Darstellung folgt daher grundsätzlich dem Weg:

Engineering Source
→ Source Context und Preparation
→ Source Grounded Evidence
→ Interpretation
→ Consensus und Variance
→ Human Engineering Review
→ Approved Engineering Information
→ Project Fit
→ Model Candidate Derivation
→ Human Model und Placement Authority
→ Internal Engineering Model Assembly
→ SysML v2 Generation
→ Generated Artifact Validation
→ Final Human Model Review
→ Publication Gate

Technische Module, Klassen und Dateien werden nur genannt, wenn sie zum Verständnis oder zur Nachvollziehbarkeit der prototypischen Realisierung erforderlich sind.

### Verifikation und Validierung

Dieses Kapitel beschreibt, wie das Konzept und seine prototypische Realisierung überprüft wurden.

### Ergebnisse

Das Ergebniskapitel dokumentiert ausschließlich die beobachteten Ergebnisse.

Interpretationen und Bewertungen werden vermieden.

### Diskussion

Die Diskussion interpretiert die Ergebnisse.

Hier werden insbesondere behandelt:

1. Bedeutung der Ergebnisse
2. Grenzen des untersuchten Ansatzes
3. technische Validität gegenüber fachlicher Korrektheit
4. Human Authority
5. Nutzen und Grenzen der Multi Persona Verarbeitung
6. Rolle semantischer und ontologischer Leitplanken
7. Traceability und Provenance
8. Multi Source Verarbeitung
9. Tokenverbrauch
10. Kosten
11. Laufzeit und Performance
12. Effizienz
13. Übertragbarkeit
14. Production Readiness
15. LLM unterstützte Systementwicklung
16. Grenzen des Proof of Concept

Die Forschungsfragen werden auf Basis der Ergebnisse und der anschließenden Diskussion explizit beantwortet.

## 11. Zielstruktur der Arbeit

Die derzeitige Zielstruktur umfasst neun Hauptkapitel.

### Kapitel 1: Einleitung

Motivation
Problemstellung
Zielsetzung
Forschungsfragen
Scope
Aufbau der Arbeit

### Kapitel 2: Theoretischer Hintergrund und Stand der Technik

MBSE und SysML v2
Architecture as Code und verwandte Konzepte
Brownfield Engineering und heterogene Bestandsinformationen
KI und Large Language Models im Systems Engineering
Human in the Loop und Human Authority
Traceability und Provenance
semantische Normalisierung und Ontologien
SysML v2 Tooling und Validierung
Literaturrecherche und Forschungslücke

### Kapitel 3: Methodisches Vorgehen

Forschungsdesign
Literaturmethodik
Ableitung des Forschungsbedarfs
Architekturentwicklung
RFL beziehungsweise RFLP Vorgehen
Traceability und Provenance Konzept
Human Authority Konzept
Multi Persona Konzept
Varianz und Stabilitätsbetrachtung
Entwicklungsstrategie des Proof of Concept
Verifikations und Validierungsmethodik
Claim und Evidence Strategie

### Kapitel 4: Konzeption der Informationsverarbeitungsarchitektur

Anforderungen und Systemgrenzen
Architekturprinzipien
Systemkontext
Verarbeitungslogik
funktionale Architektur
logische Architektur
Subsystemstruktur
Informationsobjekte
Schnittstellen und Informationsflüsse
Human Authority
Traceability
Scope Grenzen

### Kapitel 5: Prototypische Realisierung

Realisierung entlang des Engineering Information Flow.

### Kapitel 6: Verifikation und Validierung

Teststrategie
Testhierarchie
formative Untersuchungen
Gate 3
WP 12
Multi Source Untersuchung
Regressionstests
SysML v2 Validierung
SYSIDE Validierung
Human Review

### Kapitel 7: Ergebnisse

Ergebnisse der durchgeführten Untersuchungen ohne Interpretation.

### Kapitel 8: Diskussion und Beantwortung der Forschungsfragen

Interpretation der Ergebnisse
Limitationen
Effizienz und Performance
Tokenverbrauch und Kosten
Übertragbarkeit
technische Validität und Engineering Correctness
LLM unterstützte Entwicklung
Beantwortung der Hauptforschungsfrage
Beantwortung der Teilforschungsfragen
gegebenenfalls begründete Präzisierung der Fragestellungen

### Kapitel 9: Fazit und Ausblick

Zusammenfassung
Beitrag der Arbeit
wesentliche Limitationen
Ausblick

## 12. Arbeitsablauf für neue Thesis Abschnitte

Für jeden wesentlichen Abschnitt gilt grundsätzlich folgende Reihenfolge.

### Schritt 1: Ziel des Abschnitts festlegen

Es wird festgelegt, welche Funktion der Abschnitt innerhalb der Gesamtargumentation erfüllt.

### Schritt 2: Relevante Projektevidenz identifizieren

Relevante CATIA Modelle, ADRs, Checkpoints, Tests, Versuchsergebnisse und Implementierungsartefakte werden identifiziert.

### Schritt 3: Claims definieren

Die Aussagen, die der Abschnitt tragen soll, werden vor dem Schreiben des Fließtexts festgelegt.

### Schritt 4: Literatur zuordnen

Jeder allgemeine wissenschaftliche Claim wird einer geprüften Literaturquelle aus dem Repository zugeordnet.

Quellenlücken werden explizit markiert.

### Schritt 5: Evidenzgrenzen festlegen

Für jeden Claim wird unterschieden zwischen:

Literaturbeleg
Architekturevidenz
Implementierungsevidenz
empirischem Ergebnis
eigener Interpretation

### Schritt 6: Abschnitt strukturieren

Die Argumentationsfolge wird vor der Ausformulierung festgelegt.

### Schritt 7: Fließtext erstellen

Erst nach Claim und Evidence Mapping wird der eigentliche Thesis Text erstellt.

### Schritt 8: Tabellen und Abbildungen auswählen

Eine Tabelle oder Abbildung wird nur aufgenommen, wenn sie für Argumentation, Verständnis oder exemplarische Darstellung erforderlich ist.

Reine Nachweise werden in den Anhang verschoben.

### Schritt 9: Wissenschaftliche QA

Prüfung auf:

1. unbelegte allgemeine Aussagen
2. fehlerhafte oder zu weitgehende Quelleninterpretation
3. Vermischung von Ergebnis und Diskussion
4. unklare Evidenzgrenzen
5. Widersprüche zu akzeptierter Engineering Reality

### Schritt 10: Sprachliche QA

Prüfung auf:

1. Gedankenstriche
2. Formulierungen mit „man“
3. unnötig künstliche Sprache
4. Wiederholungen
5. inkonsistente Terminologie
6. zu lange oder unnötig komplexe Sätze

### Schritt 11: Darstellungs QA

Prüfung auf:

1. einheitliche Tabellen
2. korrekte Tabellenüberschriften
3. korrekte Abbildungsunterschriften
4. Nummerierung
5. Labels
6. Referenzierung im Fließtext
7. sinnvolle Platzierung im Haupttext oder Anhang

### Schritt 12: Human Review

Der Autor prüft und akzeptiert den Abschnitt.

Eine fachliche oder strukturelle Änderung gilt erst nach dieser Prüfung als akzeptierter Thesis Stand.

### Schritt 13: Git Prüfung

Vor einem Commit werden mindestens ausgeführt:

```text
git status --short
git diff --check
git diff
```

Es werden ausschließlich die für den jeweiligen Arbeitsschritt vorgesehenen Dateien gestaged.

`git add .` wird vermieden.

### Schritt 14: Commit und GitHub Sync

Nach Akzeptanz wird der Arbeitsschritt committed und nach `origin/main` beziehungsweise über den jeweils vereinbarten Arbeitsbranch nach GitHub übertragen.

### Schritt 15: Overleaf Sync

Der akzeptierte Stand wird anschließend nach `overleaf/main` übertragen.

GitHub und Overleaf sollen nach einem abgeschlossenen Arbeitsschritt denselben akzeptierten Commit enthalten.

### Schritt 16: Compile Prüfung

Nach relevanten Änderungen an LaTeX Dateien wird der Stand in der Overleaf Umgebung kompiliert.

Ein Arbeitsschritt mit LaTeX Änderungen gilt erst dann als technisch abgeschlossen, wenn der Overleaf Compile erfolgreich ist oder ein bekanntes und ausdrücklich akzeptiertes Problem dokumentiert wurde.

## 13. Git und Repository Regeln

Der Branch `main` repräsentiert den akzeptierten Thesis Stand.

GitHub und Overleaf spiegeln denselben akzeptierten Stand.

Vor Änderungen wird geprüft, ob beide Remotes synchron sind.

Breites oder unspezifisches Staging wird vermieden.

Bestehende Dateien werden nicht ohne nachvollziehbaren Grund gelöscht oder vollständig ersetzt.

Größere Strukturänderungen werden vor ihrer Umsetzung abgestimmt.

Literaturquellen im Repository werden nicht verändert.

## 14. LaTeX und Layout Regeln

Die Arbeit verwendet grundsätzlich:

12 pt Schriftgröße
A4 Format
anderthalbfachen Zeilenabstand
numerische Zitation nach IEEE
einheitliche Tabellenformatierung
nummerierte Tabellen und Abbildungen
Tabellenüberschriften oberhalb der Tabelle
Abbildungsunterschriften unterhalb der Abbildung

Schusterjungen und Hurenkinder sollen durch geeignete LaTeX Einstellungen soweit wie möglich vermieden werden.

Layoutänderungen werden zentral definiert und nicht individuell innerhalb einzelner Kapitel umgesetzt.

## 15. Autoritätsreihenfolge

Für Aussagen über das entwickelte System gilt folgende technische Autoritätsreihenfolge:

1. akzeptiertes CATIA SysML v2 Modell für die Engineering Architektur
2. akzeptierter Stand des SysMLv2 Generator Repository für die tatsächliche Implementierung
3. akzeptierte ADRs, Checkpoints und Collaboration SSOT
4. dokumentierte Tests und Versuchsergebnisse
5. weitere Projektartefakte
6. Chatverläufe

Für allgemeine wissenschaftliche Aussagen besitzt die geprüfte Fachliteratur die wissenschaftliche Belegfunktion.

Die schriftliche Thesis synthetisiert diese Quellen, ersetzt ihre jeweilige Autorität jedoch nicht.

## 16. Änderungsprinzip

Dieses Dokument wird nur geändert, wenn sich eine neue verbindliche Regel für die Thesis Arbeit ergibt oder eine bestehende Regel gemeinsam angepasst wurde.

Änderungen werden versioniert und gemeinsam mit dem Thesis Repository gespeichert.
