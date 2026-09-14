# Thesis Red Thread and Core Claims

Stand: 12.09.2026

## 1. Zweck und Verbindlichkeit

Dieses Dokument definiert den inhaltlichen roten Faden der neu ausgerichteten Masterarbeit.

Es ergänzt die bestehenden `Thesis_Writing_and_Evidence_Rules.md` und ist für die weitere inhaltliche Ausarbeitung verbindlich.

Die Masterarbeit wird nicht als Beschreibung des Turing Generators und nicht als chronologischer Entwicklungsbericht aufgebaut.

Wissenschaftlicher Gegenstand ist das Architekturkonzept für einen kontrollierten KI-gestützten Engineering-Prozess zur Überführung heterogener Bestandsinformationen in SysML v2.

Der Turing Generator dient als prototypische Instanziierung, Untersuchungsinstrument und Evidenzbasis für dieses Konzept.

---

## 2. Zentrale Problemstellung

Die zentrale Herausforderung bei der KI-gestützten Erzeugung von Engineering-Modellen liegt nicht in der Generierung syntaktisch plausiblen SysML-v2-Codes, sondern in der kontrollierten Herleitung der darin repräsentierten Engineering-Information.

Ein Large Language Model kann aus Eingangsinformationen plausible Modellinhalte erzeugen. Für einen Engineering-Prozess reicht Plausibilität jedoch nicht aus.

Es muss nachvollziehbar sein:

- welche Informationen als fachliche Grundlage zugelassen wurden,
- welche Informationen lediglich die Interpretation unterstützen,
- wo probabilistische Interpretation stattfindet,
- wo Unsicherheit oder Varianz entsteht,
- welche Entscheidungen menschlich autorisiert wurden,
- welche Transformationen deterministisch erfolgen,
- wie jede resultierende Modellinformation auf ihre Herkunft und Entscheidungsgrundlage zurückgeführt werden kann,
- und welche Aussagekraft eine technische Modellvalidierung tatsächlich besitzt.

Die übergeordnete Fragestellung hinter der Architektur lautet damit:

> Wie kann probabilistische KI-Verarbeitung so in eine Engineering-Architektur eingebettet werden, dass daraus ein kontrollierbarer, nachvollziehbarer und überprüfbarer Engineering-Prozess entsteht?

SysML v2 bildet den konkreten Zielkontext, an dem dieses Problem untersucht wird.

---

## 3. Wissenschaftlicher Beitrag

Die Arbeit entwickelt und untersucht ein Architekturkonzept für eine kontrollierte KI-gestützte Transformation heterogener Engineering-Information in SysML v2.

Der Beitrag liegt insbesondere in der Kombination und architektonischen Trennung von:

- kontrollierter Informationsbasis,
- expliziter Informationsautorität,
- Source Grounding,
- Provenance und Traceability,
- multiperspektivischer KI-Interpretation,
- Interpretationsvarianz als Review-Evidence,
- gestufter Human Authority,
- Trennung von Engineering Meaning und Modellrepräsentation,
- deterministischen nachgelagerten Transformationen,
- Fail-Closed-Grenzen,
- technischer Modellvalidierung,
- und abschließender menschlicher Engineering-Freigabe.

Das Ergebnis der Arbeit ist damit primär ein Architekturkonzept.

Der implementierte Proof of Concept ist nicht der wissenschaftliche Hauptbeitrag, sondern die prototypische Instanziierung, anhand derer das Konzept konkretisiert, belastet, korrigiert, demonstriert und untersucht wurde.

---

## 4. Harte Claim Boundaries

### 4.1 Keine Garantie vollständiger Realweltinformation

Die Architektur garantiert nicht, dass alle in der Realität relevanten Engineering-Informationen in den bereitgestellten Quellen enthalten sind.

Untersucht wird, wie die explizit registrierte und zugelassene Informationsbasis kontrolliert und nachvollziehbar verarbeitet werden kann.

