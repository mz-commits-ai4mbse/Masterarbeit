# AP Kapitel 5 – Abbildungen und Tabellen

## Ziel
Kapitel 5 beschreibt die prototypische Realisierung entlang des tatsächlich implementierten Engineering Information Flow. Die Abbildungen sollen deshalb nicht die Python-Paketstruktur dokumentieren, sondern die technischen Realisierungsgrenzen, Artefaktzustände, Human-Authority-Boundaries und den Übergang von Engineering Source zu veröffentlichtem SysML-v2-Output verständlich machen.

## Darstellungsprinzipien
- Haupttext folgt dem Informationsfluss, nicht der Modulstruktur.
- Abbildungen zeigen bevorzugt Zustandsübergänge, Artefakte und Authority Boundaries.
- Repository- oder Klassendiagramme nur, wenn sie einen inhaltlichen Mehrwert liefern.
- Keine vollständigen Modulbäume oder Prompt-Sammlungen im Haupttext.
- Screenshots nur dort, wo die Interaktionslogik oder Human Authority sichtbar wird.
- CATIA-Abbildungen aus Kapitel 4 nicht wiederholen, sondern Implementierungsnähe ergänzen.
- Testresultate und Verifikationszahlen gehören nicht in Kapitel 5, sondern in Kapitel 6/7.

## Abbildung 5.1 – Implementierter Engineering Information Flow
**Priorität:** MUSS / Kernabbildung

**Platzierung:** früh in Kapitel 5, ideal nach 5.1 oder zu Beginn von 5.2.

**Inhalt:**
Project / Source Registration → Processing Context → Source Preparation → Evidence → Engineering Subjects → Persona Interpretation → Consensus / Variance / Stability → Human Engineering Review → Approved Engineering Information → Project Fit → ProjectFitPhaseHHandoff → Model Candidate Set → Human Model Placement → Approved Model Placement Set → Internal Engineering Model → SysML-v2 Generation → Validation → Final Human Model Review → Publication.

Zusätzlich sichtbar:
- deterministische vs. probabilistische Verarbeitung
- Human-Authority-Grenzen
- zentrale persistierte Artefakte
- Multi-Source-Zusammenführung erst ab Project Fit / Candidate Derivation
- kein synthetic merged AEI

## Abbildung 5.2 – Project-, Source-, Run- und Artifact-Identity
**Priorität:** HOCH

**Platzierung:** 5.2

Darstellung:
Project → Sources → Processing Runs → Attempts → Artifacts.

Zusätzlich:
`(processing_run_id, artifact_type, artifact_id)` als project-wide Artifact Identity sowie Fingerprint-Bindung.

## Abbildung 5.3 – Source-grounded Processing bis Engineering Subject
**Priorität:** HOCH

**Platzierung:** 5.3

Darstellung:
Original Source → Source Projection / Analysis Unit → Evidence Reference → Engineering Subject → Persona Interpretations.

Kernbotschaft:
- Evidence bleibt source-grounded.
- Engineering Subject ist fachlicher Vergleichsgegenstand.
- Persona erzeugt nicht die Evidence-Identität.

## Abbildung 5.4 – Persona Interpretation, Consensus, Variance und Stability
**Priorität:** HOCH

**Platzierung:** 5.4

Darstellung eines Engineering Subject mit Persona A/B/C und jeweils wiederholten Runs.

Sichtbar:
- inter-Persona Consensus / Variance
- intra-Persona Stability
- repeated runs ≠ zusätzliche Stimmen
- Ergebnis = Processing Evidence, keine Human Authority

## Abbildung 5.5 – Human Review und AEI Promotion Boundary
**Priorität:** MUSS

**Platzierung:** 5.5

Darstellung:
Evidence + Persona Interpretations + Consensus / Variance + Provenance → Subject-centered Human Review → Human Review Decision → Finalization / Promotion → Approved Engineering Information.

Wichtig:
- LLM Output wird nicht direkt zu AEI.
- Human Decision ist an exakten Review-/Content-Fingerprint gebunden.
- ursprüngliche Evidence bleibt immutable.
- AEI ist ein neuer Authority-Zustand.

## Abbildung 5.6 – Multi-Source Project Fit und Phase-H-Handoff
**Priorität:** MUSS

**Platzierung:** 5.6

Darstellung mehrerer source-lokaler Pfade:
Source A/B/C → source-local Human Review → jeweilige AEI → jeweiliger Project Fit → ProjectFitPhaseHHandoff → separate source-local AEI consumption → ONE Project-level Model Candidate Set.

Explizit:
- kein synthetic merged AEI
- source-local Provenance bleibt erhalten
- Project Fit = Admission Gate, keine Engineering Authority
- concern-centric reconciliation nur Outlook / Research Evidence, nicht aktiver MVP-Pfad

## Abbildung 5.7 – Candidate, Placement, IEM und Generation
**Priorität:** MUSS

**Platzierung:** 5.7 oder Übergang 5.7 → 5.8

