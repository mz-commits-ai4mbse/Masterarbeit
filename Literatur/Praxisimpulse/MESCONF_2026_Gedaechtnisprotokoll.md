# MESCONF 2026: Programmzusammenfassung und Gedächtnisprotokoll

## Dokumentstatus

**Veranstaltung:** MESCONF 2026, The Modeling Conference
**Datum:** 7. und 8. Mai 2026
**Ort:** Infineon Campeon, München
**Leitthema:** Ontologie als Gateway zur Realität
**Erstellt am:** 11. Mai 2026
**Autor des Gedächtnisprotokolls:** Moritz Diez

Dieses Dokument dient zur retrospektiven Dokumentation fachlicher Impulse, die während der MESCONF 2026 für die Konzeption der Masterarbeit aufgenommen wurden.

Die Zusammenfassungen der Programmpunkte basieren auf dem öffentlich verfügbaren Programm der MESCONF 2026. Persönliche Eindrücke und informelle Gespräche sind separat gekennzeichnet.

Die informellen Gespräche wurden nicht strukturiert erhoben, protokolliert oder systematisch ausgewertet. Aus ihnen werden daher keine empirischen Ergebnisse oder repräsentativen Aussagen über die Teilnehmer der Konferenz abgeleitet.

## 1. Relevanz für die Masterarbeit

Die MESCONF 2026 behandelte mehrere Fragestellungen, die unmittelbar an die Konzeption einer KI gestützten Informationsverarbeitungsarchitektur für MBSE und SysML v2 anschließen.

Besonders relevant waren:

1. explizite Semantik und Ontologien als Grundlage für verlässliche KI
2. Halluzinationen und semantische Inkonsistenzen bei KI gestütztem Engineering
3. Zusammenspiel von Ontologien und SysML v2
4. maschinenlesbare und semantisch interpretierbare Modelle
5. Guardrails für KI gestützte Modellierung
6. Human in the Loop und menschliche Entscheidungsverantwortung
7. Nachvollziehbarkeit von KI erzeugten beziehungsweise KI unterstützten Engineering Ergebnissen
8. Umgang mit Mehrdeutigkeit statt automatischer Annahmen
9. Einsatz von KI zur Unterstützung von Modellierungsaufgaben
10. Grenzen und Risiken einer zunehmenden Automatisierung

Die Konferenz diente damit als fachlicher Praxisimpuls für die weitere Konzeptentwicklung. Sie stellt keine eigenständige empirische Untersuchung im Rahmen der Masterarbeit dar.

# 2. Programmpunkte mit besonderer Relevanz

## 2.1 Ontologien für die Systemkommunikation

**Referent:** Prof. Dr. Steffen Staab, Universität Stuttgart
**Termin:** 7. Mai 2026, 09:30 bis 10:00 Uhr

Der Vortrag thematisierte Ontologien und Wissensgraphen als Mittel zur Definition und Weiterentwicklung von Datenmodellen.

Im Mittelpunkt stand die Frage, wie Ontologien zur Lösung von Problemen in der Kommunikation zwischen Systemen beziehungsweise ihren Teilsystemen eingesetzt werden können. Dabei wurde die Übertragbarkeit dieser Ansätze auf die Komposition komplexer Systeme diskutiert.

### Relevanz für die Masterarbeit

Der Programmpunkt stärkte die Betrachtung expliziter Semantik als mögliche Grundlage für die Verarbeitung heterogener Engineering Informationen.

## 2.2 Strategische Wissensarchitekturen als Basis für verlässliche KI im Engineering

**Referent:** Alexander Krumm, VPATH AI
**Termin:** 7. Mai 2026, 10:00 bis 10:20 Uhr

Der Vortrag behandelte den Einsatz von Wissensarchitekturen und Ontologien zur Unterstützung verlässlicher KI im Engineering.

Beschrieben wurde eine Kombination aus SysML Strukturen und domänenspezifischen Ontologien zur Schaffung einer expliziten semantischen Grundlage beziehungsweise Ground Truth.

Die semantische Absicherung wurde dabei insbesondere mit der Reduktion von Halluzinationen und der Möglichkeit logischer Schlussfolgerungen über Engineering Informationen verbunden.

