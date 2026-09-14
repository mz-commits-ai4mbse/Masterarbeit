# Thesis Writing SSOT
## Stand: 14.09.2026

Dieses Dokument dient als Übergabe- und Planungs-SSOT für die weitere
Ausarbeitung der Masterarbeit.

WICHTIG:
- Die eigentlichen Kapiteldateien unter `Kapitel/` sind die Autorität für den
  bereits ausformulierten Thesis-Text.
- Dieses Dokument beschreibt Status, wissenschaftliche Positionierung,
  offene Arbeiten und die weitere Schreibstrategie.
- Historische Entwürfe unter `Archive/` dürfen zur Orientierung verwendet
  werden, sind aber keine Textautorität.
- `collaboration/` aus dem Turing-Generator-Repository ist kein
  wissenschaftlicher Evidenzbestand der Masterarbeit. Diese Dateien wurden
  primär zur Zusammenarbeit mit ChatGPT erzeugt und dürfen in der Thesis
  nicht als wissenschaftliche oder projektbezogene Evidenz zitiert werden.
- Das finale CATIA-Modell und das finale Turing-Generator-Repository werden
  separat als technische Artefakte mit der Arbeit abgegeben.

---

# 1. Wissenschaftliche Positionierung

Die Arbeit untersucht ein Architekturkonzept für die kontrollierte
KI-gestützte Transformation heterogener Engineering-Bestandsinformation
in SysML v2.

Der wissenschaftliche Beitrag ist nicht der Turing Generator als Software.
Der Turing Generator ist die prototypische Instanziierung und ein
Untersuchungsinstrument für das Architekturkonzept.

Zentrale Problemformulierung:

> Die zentrale Herausforderung bei der KI-gestützten Erzeugung von
> Engineering-Modellen liegt nicht in der Generierung syntaktisch plausiblen
> SysML-v2-Codes, sondern in der kontrollierten Herleitung der darin
> repräsentierten Engineering-Information.

Die Arbeit behauptet NICHT:
- dass KI-Modellierung schneller als manuelle Modellierung ist;
- dass der Ansatz wirtschaftlicher ist;
- dass Produktionsreife erreicht wurde;
- dass sämtliche real relevanten Engineering-Informationen automatisch
  identifiziert werden;
- dass technische SysML-v2-Validität fachliche Korrektheit beweist;
- dass Persona-Konsens fachliche Wahrheit beweist;
- dass Testanzahl ein Qualitätsmaß der Architektur darstellt.

Die industrielle Motivation darf jedoch beinhalten, dass Unternehmen einen
stärker unterstützten Transformationspfad wünschen, um Initial- und
wiederkehrende Modellierungs-/Integrationsaufwände zu reduzieren. Die
Masterarbeit untersucht, welche architektonischen Fragestellungen und
Bedingungen bei einem solchen Ansatz auftreten. Sie weist die erwartete
Aufwandsreduktion selbst nicht empirisch nach.

---

# 2. Forschungsfragen

Hauptforschungsfrage:

> Wie muss eine Informationsverarbeitungsarchitektur gestaltet sein, um
> heterogene Bestandsdaten durch ein systematisches Bewertungs- und
> Synthese-Framework sicher in valide SysML v2 Strukturen zu überführen?

TF1:

> Welche Anforderungen (Constraints) müssen an die Eingangsdaten gestellt
> werden, damit ein KI-Agent zuverlässig arbeiten kann?

TF2:

> Wie wird der Ingenieur als Kontrollorgan in die Architektur eingebunden,
> um bei detektierten Mehrdeutigkeiten effektiv einzugreifen?

TF3:

> Wie müssen die ontologischen Regeln („Leitplanken“) gestaltet sein, gegen
> die die Eingangsdaten geprüft werden, um die Plug & Play-Fähigkeit der
> resultierenden SysML v2 Modelle (Modularität) zu gewährleisten?

Die Forschungsfragen werden in Kapitel 2 aus Forschungsstand und
Forschungsbedarf hergeleitet.

Kapitel 3 beschreibt das methodische Vorgehen, beantwortet die Fragen aber
nicht erneut.

Die explizite Beantwortung erfolgt in Kapitel 8.

---

# 3. Status Kapitel 1

Kapitel 1 ist inhaltlich ausgearbeitet.

Geplante/finalisierte Struktur:

## 1.1 Ausgangssituation und Motivation
Inhalt:
- MBSE als attraktives Zielbild.
- MBSE-Einführung verursacht selbst organisatorischen, technischen und
  personellen Aufwand.