Fehlende oder nicht ableitbare Information darf nicht stillschweigend als fachliche Tatsache ergänzt werden.

### 4.2 Persona-Varianz verhindert keine Halluzinationen

Mehrere KI-Perspektiven oder Personas garantieren keine korrekte Interpretation und verhindern Halluzinationen nicht grundsätzlich.

Ihr Zweck liegt darin, unterschiedliche Interpretationen desselben source-grounded Gegenstands sichtbar zu machen.

Unterschieden werden insbesondere:

- stabiler Konsens,
- gemeinsame Unsicherheit beziehungsweise Shared Ambiguity,
- tatsächliche Interpretationsvarianz.

Diese Information dient als Review-Evidence und kann menschliche Aufmerksamkeit gezielt auf unsichere oder strittige Inhalte lenken.

### 4.3 Technische Validität ist keine Engineering Correctness

Eine erfolgreiche SysML-v2- beziehungsweise SYSIDE-Validierung belegt technische beziehungsweise sprachliche Modellvalidität.

Sie belegt nicht automatisch:

- fachliche Richtigkeit,
- Vollständigkeit der zugrunde liegenden Engineering-Information,
- Eignung für einen konkreten Entwicklungszweck,
- oder wissenschaftliche Validität des Architekturkonzepts.

### 4.4 Testanzahl ist kein Qualitätsmaß

Eine hohe Anzahl bestandener automatisierter Tests ist kein wissenschaftlicher Qualitäts- oder Validitätsnachweis.

Tests dienen als Projektevidenz für definierte technische Eigenschaften und Regression.

### 4.5 Proof of Concept ist keine Production Readiness

Der Turing Generator ist ein Proof of Concept.

Aus seiner technischen Funktionsfähigkeit wird keine allgemeine Produktionsreife abgeleitet.

---

## 5. Architekturprinzipien

### P1 — Controlled Information Basis and Authority Separation

Die Architektur unterscheidet Informationen nach ihrer Rolle und fachlichen Autorität.

#### Engineering Sources

Engineering Sources dürfen fachliche Aussagen über das zu modellierende System begründen.

Aus ihnen kann source-grounded Engineering Evidence abgeleitet werden.

#### Context Sources

Context Sources dienen dazu, die Interpretations- und Entscheidungsgrundlage des Systems zu verbessern.

Sie können beispielsweise bereitstellen:

- Terminologie,
- Domänenwissen,
- Definitionen,
- ontologische Zusammenhänge,
- Referenzwissen,
- Klassifikationshilfen,
- oder projektrelevanten Interpretationskontext.

Context Sources dürfen die Interpretation von Engineering Sources beeinflussen.

Sie dürfen jedoch nicht eigenständig:

- Engineering Evidence erzeugen,
- eine fachliche Systemaussage begründen,
- Coverage oder Readiness erfüllen,
- oder unmittelbar Modellinhalt autorisieren.

Der Zweck von Context ist damit nicht Informationsvermeidung, sondern eine bewusst kontrollierte Erweiterung der Interpretationsgrundlage.

#### Processing- und Orchestration-Kontext

Von beiden Source-Rollen ist technischer Processing-Kontext zu unterscheiden.

Dazu gehören beispielsweise:

- Prompts,
- Workflow-Anweisungen,
- Agentenrollen,
- Verarbeitungskonfiguration,
- Orchestrierungsinformation,
- technische Metadaten.

Diese Informationen steuern die Verarbeitung, sind aber ebenfalls keine Engineering Evidence.

#### Kernaussage

Grounding bedeutet nicht, dass ein KI-System keinerlei Zusatzwissen verwenden darf.

Grounding bedeutet, dass nachvollziehbar bleibt, welche Information welche Autorität besitzt und worauf eine fachliche Aussage tatsächlich zurückgeführt werden kann.

---

### P2 — End-to-End Provenance and Traceability

Jede relevante Engineering-Information muss entlang ihrer Verarbeitung nachvollziehbar bleiben.