### Relevanz für die Masterarbeit

Der Vortrag stellte einen unmittelbaren Praxisimpuls für die Untersuchung semantischer und ontologischer Leitplanken innerhalb einer KI gestützten Engineering Architektur dar.

## 2.3 Praktischer Einsatz von Ontologie bei Mercedes im Systems Engineering

**Referent:** Jochen Epple, Mercedes
**Termin:** 7. Mai 2026, 10:20 bis 10:40 Uhr

Der Vortrag stellte die Entwicklung eines Metamodells für Engineering Informationen bei Mercedes vor.

Ausgehend von der Beschreibung verschiedener Engineering Daten und ihrer Zusammenhänge entstand schrittweise ein dreischichtiges Metamodell mit Ontologieebene, Konzeptebene und Realisierungsebene.

Das Modell entwickelte sich von einem persönlichen Hilfsmittel zu einer fachbereichsübergreifend verwendeten Grundlage für Methodenentwicklung, Qualitätsmanagement, Engineering IT und Datenstrukturen für KI Modelle.

### Relevanz für die Masterarbeit

Der Vortrag zeigte einen industriellen Anwendungsfall, in dem explizite semantische Strukturen nicht nur zur Modellierung, sondern auch zur Integration und Verarbeitung heterogener Engineering Informationen genutzt werden.

## 2.4 Podiumsdiskussion: SysML, Ontologie und die Realität komplexer Systeme

**Teilnehmer:** Andreas Willert, Tim Weilkiens, Steffen Staab, Jochen Epple und Alexander Krumm
**Termin:** 7. Mai 2026, 11:00 bis 12:00 Uhr

Die Podiumsdiskussion behandelte die Frage, in welchem Verhältnis formale Modellierung, Semantik und Ontologien zueinander stehen.

Ausgangspunkt war unter anderem die Beobachtung, dass KI Systeme ohne ausreichend explizite Wirklichkeitsannahmen zu Inkonsistenzen und Halluzinationen neigen können.

Diskutiert wurde, ob die durch SysML bereitgestellte Semantik für die Modellierung komplexer Systeme ausreicht oder durch explizite Ontologien ergänzt werden sollte.

Ein weiterer Schwerpunkt lag auf der Frage, ob SysML und Ontologien konkurrierende Ansätze darstellen oder als komplementäre Bestandteile einer robusten Modellierungs und Informationsarchitektur betrachtet werden können.

### Relevanz für die Masterarbeit

Die Diskussion gab einen wesentlichen Impuls dafür, Ontologien nicht isoliert als alleinige Lösung zu betrachten, sondern ihre Rolle innerhalb einer umfassenderen Informationsverarbeitungsarchitektur zu untersuchen.

## 2.5 Open Space: SysML v2 trotz KI? Oder gerade wegen?

**Moderation:** Ruslan Bernijazov und Tim Weilkiens
**Termin:** 7. Mai 2026, 13:45 bis 14:30 Uhr

Der Open Space behandelte die Rolle formaler SysML v2 Modelle in einer zunehmend KI gestützten Engineering Umgebung.

Diskutiert wurden sowohl mögliche Vorteile als auch Risiken der KI gestützten Modellierung.

Zu den im Programm explizit genannten Risiken gehörten:

1. halluzinierte Modellinhalte
2. plausibel wirkende, fachlich jedoch falsche Modelle
3. Verlust von Modellierungswissen
4. unklare Verantwortlichkeit
5. Abhängigkeit von KI Anbietern und Trainingsdaten

Als zentrale Fragestellung wurde diskutiert, welche Guardrails und Human in the Loop Muster erforderlich sind, um KI sinnvoll in die SysML v2 Modellierung einzubinden.

Ein weiterer Aspekt war die mögliche Verschiebung der Tätigkeit von der manuellen Modellierung hin zu Kuratieren, Prüfen und Entscheiden.

### Relevanz für die Masterarbeit

Dieser Programmpunkt besitzt eine besonders hohe inhaltliche Nähe zur späteren Architektur des Turing Generators.

Die Fragestellungen zu Guardrails, Halluzinationen, menschlicher Verantwortung sowie der Verschiebung der Engineering Tätigkeit hin zu Prüfung und Entscheidung wurden bei der weiteren Konzeptentwicklung aufgegriffen.

