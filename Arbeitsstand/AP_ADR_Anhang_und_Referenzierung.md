# AP – ADR-Anhang und Thesis-Referenzierung

## Ziel

Dieses Arbeitspaket bündelt die Erstellung eines eigenständigen ADR-Anhangs
sowie die konsistente Referenzierung der thesis-relevanten Architecture Decision
Records in Kapitel 4, 5 und 6.

Die ADRs bleiben dabei echte Engineering-Artefakte des Turing-Generator-Projekts.
Sie werden nicht rückwirkend inhaltlich umgeschrieben. Für die Thesis werden sie
redaktionell eingeordnet, ausgewählt und referenziert.

---

# 1. Grundprinzip

Die ADRs übernehmen in der Thesis unterschiedliche Rollen:

- **Kapitel 4:** Design-Rationale und Engineering-Evidence für zentrale
  Architekturentscheidungen.
- **Kapitel 5:** Engineering-Evidence für die technische Realisierung der
  Architektur entlang des Engineering Information Flow.
- **Kapitel 6:** Referenz auf den zu Beginn der jeweiligen Untersuchung
  vorhandenen bzw. geprüften Implementierungs- und Architekturzustand.
- **Kapitel 7:** grundsätzlich keine neue ADR-Diskussion; hier stehen
  beobachtete Ergebnisse.
- **Kapitel 8:** Einordnung finding- bzw. blocker-getriebener ADRs als
  Architekturentwicklung und Lessons Learned.

Wichtig:

- ADRs strukturieren kein Kapitel.
- ADRs ersetzen keine wissenschaftliche Literatur.
- ADRs werden als Projektevidenz bzw. Design-Rationale verwendet.
- Testergebnisse und Blocker dürfen nicht über ADR-Kontexttexte in Kapitel 4
  oder 5 vorweggenommen werden.
- Die Original-ADRs im Engineering-Repository bleiben unverändert.

---

# 2. Zielstruktur des ADR-Anhangs

Vorgesehene Datei:

`Anhang/02_ArchitectureDecisionRecords.tex`

Vorgesehenes Hauptlabel:

```latex
\section{Architecture Decision Records}
\label{app:architecture_decision_records}
```

## 2.1 Einordnung der ADRs

Kurzer einleitender Text zu:

- ADRs als zeitgenössische Engineering-Artefakte
- Rolle im iterativen Architektur- und Implementierungsprozess
- Dokumentation von Context, Decision und Consequences
- Abgrenzung zu wissenschaftlicher Literatur
- Abgrenzung zu Testprotokollen und Findings
- Verwendung in der Thesis als Design-Rationale- und Engineering-Evidence

## 2.2 ADR-Übersicht

Tabelle mit mindestens:

| Feld | Inhalt |
|---|---|
| ADR | ID |
| Titel | Originaltitel |
| Status | z. B. Accepted |
| Kategorie | Architektur / Realisierung / Verification-getrieben / operativ |
| Engineering-Flow-Bezug | betroffene Verarbeitungsstufe |
| Thesis-Bezug | Kapitel bzw. Abschnitt |
| Appendix-Eintrag | Label |

Die Tabelle soll nicht den vollständigen ADR-Inhalt ersetzen, sondern
Navigation und Traceability ermöglichen.

## 2.3 Ausgewählte thesis-relevante ADRs

Für jede im Anhang aufgenommene ADR wird ein einheitlicher Eintrag erstellt.

Empfohlenes Schema:

```latex
\subsection{ADR-XXX: Originaltitel}
\label{adr:XXX}
```

Darunter:

1. **Thesis-Einordnung**
   - Warum ist diese ADR für die Arbeit relevant?
   - Welche Architektur- oder Realisierungsentscheidung stützt sie?
   - Wo wird sie im Haupttext verwendet?

2. **Originalstatus und Datum**

3. **Thesis-relevanter Originalauszug**
   - Context
   - Decision
   - Consequences
   - ggf. weitere unmittelbar relevante Originalabschnitte

4. **Hinweis zur redaktionellen Behandlung**
   - Inhalt nicht verändert
   - ggf. nicht thesis-relevante Detailabschnitte ausgelassen