- Hoher Up-front-Aufwand kann insbesondere für KMU eine Einführungshürde
  darstellen.
- Brownfield-Unternehmen starten nicht mit leerem Informationsbestand.
- Legacy-Artefakte müssen berücksichtigt werden.
- In der industriellen Themenfindung wurde wiederkehrender Modellierungs-
  und Integrationsaufwand bei sukzessiver Erweiterung von Modellen als
  praktische Hürde wahrgenommen.
- KI-Unterstützung wird als möglicher Ansatz motiviert, aber keine
  Produktivitäts- oder Kosteneinsparung behauptet.

Wichtige Quellen:
- `Hendriks2022`
- `Campo2023`
- `Call2022`
- `Henderson2024`
- `Nanfuka2023`

## 1.2 Problemstellung
Inhalt:
- Brownfield-Transformation ist keine reine Formatkonvertierung.
- Engineering Meaning muss aus heterogenen Quellen erschlossen werden.
- Legacy-Artefakte können im normalen Entwicklungs-/PLM-Prozess
  weiterverändert werden.
- Bereits überführte Modellinformation kann dadurch erneut betroffen sein.
- Wiederkehrender Modellierungs- und Integrationsaufwand wird als
  industrielle Problemwahrnehmung beschrieben, NICHT als empirisch
  bestimmter allgemeiner Aufwandsverlauf.
- LLMs eröffnen semantische Verarbeitungsmöglichkeiten, erzeugen aber ein
  neues Kontrollproblem.
- Kernproblem ist die kontrollierte Herleitung der Engineering-Information.

Abbildung:
`Masterarbeit/Graphiken/1.2_Problemdarstellung.png`

Die Abbildung ist eine professionelle Neudarstellung einer Skizze aus der
industriellen Themenfindung mit Rücker + Schindele.

Interpretation:
- violetter wellenförmiger Verlauf = wahrgenommener wiederkehrender
  Modellierungs-/Integrationsaufwand bei sukzessiver Modellbildung;
- grüner Verlauf = industrielles Zielbild einer stärker unterstützten
  Transformation;
- KEINE empirische Aufwandserhebung;
- KEIN Nachweis, dass der Ansatz der Masterarbeit schneller ist.

## 1.3 Zielsetzung
Ziel:
Entwicklung und Untersuchung eines Architekturkonzepts für kontrollierte
KI-gestützte Transformation heterogener Engineering-Bestandsinformation
nach SysML v2.

Turing Generator:
prototypische Instanziierung / Untersuchungsinstrument, nicht das
wissenschaftliche Hauptergebnis.

## 1.4 Abgrenzung
Explizit ausgeschlossen:
- Physical-/Deployment-Architektur;
- produktionsreifes System;
- vollständige autonome Modellgenerierung;
- Verarbeitung beliebiger Engineering-Artefakte;
- automatische vollständige PLM-/Modell-Synchronisation;
- vollständige Change-Impact-Analyse;
- Produktivitäts-/Kosten-/Geschwindigkeitsnachweis;
- Schluss von technischer SysML-Gültigkeit auf Engineering Correctness.

## 1.5 Aufbau der Arbeit
Kurzbeschreibung der Kapitel 2–9.

---

# 4. Status Kapitel 2

## 2.1 MBSE, SysML v2 und maschinenverarbeitbare Modelle
Text ist ausgearbeitet.

Roter Faden:

Systems Engineering
→ MBSE
→ explizite und verknüpfte Engineering-Information
→ SysML v2
→ textuelle und maschinenverarbeitbare Modellrepräsentation
→ Everything as Code / Architecture as Code.

Wichtige Quellen:
- `Hendriks2022`
- `SysMLv2`
- `Teodorov2025`
- `Stirbu2022`
- `Bucaioni2025`

Kein unnötiger SE-Grundlagenblock.
Kein Transformer-/KI-Grundlagenkurs.

Grafiken im State of the Art:
Nicht künstlich selbst erzeugen, nur um Text zu illustrieren.
Wenn eine Grundlagen-/SotA-Grafik sinnvoll ist, bevorzugt aus
wissenschaftlicher Literatur beziehungsweise als klar zitierte Adaption
verwenden.

## 2.2 Brownfield Engineering und heterogene Bestandsinformation
Text ist ausgearbeitet.