## 2.6 SysML v2 auf der Apollo Mission

**Referenten:** Tim Weilkiens und Marco Bimbi
**Termin:** 8. Mai 2026, 09:15 bis 10:15 Uhr

Anhand eines umfangreichen SysML v2 Modells der Apollo 11 Mission wurde die maschinelle Verarbeitung formaler Modelle betrachtet.

Ein Schwerpunkt lag auf der Frage, welche Informationen eine Maschine zusätzlich zur reinen SysML v2 Syntax und Semantik benötigt, um die Bedeutung einzelner Modellelemente zuverlässig einordnen zu können.

Darüber hinaus wurde die standardisierte SysML v2 API zur maschinellen Nutzung des Modells und zur simulationsgestützten Weiterverarbeitung betrachtet.

### Relevanz für die Masterarbeit

Der Vortrag verdeutlichte die Bedeutung strukturierter, maschinenlesbarer Modelle und zusätzlicher semantischer Kontextinformation für automatisierte Verarbeitungsschritte.

## 2.7 KI unterstütztes MBSE mit SysML v2

**Referenten:** Patrick Weber und Peter Schedl, IBM
**Termin:** 8. Mai 2026, 10:45 bis 11:45 Uhr

Der Vortrag zeigte den Einsatz von KI innerhalb eines SysML v2 basierten MBSE Werkzeugs.

Beispielsweise können textuelle Anforderungen analysiert und daraus passende Modellelemente vorgeschlagen beziehungsweise generiert werden.

Der Ansatz kombiniert eine etablierte Engineering Methode mit KI Unterstützung, um Modellierungsaufwand zu reduzieren und den Einstieg in MBSE zu erleichtern.

### Relevanz für die Masterarbeit

Der Programmpunkt zeigte einen konkreten industriellen Ansatz zur KI unterstützten Ableitung von Modellelementen aus textuellen Engineering Informationen.

## 2.8 Qualifizierbare agentische MBSE Pipelines

**Einreicher:** Andreas Gehlsen, Cariad
**Termin:** 8. Mai 2026, 13:30 bis 14:15 Uhr

Der Open Space thematisierte die Nichtdeterministik von LLM Agenten im Kontext sicherheitskritischer Entwicklungsprozesse und der ISO 26262 Toolqualifikation.

Im Mittelpunkt stand die Frage, ob Architekturmechanismen entwickelt werden können, mit denen die Ergebnisse nichtdeterministischer Agenten kontrolliert beziehungsweise qualifizierbar verarbeitet werden können.

### Relevanz für die Masterarbeit

Der Programmpunkt berührt unmittelbar die Trennung zwischen probabilistischer KI Verarbeitung und kontrollierten beziehungsweise deterministischen Verarbeitungsschritten.

## 2.9 KI im Engineering und die Rolle menschlichen Fachwissens

Mehrere weitere Programmpunkte griffen ähnliche Fragestellungen auf.

Bei der Entwicklung von Hardware in the Loop Tests mit KI wurde ausdrücklich auf aktuelle Grenzen der KI und die weiterhin notwendige Rolle menschlichen Fachwissens eingegangen.

Im Workshop zu AI Powered Engineering wurden Datenqualität, Nachvollziehbarkeit, Integration in bestehende Prozesse und die sinnvolle Rolle des Menschen als Voraussetzungen für den praktischen KI Einsatz diskutiert.

Auch weitere Beiträge betrachteten KI als Unterstützung für Modellierungsaufgaben und nicht ausschließlich als autonome Modellierungsinstanz.

## 2.10 Kollaborativer KI Workflow und Umgang mit Mehrdeutigkeit

Ein weiterer Beitrag behandelte einen KI unterstützten Workflow für modellbasiertes Testen.

Eine zentrale Eigenschaft des vorgestellten Ansatzes war, dass die KI bei erkannten Mehrdeutigkeiten Fragen stellt, anstatt selbstständig Annahmen zu treffen.

Entscheidungen werden dokumentiert und mit den betroffenen Artefakten verknüpft. Dadurch entstehen nachvollziehbare Entscheidungsketten.