Beispielhinweis:

> Der folgende Auszug gibt die für die Thesis relevanten Teile des
> ursprünglichen Engineering-Artefakts wieder. Inhaltliche Aussagen wurden
> nicht verändert; nicht thesis-relevante Implementierungsdetails wurden
> ausgelassen.

---

# 3. ADR-Auswahl für den ersten Appendix-Block

## Hohe Priorität

Diese ADRs sind bereits oder voraussichtlich unmittelbar für Kapitel 4 und 5
relevant:

- ADR-009 – Textual Source Processing Boundary
- ADR-011 – Semantic Information Unit and Ontology Boundary
- ADR-012 – Processing State and Artifact Organization
- ADR-016 – Human Review Workspace and Approved Input Promotion Architecture
- ADR-018 – Model Candidate Layer and Structural Comparability
- ADR-019 – Internal Engineering Model Assembly Architecture
- ADR-021 – SYSIDE-Compatible SysML v2 Generation Architecture
- ADR-022 – SysML v2 Validation Layer Architecture
- ADR-023 – Final Model Review and Output Publication Architecture
- ADR-027 – Source-Grounded Evidence Detection and Persona Interpretation Architecture
- ADR-029 – Human-Reviewed Model Placement Before Model Assembly

## Zweite Priorität / nach Claim prüfen

- ADR-005
- ADR-010
- ADR-013
- ADR-014
- ADR-015
- ADR-017
- ADR-020
- ADR-024
- ADR-025
- ADR-026
- ADR-028a
- ADR-028b
- ADR-030
- ADR-031

## Verification-/Finding-getriebene Sondergruppe

- ADR-032
- ADR-033
- ADR-034

Diese Gruppe wird zunächst **nicht als normale initiale Design-Rationale in
Kapitel 4 verwendet**.

Der finale akzeptierte Architektur- und Implementierungszustand darf in
Kapitel 4 bzw. 5 beschrieben werden. Die Entstehungslogik

`Verification → Finding/Blocker → Architekturkorrektur → erneute Prüfung`

wird erst in Kapitel 6 bis 8 offengelegt und interpretiert.

---

# 4. Referenzkonvention im Haupttext

## 4.1 Erstmalige Einführung in Kapitel 4

Beim ersten tatsächlichen ADR-Bezug in Kapitel 4 wird einmalig erklärt, wie
ADRs in der Thesis verwendet werden.

Vorgeschlagener Text:

```latex
Die im Entwicklungsverlauf getroffenen Architecture Decision Records (ADRs)
dokumentieren zentrale Architektur- und Realisierungsentscheidungen als
Engineering-Artefakte. Soweit sie für die Argumentation dieses Kapitels
relevant sind, werden sie im Folgenden als Design-Rationale-Evidence
herangezogen. Eine Übersicht und die thesis-relevanten ADR-Auszüge sind in
Anhang~\ref{app:architecture_decision_records} zusammengestellt.
```

Danach keine Wiederholung dieses allgemeinen Hinweises.

## 4.2 Einzelreferenzen

Sobald der Appendix erstellt ist, werden nackte ADR-Nennungen bevorzugt mit
einer gezielten Appendix-Referenz versehen.

Beispiel:

```latex
... wurde in ADR-019 dokumentiert
(vgl. Anhang~\ref{adr:019}).
```

Bei mehreren ADRs in einem Satz kann auf den Gesamtanhang oder auf einzelne
Labels verwiesen werden.

Beispiel:

```latex
Die zugehörigen Entscheidungen sind in ADR-016, ADR-018 und ADR-019
dokumentiert (vgl. Anhang~\ref{app:architecture_decision_records}).
```

Nicht jede ADR-Nennung benötigt zwingend einen eigenen Klammerverweis, wenn
im unmittelbaren Absatz bereits eindeutig auf den ADR-Anhang verwiesen wurde.
Die Referenzierung soll wissenschaftlich nachvollziehbar, aber nicht
typografisch überladen sein.

---

# 5. Kapitel 4 – Referenzierungsauftrag