Die Traceability-Kette verbindet insbesondere:

Source
→ Evidence
→ Interpretation
→ Human Decision
→ Approved Engineering Information
→ Model Derivation
→ Model Placement
→ Internal Engineering Model
→ SysML-v2-Artefakt
→ Validation
→ Human Release.

Provenance ist nicht lediglich Metadokumentation, sondern Bestandteil der Architektur.

Bei Multi-Source-Verarbeitung bleibt source-lokale Provenance erhalten, auch wenn Informationen projektweit zusammengeführt oder gemeinsam bewertet werden.

---

### P3 — Multi-Perspective Interpretation as Review Evidence

Mehrere Interpretationen werden auf denselben source-grounded fachlichen Gegenstand angewendet.

Die Personas dienen nicht der unabhängigen Erfindung von Engineering Subjects.

Sie erzeugen unterschiedliche Interpretationsperspektiven auf eine gemeinsame Evidence- beziehungsweise Subject-Basis.

Ziel ist die Sichtbarmachung von:

- Konsens,
- Shared Ambiguity,
- Interpretationsvarianz.

Varianz wird damit selbst zu Engineering Review Evidence.

---

### P4 — Staged Human Authority

Human-in-the-Loop wird nicht als generisches finales Approval verstanden.

Menschliche Autorität wird an den Stellen eingesetzt, an denen eine Entscheidung die fachliche Bedeutung oder die spätere Modellstruktur materiell verändert.

Dazu gehören insbesondere:

- Bewertung interpretierter Engineering-Information,
- Korrektur oder Zurückweisung,
- Klärung von Mehrdeutigkeiten,
- Project Fit,
- Model Placement,
- gegebenenfalls Model Quality Refinement,
- Final Model Review und Release.

Der Mensch soll nicht jede technische Transformation manuell nachvollziehen müssen.

Die Architektur soll menschliche Aufmerksamkeit auf Entscheidungen konzentrieren, die Engineering Authority benötigen.

---

### P5 — Separation of Engineering Meaning, Representation and Formulation

Die Architektur trennt mindestens drei Verantwortlichkeiten:

1. Engineering Meaning
   Was bedeutet die zugrunde liegende Engineering-Information fachlich?

2. Target Representation and Placement
   Als welcher Modellgegenstand und an welcher Stelle soll diese Information im Zielmodell repräsentiert werden?

3. Target-Model Formulation
   Wie wird die autorisierte Modellinformation syntaktisch und strukturell als SysML v2 formuliert?

Diese Entscheidungen dürfen nicht implizit in einem einzelnen probabilistischen Generierungsschritt verschmelzen.

Ontologische oder semantische Klassifikation allein reicht deshalb nicht zur vollständigen Modellbildung aus.

---

### P6 — Deterministic and Fail-Closed Controlled Transitions

Nach einer fachlich autorisierten Entscheidung sollen nachgelagerte Verarbeitungsschritte möglichst deterministisch erfolgen.

Vertrags-, Integritäts-, Identitäts- oder Provenance-Verletzungen dürfen nicht stillschweigend in nachgelagerte Artefakte propagiert werden.

Wo definierte technische oder fachliche Voraussetzungen verletzt sind, verarbeitet das System fail-closed.

Ein technisch erfolgreicher LLM-Aufruf ist dabei nicht gleichbedeutend mit akzeptierter Engineering-Information.

---

### P7 — Separation of Technical Validity, Engineering Correctness and Scientific Evidence

Die Architektur und ihre Evaluation unterscheiden drei Ebenen:

1. technische beziehungsweise syntaktische Validität,
2. fachliche Engineering Correctness beziehungsweise menschliche Engineering Authority,
3. wissenschaftliche Evidenz für Aussagen über das Architekturkonzept.

Keine dieser Ebenen ersetzt die andere.

---

## 6. Multi-Source als querschnittliches Architekturthema