### Relevanz für die Masterarbeit

Dieser Ansatz weist deutliche konzeptionelle Parallelen zu Human Authority, Effective Intervention und Traceability innerhalb des Turing Generators auf.

# 3. Persönliche Eindrücke und informelle Fachgespräche

Die folgenden Punkte beruhen auf persönlichen Erinnerungen an Gespräche mit Teilnehmern der MESCONF 2026.

Sie wurden nicht strukturiert erhoben und stellen daher weder eine repräsentative Befragung noch einen nachgewiesenen Konsens der Teilnehmer dar.

Als wiederkehrende Gesprächsinhalte wurden wahrgenommen:

1. Halluzinationen wurden als wesentliches Risiko beim Einsatz generativer KI für Engineering und Modellierungsaufgaben betrachtet.
2. Ontologien wurden mehrfach als möglicher Ansatz diskutiert, um den semantischen Handlungsspielraum von KI Systemen zu begrenzen und Ergebnisse besser gegen fachliche Strukturen prüfen zu können.
3. In mehreren Gesprächen wurde betont, dass KI erzeugte Engineering Ergebnisse nicht ohne fachliche Kontrolle übernommen werden sollten.
4. Die Verantwortung für fachliche Entscheidungen wurde weiterhin beim menschlichen Engineer gesehen.
5. KI wurde überwiegend als unterstützendes Werkzeug für Analyse, Strukturierung, Vorschläge und Automatisierung diskutiert.
6. Die gezielte Übergabe unklarer oder widersprüchlicher Sachverhalte an einen menschlichen Entscheider wurde als sinnvoller Ansatz wahrgenommen.

Diese Eindrücke beeinflussten die weitere Konzeptentwicklung der Masterarbeit, wurden jedoch nicht als empirische Daten ausgewertet.

# 4. Einfluss auf die weitere Konzeptentwicklung

Aus der Konferenz wurden insbesondere folgende Fragestellungen für die weitere Arbeit mitgenommen:

1. Wie kann der Handlungsspielraum eines LLM durch explizite semantische Leitplanken begrenzt werden?
2. Welche Rolle können Ontologien innerhalb einer solchen Architektur übernehmen?
3. Wie kann verhindert werden, dass eine KI nicht belegte Engineering Informationen erzeugt und anschließend als Fakten weiterverarbeitet?
4. An welchen Stellen muss menschliche Engineering Authority erhalten bleiben?
5. Wie können Unsicherheit und Mehrdeutigkeit so dargestellt werden, dass eine gezielte menschliche Entscheidung möglich wird?
6. Wie kann die Herkunft einer Information über KI gestützte Verarbeitungsschritte hinweg nachvollziehbar bleiben?
7. Welche Verarbeitungsschritte sollten probabilistisch und welche deterministisch umgesetzt werden?
8. Welche Rolle spielt ein formal strukturiertes und maschinenlesbares Zielmodell wie SysML v2 für die weitere automatisierte Verarbeitung?

Diese Fragen wurden anschließend im Rahmen der Literaturarbeit, der Architekturkonzeption und der prototypischen Umsetzung weiter konkretisiert.

# 5. Verwendung innerhalb der Masterarbeit

Dieses Gedächtnisprotokoll dient ausschließlich zur Dokumentation der Entstehung und Motivation einzelner Konzeptentscheidungen.

Zulässige Verwendung:

1. Beschreibung der MESCONF als Praxisimpuls innerhalb des methodischen Vorgehens
2. Nachvollziehbarkeit der Entstehung bestimmter Untersuchungsfragen
3. Dokumentation persönlicher fachlicher Eindrücke
4. Kontext für die weitere Literaturrecherche und Konzeptentwicklung

Nicht zulässige Verwendung:

1. Beleg allgemeiner wissenschaftlicher Aussagen
2. Nachweis der Wirksamkeit von Ontologien gegen Halluzinationen
3. Behauptung eines repräsentativen Teilnehmerkonsenses
4. Ersatz wissenschaftlicher Literatur
5. Darstellung der informellen Gespräche als Interviewstudie oder empirische Erhebung

Für allgemeine Aussagen werden weiterhin geeignete wissenschaftliche Literaturquellen verwendet.