Aktueller Stand:

Kapitel 4 enthält bereits direkte ADR-Nennungen.

Zu bearbeiten:

- [ ] allgemeinen ADR-Hinweis beim ersten ADR-Vorkommen einfügen
- [ ] ADR-009 und ADR-011 mit Appendix-Referenz versehen
- [ ] ADR-027 referenzieren
- [ ] ADR-016 / 018 / 019 / 021 / 022 / 023 in der Logical-Architecture-Passage referenzieren
- [ ] ADR-012 in Evidence/Traceability referenzieren
- [ ] ADR-018 in Model Candidate Derivation referenzieren
- [ ] ADR-029 in Model Placement referenzieren
- [ ] ADR-019 in IEM-Passage referenzieren
- [ ] ADR-021 in Generation referenzieren
- [ ] nach Appendix-Erstellung alle Labels kompilieren und auf broken refs prüfen
- [ ] ADR-032 bis ADR-034 nicht über ihre Verification-Historie in Kapitel 4 einführen

Ziel:
Keine neue ADR-Historie in Kapitel 4. Die bestehenden Architekturclaims werden
lediglich mit Engineering Decision Evidence rückgebunden.

---

# 6. Kapitel 5 – Referenzierungsauftrag

Kapitel 5 folgt dem implementierten Engineering Information Flow.

Für jede wesentliche Realisierungsstufe wird geprüft:

1. Welche Architekturverantwortung aus Kapitel 4 wird realisiert?
2. Welche ADR dokumentiert die relevante Realisierungsentscheidung?
3. Ist die ADR initial/normal entstanden oder finding-getrieben?
4. Darf nur der finale Zustand beschrieben werden oder auch die
   Entstehungsbegründung?

Typische Zuordnung:

- Project / Source Context → ADR-005, ADR-010, ADR-012
- Source Preparation → ADR-009
- Evidence / Semantic Boundary → ADR-011, ADR-027
- Persona / Consensus → ADR-025, ADR-026 nur ohne vorweggenommene Findings
- Human Review / AEI Promotion → ADR-016
- Model Candidate Layer → ADR-018
- Model Derivation / Placement → ADR-020, ADR-028b, ADR-029
- IEM → ADR-019
- SysML-v2 Generation → ADR-021
- Validation → ADR-022
- Final Review / Publication → ADR-023
- UI / Guided Workflow → ADR-014, ADR-017, ADR-024

Regel:
Kapitel 5 darf den stabilisierten finalen Realisierungszustand beschreiben.
Bei verification-getriebenen ADRs wird die Ursache noch nicht erläutert.

---

# 7. Kapitel 6 – Referenzierungsauftrag

Kapitel 6 beschreibt die Verifikation und Validierung.

ADRs werden dort nicht als Testergebnis verwendet, sondern um den jeweils
geprüften Architektur- bzw. Implementierungszustand eindeutig zu identifizieren.

Mögliche Verwendung:

```latex
Die Untersuchung prüfte die in ADR-016 dokumentierte Human-Review-Boundary
gegen ...
```

oder:

```latex
Der geprüfte Multi-Source-Zustand basierte auf ...
```

Erst hier dürfen zugehörige Findings und Blocker eingeführt werden, sofern sie
Bestandteil der beschriebenen Untersuchung waren.

Wichtig:
- ADR ≠ Test Evidence
- Testprotokoll / Checkpoint / Findings Register = Verification Evidence
- ADR = geprüfte oder aus einer Untersuchung resultierende Engineering Decision

Bei finding-getriebenen ADRs muss die zeitliche Reihenfolge sauber dargestellt
werden:

```text
Ausgangszustand
→ Untersuchung
→ Finding / Blocker
→ ADR / Architekturkorrektur
→ Re-Test
```

Diese Kette wird nicht rückwirkend so dargestellt, als hätte die finale ADR
bereits vor dem ursprünglichen Test bestanden.

---

# 8. Kapitel 7 und 8

## Kapitel 7

ADRs nur nennen, wenn sie zur eindeutigen Identifikation des getesteten
Systemstands erforderlich sind.