Multi-Source-Verarbeitung ist kein zusätzlicher isolierter Pipeline-Schritt.

Sie verschärft insbesondere P1 und P2.

Erforderlich sind:

- stabile Source Identity,
- source-lokale Provenance,
- explizite projektweite Admission,
- kontrollierte Project-Fit-Entscheidungen,
- nachvollziehbare Zusammenführung fachlich zusammengehöriger Information,
- Erhalt der Herkunft auch nach projektweiter Konsolidierung.

Die Herkunft einer Information darf ihre fachliche Gruppierung nicht künstlich verhindern.

Gleichzeitig darf projektweite Zusammenführung ihre ursprüngliche Provenance nicht zerstören.

---

## 7. Rolle der Architecture Decision Records

Die Architecture Decision Records sind zentrale Engineering-Artefakte der Arbeit.

Sie sind jedoch nicht selbst der wissenschaftliche Beitrag.

Der wissenschaftliche Beitrag ist das aus den Entscheidungen synthetisierte und untersuchte Architekturkonzept.

Die ADRs bilden die zentrale Design-Rationale-Evidence für dessen Entwicklung.

Sie dokumentieren insbesondere:

- welches Architekturproblem adressiert wurde,
- welche Alternativen betrachtet wurden,
- welche Entscheidung getroffen wurde,
- warum diese Entscheidung getroffen wurde,
- welche Konsequenzen akzeptiert wurden,
- und bei finding-getriebenen ADRs, welche Beobachtungen zu einer Architekturkorrektur führten.

Damit beantworten ADRs primär die Frage:

> Warum ist die Architektur so gestaltet?

Die Thesis wird dennoch nicht nach ADR-Nummern strukturiert.

Die Argumentation folgt:

Problem
→ erforderliche Verantwortung
→ Architekturprinzip
→ konzeptionelle Lösung
→ Design Rationale
→ prototypische Instanziierung
→ Evaluation und Boundary.

ADRs werden an der jeweils fachlich passenden Stelle als Projektevidenz referenziert.

Sie ersetzen keine wissenschaftliche Literatur.

Finding-getriebene oder später revidierte ADRs bleiben als historische Engineering- und Research-Evidence erhalten und werden nicht rückwirkend umgeschrieben.

---

## 8. Authority- und Evidenzhierarchie der Thesis

### 8.1 Scientific Authority

Wissenschaftliche Literatur begründet:

- allgemeine Aussagen,
- theoretische Konzepte,
- bekannte Risiken,
- methodische Entscheidungen,
- Forschungslücken,
- und die Einordnung der Ergebnisse.

### 8.2 Conceptual Contribution

Das in Kapitel 4 synthetisierte Architekturkonzept ist der eigentliche wissenschaftliche Lösungsbeitrag der Arbeit.

### 8.3 Design-Rationale Evidence

Die ADRs dokumentieren die Überlegungen und Entscheidungen, aus denen sich das Konzept entwickelt hat.

Sie beantworten primär das Warum.

### 8.4 Structural Architecture Authority

Das akzeptierte CATIA-/SysML-v2-Systemmodell repräsentiert den final akzeptierten strukturellen Architekturzustand.

Es beantwortet primär:

> Wie ist die akzeptierte Architektur strukturiert?

Historische ADRs können frühere oder später korrigierte Zustände dokumentieren.

Für die Beschreibung des finalen Architekturzustands ist der akzeptierte Endzustand maßgeblich.

### 8.5 Prototype Instantiation

Der Turing Generator zeigt:

> Wie wurde das Architekturkonzept prototypisch instanziiert?

Code, Manifeste, persistierte Artefakte und technische Komponenten sind Implementierungsevidenz.

### 8.6 Evaluation Evidence

Tests, Findings, formative Untersuchungen, Multi-Source-Versuche und der Demo-Run 930444 zeigen:

> Was wurde bei der Anwendung und Untersuchung des Konzepts beziehungsweise seiner Instanziierung beobachtet?