Roter Faden:
- Brownfield besitzt bereits Systeme, Artefakte und historische Information.
- Transformation zu MBSE beinhaltet Informationserschließung.
- Heterogenität betrifft nicht nur Datenformate, sondern auch Semantik.
- Legacy-Information und daraus abgeleitete Modelle können sich
  weiterentwickeln.
- formale Modelltransformation ≠ Interpretation un-/semistrukturierter
  Legacy-Information;
- bei Legacy-Dokumenten muss Engineering Meaning vor der formalen
  Zieltransformation erschlossen werden.

Wichtige Quellen:
- `Maheshwari2018`
- `Henderson2024`
- `Legat2014`
- `Kapos.2014`
- `Li.2022`
- `Zhou.2024`
- `Meyers.2013`

## 2.3 NÄCHSTER SCHRITT
Arbeitsname:

### Literaturbasierte Strukturierung des Transformationsproblems

WICHTIG:
Der bisher diskutierte generische Abschnitt
„KI- und LLM-gestützte Informationsverarbeitung“ wird NICHT unmittelbar
als 2.3 geschrieben.

Stattdessen soll zunächst das Drei-Schichten-Modell eingeführt werden, das
bereits in der initialen Literaturarbeit und in der Kick-off-Präsentation
verwendet wurde.

Der nächste Chat muss deshalb ZUERST:
1. die originale Kick-off-Präsentation beziehungsweise die dort verwendete
   Darstellung prüfen;
2. die exakten Bezeichnungen der drei Schichten übernehmen;
3. nachvollziehen, aus welchen Literaturquellen diese Strukturierung
   ursprünglich abgeleitet wurde;
4. sicherstellen, dass der aktuelle Text der ursprünglichen Kick-off-Logik
   nicht widerspricht.

Erinnerte Einordnung:
Die damalige Struktur hatte eine Data-/Process-/Knowledge-Perspektive.
Diese Erinnerung ist vor dem Schreiben gegen die originale
Kick-off-Unterlage zu verifizieren und darf nicht ungeprüft als exakte
Terminologie übernommen werden.

Funktion von 2.3:
- aus 2.1 und 2.2 eine erste literaturbasierte Struktur des
  Transformationsproblems entwickeln;
- zeigen, dass die Transformation mehrere gekoppelte Ebenen betrifft;
- die drei Schichten nicht als Ergebnis der späteren Architektur darstellen,
  sondern als frühen, literaturbasierten Strukturierungsrahmen;
- Anschluss an die initiale Lösungshypothese aus dem Kick-off erhalten.

Wenn die Kick-off-Grafik eine eigene Synthese aus Literatur war:
Caption z. B.
„Eigene Darstellung in Anlehnung an ...“

Wenn eine Originalabbildung aus Literatur verwendet wird:
Originalquelle unmittelbar zitieren.

## 2.4 Geplanter Abschnitt
Arbeitsname:

### KI-gestützte Verarbeitung und Kontrollmechanismen

Kein allgemeiner KI-Grundlagenblock.

Stattdessen gezielt untersuchen, welche vorhandenen Ansätze die in 2.3
identifizierten Ebenen beziehungsweise Probleme adressieren.

Zu behandeln:
- LLMs für semantische Interpretation von Engineering-Information;
- LLM-/Agenten-basierte Generierung formaler Modelle;
- Grenzen von LLM-only-Ansätzen;
- syntaktische Validität ≠ fachliche Korrektheit;
- Retrieval / Grounding;
- agentische Verarbeitung;
- Human-in-the-Loop / Human Oversight;
- Multi-Perspective / Multi-Agent-Ansätze;
- Ontologien und semantische Leitplanken;
- Provenance und Traceability;
- metamodell-/syntaxbasierte technische Validierung.

Wichtige Literaturbasis:
- `Pan.2025`
- `Cibrian.2025`
- `Cibrian.2025b`
- `Topcu2025`
- `Chis.2025`
- Ontologiequellen aus bestehendem Literaturbestand
- HITL-Quellen aus Kapitel 3
- Provenance: `MoreauMissier2013`, `Simmhan2005`
- Multi-Perspective: `Du2024`, `TriemDing2024`

Wichtige Grenze:
Mehrere Agents / Personas oder deren Agreement dürfen nicht automatisch
mit fachlicher Richtigkeit gleichgesetzt werden.

## 2.5 Geplanter Abschnitt

### Synthese des Forschungsstands und Forschungsbedarf

Hier werden die vorherigen Abschnitte zusammengeführt.