Darstellung:
Approved Engineering Information → Model Candidate Derivation → Model Candidate Set → Placement Proposals → Human Model Placement Review → Approved Model Placement Set → deterministic Assembly → Internal Engineering Model → deterministic SysML-v2 Generation.

Kernbotschaften:
- Candidate ≠ Model Authority
- Persona agreement ≠ Model Authority
- Human Placement vor Assembly
- IEM representation-neutral
- Generator interpretiert keine Engineering-Semantik neu

## Abbildung 5.8 – Validation, Final Human Review und Publication
**Priorität:** HOCH

**Platzierung:** 5.9

Darstellung:
GeneratedSysMLArtifactSet → interne deterministische Validierung + externe SYSIDE-Validierung → SysMLValidationResult → Final Human Model Review → Final Model Review Decision → Published Output Package.

Statuslogik:
- valid + passed → publication eligible
- invalid / incomplete / blocked → reviewable, aber nicht publishable

## Abbildung 5.9 – Human-facing Guided Engineering Workflow
**Priorität:** MITTEL bis HOCH

**Platzierung:** 5.10.1

Bevorzugt ein repräsentativer Streamlit-Screenshot oder eine schematische UI-Abbildung.

Sichtbare Stufen:
- Project & Sources
- Processing
- Human Review
- Model Proposal / Placement
- Final Model Review
- Published Output

Kernbotschaft:
Simple by default. Explainable on demand. Fully traceable underneath.

Kein UI-Handbuch.

## Abbildung 5.10 – Provenance- und Fingerprint-Kette
**Priorität:** HOCH

**Platzierung:** 5.10.2

Mögliche Kette:
Source → Processing Artifact → Review Subject → Human Decision → AEI → Project Fit Handoff → Model Candidate → Placement Decision → IEM → Generated Artifact → Validation Result → Final Review Decision → Published Output.

Jeder Übergang:
- explizite ID
- Fingerprint
- immutable Referenz
- keine implizite latest-Auswahl

## Tabelle 5.1 – Architektur-zu-Implementierung-Mapping
**Priorität:** MUSS

**Platzierung:** 5.10.3

Spalten:
1. Architekturverantwortung
2. Repräsentative Implementierung
3. Zentrale Artefakte / Contracts
4. Repräsentative Design-Rationale / ADR

Empfohlene Zeilen:
- Project and Source Context Management
- Engineering Information Processing
- Human Review and Engineering Authority
- Evidence / Traceability
- Architecture Derivation and Model Placement
- Internal Model Assembly
- SysML v2 Artifact Generation
- Generated Artifact Validation
- Final Model Review and Publication

Wichtig:
- keine 1:1-Zuordnung suggerieren
- keine vollständige 30+-Module-Liste
- mehrere Module dürfen eine Architekturverantwortung realisieren
- ein Modul darf mehrere Verantwortungen unterstützen

## Optionale Tabelle 5.2 – Zentrale technische Artefaktzustände
**Priorität:** OPTIONAL

Spalten:
- Verarbeitungsstufe
- Artefakt / Zustand
- Authority-Typ
- Mutability
- Downstream-Rolle

Nur verwenden, wenn die Authority-Zustände im Fließtext sonst schwer überschaubar werden.

## Empfohlene Mindestmenge
1. Abbildung 5.1 – Implementierter Engineering Information Flow
2. Abbildung 5.5 – Human Review und AEI Promotion
3. Abbildung 5.6 – Multi-Source Project Fit
4. Abbildung 5.7 – Candidate → Placement → IEM → SysML
5. Abbildung 5.8 – Validation → Final Review → Publication
6. Abbildung 5.9 – Guided Workflow Screenshot
7. Tabelle 5.1 – Architektur-zu-Implementierung-Mapping

Abbildungen 5.2, 5.3, 5.4 und 5.10 sind hochwertige Ergänzungen, falls der Umfang dies rechtfertigt.

## Reihenfolge der Erstellung
1. Engineering Information Flow
2. Human Review / AEI Boundary
3. Multi-Source Project Fit
4. Candidate / Placement / IEM / Generation
5. Validation / Final Review / Publication
6. Guided Workflow Screenshot
7. Architektur-zu-Implementierung-Tabelle
8. danach optionale Detailgrafiken

## Definition of Done
Kapitel 5 ist grafisch vollständig, wenn:
- der komplette implementierte Informationsfluss mindestens einmal sichtbar ist,
- die wichtigsten Human-Authority-Grenzen klar hervorgehoben sind,
- Multi-Source ohne synthetic merged AEI korrekt dargestellt ist,
- IEM und SysML-v2-Generierung getrennt sichtbar sind,
- Validation und Human Release nicht miteinander vermischt werden,
- das Verhältnis von Architektur zu repräsentativer Implementierung gezeigt wird,
- keine Grafik eine nicht implementierte Target-Architecture-Fähigkeit als PoC-Realität darstellt,
- keine Abbildung Verifikationsresultate aus Kapitel 6/7 vorwegnimmt.