Diese Evidenz bildet die Grundlage für Kapitel 7 und die Interpretation in Kapitel 8.

---

## 9. Forschungsfragen

Die mit dem betreuenden Professor abgestimmten Forschungsfragen werden nicht verändert.

### Hauptforschungsfrage

> Wie muss eine Informationsverarbeitungsarchitektur gestaltet sein, um heterogene Bestandsdaten durch ein systematisches Bewertungs- und Synthese-Framework sicher in valide SysML v2 Strukturen zu überführen?

### TF1 — Input Assessment & Constraints

> Welche Anforderungen (Constraints) müssen an die Eingangsdaten gestellt werden, damit ein KI-Agent zuverlässig arbeiten kann?

Der ursprüngliche Fokus auf Datenqualität wird durch die Ergebnisse nicht ersetzt, sondern differenziert beantwortet.

Relevant sind insbesondere:

- Verarbeitbarkeit der Informationsmodalität,
- kontrollierte Informationsbasis,
- Source Identity,
- Source Role,
- Grounding,
- Provenance,
- Coverage,
- Umgang mit fehlender beziehungsweise nicht ableitbarer Information.

Die Untersuchung kann zeigen, dass nicht nur Eigenschaften des Dokuments selbst, sondern auch die architektonische Kontrolle seiner Rolle entscheidend sind.

### TF2 — Assurance & Effective Intervention

> Wie wird der Ingenieur als Kontrollorgan in die Architektur eingebunden, um bei detektierten Mehrdeutigkeiten effektiv einzugreifen?

Die Antwortlogik fokussiert auf:

- gestufte Human Authority,
- zielgerichtete Review-Gates,
- Unsicherheit,
- Consensus,
- Shared Ambiguity,
- Persona Variance,
- Korrektur,
- Rejection,
- Clarification,
- Model Placement,
- Final Release.

Nicht die Menge menschlicher Interaktion ist entscheidend, sondern ihre Position an materiellen Engineering-Entscheidungen.

### TF3 — Leitplanken-Design

> Wie müssen die ontologischen Regeln („Leitplanken“) gestaltet sein, gegen die die Eingangsdaten geprüft werden, um die Plug & Play-Fähigkeit der resultierenden SysML v2 Modelle (Modularität) zu gewährleisten?

Die Arbeit beantwortet diese Frage auf Basis der tatsächlich gewonnenen Erkenntnisse.

Eine mögliche zentrale Erkenntnis ist, dass ontologische und semantische Leitplanken erforderlich, aber allein nicht hinreichend für robuste Modellbildung sind.

Zusätzlich müssen getrennt kontrolliert werden:

- Engineering Meaning,
- Target Representation,
- Placement,
- Formulation,
- Assembly,
- technische Validation,
- und Human Model Authority.

Falls sich dadurch eine ursprüngliche Annahme der Forschungsfrage als zu eng erweist, wird dies als Ergebnis diskutiert und nicht durch nachträgliche Umformulierung der Forschungsfrage verdeckt.

### Zusammenhang zur Hauptforschungsfrage

Die Hauptforschungsfrage integriert:

TF1
→ kontrollierte Informationsbasis

TF2
→ kontrollierte Engineering Authority

TF3
→ kontrollierte semantische und modellbezogene Transformation

zu einem gemeinsamen Architekturkonzept.

---

## 10. Rolle des Proof of Concept

Der Turing Generator ist:

- Demonstrator,
- prototypische Instanziierung,
- Untersuchungsinstrument,
- Quelle empirischer Projektevidenz.

Er ist nicht:

- wissenschaftliches Endprodukt,
- Referenzimplementierung mit Production-Readiness-Anspruch,
- Beweis universeller Gültigkeit,
- oder Hauptstruktur der Thesis.

Kapitel 5 beschreibt deshalb nur diejenigen Implementierungsaspekte, die zum Verständnis der Architekturprinzipien und ihrer Untersuchung erforderlich sind.