Erwartete Synthese:
- Verfahren für Modelltransformation existieren.
- LLM-basierte Interpretation und Modellgenerierung existiert.
- Human-in-the-Loop existiert.
- Ontologien / semantische Leitplanken existieren.
- Provenance / Traceability existiert.
- Multi-Agent-/Multi-Perspective-Verfahren existieren.
- technische Modellvalidierung existiert.

Forschungslücke:
Es fehlt nicht primär ein weiterer Mechanismus zur bloßen Erzeugung von
SysML-v2-Code, sondern eine zusammenhängende Informationsverarbeitungsarchitektur,
die heterogene Brownfield-Information mit kontrollierter probabilistischer
Interpretation, nachvollziehbarer Herkunft, expliziten menschlichen
Authority-Grenzen und formal kontrollierten Übergängen zur Zielrepräsentation
verbindet.

Diese Formulierung ist vor finaler Verwendung claimweise mit der
Literatursynthese abzugleichen. Nicht behaupten, dass weltweit „keine“
vergleichbare Architektur existiert, wenn die Literatur nur zeigt, dass
die identifizierten Ansätze die Aspekte nicht in der benötigten Kombination
adressieren.

## 2.6 Forschungsfragen

Nach 2.5 werden Hauptforschungsfrage und TF1–TF3 eingeführt.

Die Forschungsfragen dürfen nicht plötzlich erscheinen.
Für jede TF soll kurz sichtbar werden, welcher Forschungsbedarf aus 2.1–2.5
zu ihr führt:

TF1:
Informationsbasis, Heterogenität, Provenance, Constraints.

TF2:
Unsicherheit, Mehrdeutigkeit, Human Authority / HITL.

TF3:
semantische/ontologische Leitplanken, Modellstruktur und formale
Zielrepräsentation.

---

# 5. Status Kapitel 3

Kapitel 3 ist inhaltlich bis einschließlich 3.6 ausgearbeitet.

Struktur:

3.1 Forschungsdesign und Vorgehensmodell  
3.2 Literaturrecherche und Ableitung des Forschungsbedarfs  
3.3 Methodik der modellbasierten Architekturentwicklung  
3.4 Methodische Gestaltungsprinzipien der KI-gestützten Informationsverarbeitung  
3.5 Iterative Konkretisierung und prototypische Untersuchung  
3.6 Evaluationslogik und Abgrenzung  

Kein 3.7 erforderlich.

Die frühe sechsstufige Lösungshypothese aus der Kick-off-Phase gehört in 3.1,
nicht in Kapitel 2. Sie stellt bereits die erste eigene Antwort auf den zuvor
identifizierten Forschungsbedarf dar.

Kapitel 3 soll Methodik beschreiben:
„Wie wird vorgegangen, um zu Ergebnissen zu kommen?“

Keine Ergebnisse, PASS-Zahlen oder Forschungsfragenbeantwortung in Kapitel 3.

---

# 6. Status Kapitel 4–8

Kapitel 4–8 sind inhaltlich weitgehend vollständig, derzeit aber deutlich
zu lang.

Aktueller grober Umfang:
- Kapitel 4 ca. 25 Seiten
- Kapitel 5 ca. 26 Seiten
- Kapitel 6 ca. 15 Seiten
- Kapitel 7 ca. 21 Seiten
- Kapitel 8 ca. 32 Seiten

Ziel später:
- Kapitel 4 ca. 12–14 Seiten
- Kapitel 5 ca. 12–14 Seiten
- Kapitel 6 ca. 8–10 Seiten
- Kapitel 7 ca. 10–12 Seiten
- Kapitel 8 ca. 16–20 Seiten

Die Kürzung erfolgt erst nach Stabilisierung von Kapitel 1–3 und Kapitel 9.

Wichtige Trennung:
- Kapitel 4 = Architekturkonzept
- Kapitel 5 = prototypische Realisierung
- Kapitel 6 = wie Verifikation/Validierung durchgeführt wurde
- Kapitel 7 = was beobachtet wurde
- Kapitel 8 = Interpretation, Literaturvergleich und Beantwortung der
  Forschungsfragen

---

# 7. Noch offenes Persona-Thema

Nach Abschluss von Kapitel 2/3:

- finale tatsächlich verwendete Persona-Sets im Turing-Generator-Code prüfen;
- aktive Personas von Legacy-/obsoleten Profilen unterscheiden;
- in Kapitel 5 nur kompakte Übersicht der verwendeten Persona-Gruppen;
- vollständige tatsächlich verwendete Persona-Definitionen in einen Anhang;
- keine ausführlichen Persona-Definitionen in Kapitel 3.