Keine Design-Rationale-Diskussion.

## Kapitel 8

Hier werden finding-getriebene ADRs wirksam als Entwicklungs- und
Reflexionsevidence eingebunden.

Insbesondere:

- Welche Annahme der ursprünglichen Architektur war unzureichend?
- Welches empirische Finding machte dies sichtbar?
- Welche Architekturentscheidung wurde daraufhin geändert?
- Welche Eigenschaft der finalen Architektur entstand daraus?
- Welche Limitation bzw. generalisierbare Erkenntnis folgt daraus?

ADR-032 bis ADR-034 sind hierfür besonders relevant.

---

# 9. Umgang mit Original-ADRs

Die Engineering-Artefakte werden nicht rückwirkend thesis-konform umgeschrieben.

Erlaubt:

- typografische Vereinheitlichung
- LaTeX-Überführung
- Kürzung nicht thesis-relevanter Detailabschnitte
- redaktionelle Thesis-Einordnung
- Kennzeichnung als Auszug
- stabile Labels und Querverweise

Nicht erlaubt:

- ursprünglichen Context nachträglich „bereinigen“
- Findings aus dem ADR entfernen und so tun, als seien sie nie enthalten gewesen
- Entscheidungsgründe rückwirkend ändern
- Chronologie verändern
- Status oder Konsequenzen neu formulieren, ohne dies als Thesis-Einordnung zu kennzeichnen

---

# 10. Technische Umsetzung in LaTeX

## Dateien

Vorgesehen:

```text
Anhang/02_ArchitectureDecisionRecords.tex
```

Optional bei großem Umfang:

```text
Anhang/ADRs/ADR-009.tex
Anhang/ADRs/ADR-011.tex
...
```

Die Hauptdatei bindet die einzelnen ADR-Auszüge dann mit `\input{...}` ein.

## Labels

Gesamtanhang:

```latex
\label{app:architecture_decision_records}
```

Einzel-ADRs:

```latex
\label{adr:009}
\label{adr:011}
\label{adr:012}
...
```

Bei doppelter ADR-Nummer 028 Dateinamen und Labels eindeutig machen, z. B.:

```latex
\label{adr:028_subject_discovery}
\label{adr:028_model_derivation}
```

---

# 11. Abschluss-Checkliste

- [ ] vollständige finale ADR-Dateiliste erfassen
- [ ] ADR-Klassifikationsmatrix final prüfen
- [ ] finding-getriebene ADRs eindeutig markieren
- [ ] Auswahl für den Appendix festlegen
- [ ] `Anhang/02_ArchitectureDecisionRecords.tex` erstellen
- [ ] Einleitung und ADR-Übersicht erstellen
- [ ] thesis-relevante ADR-Auszüge kompilieren
- [ ] Originalinhalt gegen Repository prüfen
- [ ] Labels für alle verwendeten ADRs vergeben
- [ ] Kapitel 4 referenzieren
- [ ] Kapitel 5 referenzieren
- [ ] Kapitel 6 referenzieren
- [ ] Kapitel 8 für verification-getriebene ADRs vormerken
- [ ] keine Blocker-/Finding-Historie in Kapitel 4/5 vorwegnehmen
- [ ] Overleaf/Biber/LaTeX kompilieren
- [ ] alle `\ref`-Verweise auflösen
- [ ] Appendix-Umfang auf Lesbarkeit prüfen
- [ ] ggf. sehr lange ADRs weiter kürzen und als Auszug kennzeichnen

---

# 12. Definition of Done

Das Arbeitspaket ist abgeschlossen, wenn:

1. alle im Haupttext verwendeten ADRs im Appendix eindeutig auffindbar sind;
2. Kapitel 4, 5 und 6 konsistent auf diese Engineering-Evidence verweisen;
3. finding-getriebene Entscheidungen zeitlich korrekt behandelt werden;
4. Original-ADRs und Thesis-Einordnung klar voneinander unterscheidbar sind;
5. keine Testresultate oder Blocker durch Kapitel 4 oder 5 vorweggenommen werden;
6. alle LaTeX-Querverweise fehlerfrei kompilieren.