---

## 11. Rolle des Demo-Projekts 930444

Projekt 930444 dient als finaler persistierter Referenzlauf der akzeptierten PoC-Architektur.

Es dokumentiert eine durchgängige Authority- und Traceability-Kette von registrierten Quellen bis zum publizierten SysML-v2-Artefakt.

Es kann insbesondere als Evidenz verwendet werden für:

- Engineering- und Context-Source-Rollen,
- source-grounded Verarbeitung,
- Human Engineering Review,
- Approved Engineering Information,
- Project Fit,
- Model Placement,
- Model Assembly,
- Model Quality Refinement,
- SysML-v2-Generierung,
- Traceability,
- technische SYSIDE-Validierung,
- Final Human Release,
- immutable Publication Output.

Projekt 930444 ist kein Ersatz für die formativen Untersuchungen.

Historische Findings werden nur dann mit 930444 verknüpft, wenn sie in diesem Run tatsächlich beobachtet wurden.

Der Referenzlauf belegt nicht automatisch die allgemeine Wirksamkeit der Architektur oder eine kontrollierte Stakeholder-Evaluation.

---

## 12. Funktion der formativen Findings

Historische Blocker, Findings und Korrekturen sind keine Software-Fehlerchronik.

Sie werden nur aufgenommen, wenn sie eine wissenschaftlich relevante Architekturgrenze sichtbar gemacht haben.

Die argumentative Logik lautet:

Beobachtung
→ aufgedecktes Architekturproblem
→ Design-Rationale beziehungsweise Architekturkorrektur
→ Retest
→ Erkenntnis für das Architekturkonzept.

Die zehn verdichteten Finding-Cluster aus `Thesis_Findings_Matrix_Final.md` dienen dabei als Evidence-Struktur, nicht automatisch als Kapitelstruktur.

---

## 13. Kapitelverantwortlichkeiten

### Kapitel 1 — Einleitung

Funktion:

- Problem motivieren,
- Forschungslücke zuspitzen,
- Ziel und Beitrag positionieren,
- Forschungsfragen unverändert einführen.

Zielumfang: ca. 5–6 Seiten.

### Kapitel 2 — Theoretischer Hintergrund und Stand der Technik

Funktion:

Nur die theoretischen Bausteine bereitstellen, die für Problem, Architekturprinzipien und Diskussion benötigt werden.

Insbesondere:

- MBSE und SysML v2,
- Architecture as Code,
- heterogene Brownfield-Information,
- LLMs im Engineering,
- Human-in-the-Loop,
- Multi-Agent-/Multi-Perspective-Ansätze,
- Provenance und Traceability,
- Semantik und Ontologien,
- Modellvalidierung.

Zielumfang: ca. 13–15 Seiten.

### Kapitel 3 — Methodisches Vorgehen

Funktion:

Erklären, wie das Architekturkonzept entwickelt und untersucht wurde.

Der Ansatz ist designorientiert und weist Bezüge zu Design Science auf.

Die Arbeit wird nicht nachträglich als vollständige Durchführung eines starren DSRM-Prozesses umgedeutet.

Zielumfang: ca. 8–11 Seiten.

### Kapitel 4 — Architekturkonzept

Herzstück der Arbeit.

Kapitel 4 folgt nicht den ADR-Nummern und nicht dem Turing-Generator-Workflow.

Jeder wesentliche Abschnitt folgt möglichst der Logik:

Problem
→ Warum reicht ein trivialer Ansatz nicht?
→ erforderliche Verantwortung
→ Architekturprinzip
→ konzeptionelle Lösung
→ Design-Rationale-Evidence
→ Scope Boundary.

Zielumfang: ca. 14–16 Seiten.

### Kapitel 5 — Prototypische Realisierung

Nur die Instanziierung der für die Argumentation relevanten Architekturprinzipien.

Keine vollständige Systemdokumentation.

Zielumfang: ca. 7–10 Seiten.