---

# 8. Plan Kapitel 9

Kapitel 9 soll bewusst kurz bleiben.

Reihenfolge:

## 9.1 Ausblick
## 9.2 Fazit

Vor der finalen Formulierung des Ausblicks soll der finale
Turing-Generator-Stand beziehungsweise insbesondere der relevante technische
State noch einmal inspiziert werden. Keine Zukunftsbehauptung aus historischen
Planungsdateien übernehmen.

### 9.1 Ausblick: geplante Themen

#### A. Inkrementelle Brownfield-Evolution
Direkter Anschluss an Kapitel 1:

Zukünftig könnte untersucht werden, wie Änderungen registrierter
Engineering-Quellen erkannt und nur die davon betroffenen nachgelagerten
Informations- und Modellzustände erneut verarbeitet werden können.

Mögliche Fragestellungen:
- Source Change Detection;
- Impact-Ermittlung entlang persistierter Provenance;
- selektive statt vollständige Re-Interpretation;
- erneute Human Authority nur an tatsächlich betroffenen Übergängen;
- kontrollierte Aktualisierung bestehender Modellstände.

Keine Behauptung, dass dies in der Masterarbeit bereits vollständig gelöst ist.

#### B. Lernen aus menschlichen Entscheidungen / Adaptive Human Feedback Learning
Bereits diskutierter Zukunftspfad.

Persistierte menschliche Entscheidungen könnten zukünftig als
Erfahrungsbasis genutzt werden, um wiederkehrende Entscheidungssituationen
besser vorzubereiten.

Wichtige Grenze:
Kein unkontrolliertes oder stilles Selbstlernen.

Zu untersuchen wäre:
- welche Human Decisions als wiederverwendbare Erfahrung geeignet sind;
- wie Kontext und Gültigkeitsbereich einer Entscheidung erhalten bleiben;
- wann frühere Entscheidungen nur als Vorschlag dienen dürfen;
- wann erneut menschliche Autorisierung erforderlich ist;
- wie Provenance und Auditierbarkeit auch bei lernenden/adaptiven
  Mechanismen erhalten bleiben.

Human Authority bleibt dabei grundsätzlich erhalten.

#### C. Erweiterung der unterstützten Informationsquellen
Der PoC fokussiert auf begrenzte, aufbereitete Engineering-Testdaten.

Zukünftige Arbeiten könnten weitere Eingabeformen untersuchen:
- umfangreichere Dokumente;
- Tabellen;
- strukturierte PLM-/ALM-Daten;
- gegebenenfalls Abbildungen und weitere multimodale Engineering-Information.

Dabei erneut:
Nicht behaupten, dass die heutige Architektur bereits jede Modalität
vollständig beherrscht.

#### D. Skalierung auf größere Engineering-Projekte
Der potenzielle Nutzen automatisierter Unterstützung wird besonders bei
umfangreichen Projekten mit sehr großen Informationsbeständen relevant.

Die Arbeit weist keine Produktivitätssteigerung nach.

Zukünftige Untersuchungen könnten deshalb gezielt empirisch prüfen:
- Verhalten bei sehr großen Dokumentmengen;
- Laufzeit und Kosten;
- Review-Aufwand;
- Verhältnis automatisierter und menschlicher Verarbeitung;
- Modellqualität;
- tatsächliche Produktivität gegenüber manueller Modellierung.

Erst solche Untersuchungen dürften Aussagen über Effizienz-/Produktivitätsvorteile
stützen.

#### E. Produktive Integration
Weiterführende Arbeit könnte die konzeptionelle/prototypische Architektur
in Richtung produktiver Engineering-Umgebung untersuchen:

- PLM-/ALM-Anbindungen;
- Identitäts- und Rechtekonzepte;
- Versionierung;
- Monitoring;
- Recovery und Robustheit;
- reproduzierbare Modellaktualisierung;
- Governance für eingesetzte KI-Modelle.

Dies liegt außerhalb des Anspruchs der aktuellen Masterarbeit.

### 9.2 Fazit

Das Fazit soll KEINE neuen Ergebnisse enthalten.

Es soll:
- auf die Ausgangsproblemstellung zurückgreifen;
- den Architekturbeitrag knapp zusammenfassen;
- die Forschungsfragen in verdichteter Form zusammenführen;
- klar zwischen Architekturkonzept und Prototyp unterscheiden;
- die wichtigsten Claim-Grenzen erneut sichtbar machen.