### Kapitel 6 — Verifikation und Validierung

Beschreibt ausschließlich:

> Wie wurde untersucht?

Keine Ergebnisinterpretation.

Zielumfang: ca. 7–9 Seiten.

### Kapitel 7 — Ergebnisse

Beschreibt ausschließlich:

> Was wurde beobachtet?

Keine Interpretation und keine Beantwortung der Forschungsfragen.

Zielumfang: ca. 6–8 Seiten.

### Kapitel 8 — Diskussion und Beantwortung der Forschungsfragen

Beschreibt:

> Was bedeuten die Ergebnisse?

Hier erfolgt:

- Literaturabgleich,
- Interpretation,
- Diskussion von Gegenbefunden,
- Claim Boundaries,
- Limitationen,
- Übertragbarkeit,
- Beantwortung von HF, TF1, TF2 und TF3.

Zielumfang: ca. 10–12 Seiten.

### Kapitel 9 — Fazit und Ausblick

Verdichtete Antwort auf Problemstellung und Beitrag.

Keine neue Evidenz.

Zielumfang: ca. 3–4 Seiten.

Gesamtziel für den Haupttext: ungefähr 80 Seiten.

---

## 14. Verbindliche Schreiblogik

Die Arbeit folgt dem Grundsatz:

> WHY before WHAT.

Nicht:

Komponente
→ Funktion
→ nächste Komponente.

Sondern:

Problem
→ unzureichender naiver Ansatz
→ benötigte Verantwortung
→ Architekturentscheidung
→ prototypische Instanziierung
→ Evidenz
→ Boundary.

Für jeden Absatz gilt als Filter:

> Benötigt der Leser diese Information, um die Architekturentscheidung oder ihre Evaluation zu verstehen?

Wenn nein:

- entfernen,
- stark kürzen,
- oder in den Anhang verschieben.

---

## 15. Terminologie

### Architecture Concept

Der wissenschaftliche Lösungsbeitrag.

### Architecture Principle

Generalisierte Gestaltungsregel beziehungsweise Verantwortungstrennung innerhalb des Konzepts.

### ADR

Zeitgenössisches Engineering-Artefakt zur Dokumentation der Design Rationale.

### CATIA-/SysML-v2-Architekturmodell

Strukturelle Repräsentation des final akzeptierten Architekturzustands.

### Turing Generator

Prototypische Instanziierung des Architekturkonzepts.

### Finding

Empirische Beobachtung, die für Evaluation oder Architekturentwicklung relevant ist.

### Engineering Source

Quelle mit fachlicher Evidence Authority für das zu modellierende System.

### Context Source

Quelle zur Unterstützung von Interpretation und Entscheidung ohne eigenständige Engineering Evidence Authority.

### Human Authority

Explizite menschliche Autorisierung einer fachlich relevanten Engineering-Entscheidung.

---

## 16. Nicht-Ziele der neuen Thesis

Die Arbeit soll ausdrücklich nicht:

- den vollständigen Turing Generator dokumentieren,
- jede ADR einzeln nacherzählen,
- jeden Testfall im Haupttext aufführen,
- jede CATIA-Sicht reproduzieren,
- historische Entwicklungsreihenfolge abbilden,
- Testanzahl als Qualität verkaufen,
- Multi-Persona-Verarbeitung als Hallucination Cure darstellen,
- Ontologien als alleinige Lösung darstellen,
- oder SYSIDE-Validierung mit Engineering Correctness gleichsetzen.

---

## 17. Arbeitsregel für die weitere Neufassung

Vor dem Schreiben jedes Kapitels oder größeren Abschnitts werden zuerst festgelegt:

1. Funktion des Abschnitts,
2. zentrale Claims,
3. benötigte Literatur,
4. relevante ADRs,
5. relevante CATIA-Artefakte,
6. Implementierungsevidenz,
7. Evaluationsevidenz,
8. Claim Boundaries.

Erst danach wird Fließtext geschrieben.