Kernbotschaft:

Die Arbeit entwickelt kein „LLM, das SysML schreibt“, sondern untersucht,
wie probabilistische Informationsverarbeitung durch explizite
Informationsgrundlagen, Provenance, Human Authority und kontrollierte
Übergänge in einen nachvollziehbaren Engineering-Prozess eingebettet
werden kann.

### Kurze Reflexion zum KI-Einsatz
Nahe dem Ende von Kapitel 9 maximal ca. 1/3 Seite.

Transparent machen:
- KI ist sowohl Gegenstand der Architektur als auch Entwicklungswerkzeug;
- wesentliche Teile der Softwareimplementierung wurden mit LLM-Unterstützung
  erstellt;
- der Autor verfügt über grundlegende Programmierkenntnisse;
- Systems Engineering, Informationslogik, Anforderungen,
  Architekturentscheidungen, fachliche Randbedingungen,
  Authority-Grenzen und Akzeptanzentscheidungen verblieben beim Autor;
- generierter Code wurde über gezielte Tests, Regression,
  End-to-End-Untersuchungen und technische Validierung geprüft;
- aus dem KI-gestützten Entwicklungsprozess wird keine
  Produktivitätsaussage abgeleitet.

---

# 9. Weitere Abschlussarbeiten nach Stabilisierung des Textes

1. Kapitel 1–3 final auf Redundanzen, Übergänge und Zitationsdichte prüfen.
2. Kapitel 9 schreiben.
3. Persona-Dokumentation ergänzen.
4. Kapitel 4–8 erheblich kürzen.
5. Anhänge kürzen, insbesondere den aktuell übergroßen ADR-Anhang.
6. Prüfen, welche Anhänge tatsächlich wissenschaftliche/project evidence sind.
7. Finale CATIA-Modellabgabe vorbereiten.
8. Turing-Generator-Repository erst am Ende bereinigen:
   obsolete Artefakte entfernen/archivieren, finale reproduzierbare
   Abgabestruktur herstellen.
9. Keine während des Schreibens benötigte Traceability vorschnell zerstören.

---

# 10. Schreib- und Evidenzregeln

- Allgemeine wissenschaftliche Aussage → Literatur.
- Eigene Architekturentscheidung → Literatur stützt Problem/Prinzip,
  die konkrete Lösung ist Beitrag der Arbeit.
- Projekt-/Implementierungsbeschreibung → eigenes technisches Artefakt,
  keine künstliche Literaturreferenz.
- Ergebnis → Evaluationsevidenz.
- Interpretation/Generalisierung → Ergebnisse + Literatur.
- Keine erfundenen Seitenangaben oder Quellenclaims.
- Keine Übernahme von `collaboration/` als wissenschaftliche Evidenz.
- Keine Testanzahl als Architekturqualitätsmetrik.
- technische SysML-v2-Validität ≠ Engineering Correctness.
- Consensus ≠ Truth.
- deterministische Verarbeitung ≠ fachliche Richtigkeit.
- „vollständige Traceability“ nur innerhalb registrierter/admitted
  Projektinformation, nicht bezogen auf alle real existierenden Informationen.
- Testdaten in der Thesis als „aufbereitete Engineering-Testdaten“
  charakterisieren; Ursprung sind reale PreciPoint-Engineering-Daten,
  deren Scope, Wortlaut und Dokumentstruktur für reproduzierbare,
  praktikable Testläufe reduziert/normalisiert wurden.

---

# 11. Startpunkt für den nächsten Chat

Der nächste Chat soll zuerst diese Datei lesen und anschließend den aktuellen
Textstand aus den tatsächlichen Kapiteldateien prüfen.

Erster Arbeitsauftrag:

> Wir setzen die Masterarbeit anhand von
> `Arbeitsstand/2026-09-14_Thesis_Writing_SSOT.md` fort.
> Lies zuerst den SSOT sowie die aktuellen Kapitel 1–3 und die relevante
> Literaturbasis. Wir stehen bei Kapitel 2.3.
> Bevor 2.3 geschrieben wird, muss die originale Kick-off-Unterlage geprüft
> werden, um das damalige Drei-Schichten-Modell mit exakten Bezeichnungen
> und Literaturquellen zu rekonstruieren. Nichts aus Erinnerung erfinden.
> Danach Claim-/Quellenmatrix für 2.3 und erst nach Akzeptanz den Text.