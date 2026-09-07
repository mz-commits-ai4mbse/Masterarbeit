# ADR-Thesis-Klassifikationsmatrix

## Zweck

Diese Matrix ordnet die im Turing-Generator entstandenen Architecture Decision Records für die Verwendung in der Masterarbeit ein.

Sie dient drei Zielen:

1. ADRs als echte Engineering-Artefakte wirksam in die Thesis einzubinden.
2. Architektur- und Realisierungsentscheidungen von verifikations- bzw. finding-getriebenen Korrekturentscheidungen zu trennen.
3. Zu vermeiden, dass Kapitel 4 oder 5 Testergebnisse, Blocker oder deren Interpretation vorwegnehmen.

Die Matrix ist eine redaktionelle Thesis-Hilfe. Sie verändert die Original-ADRs nicht. Die ADR-Dateien im Engineering-Repository bleiben die autoritative Originalevidenz.

## Klassifikationslogik

### A – Architekturentscheidung
Entscheidung über Verantwortlichkeiten, Informationsgrenzen, Authority-Grenzen, zentrale Informationsobjekte oder den konzeptionellen Verarbeitungsfluss. Primäre Thesis-Verwendung: Kapitel 4.

### R – Realisierungsentscheidung
Entscheidung darüber, wie eine bereits konzipierte Architektur technisch im Proof of Concept umgesetzt wird. Primäre Thesis-Verwendung: Kapitel 5.

### V – Verification-/Finding-getriebene Entscheidung
Entscheidung, deren Anlass wesentlich aus einem Test, Finding, Blocker oder einer formativen Untersuchung entstand. Der finale Realisierungszustand darf in Kapitel 5 beschrieben werden. Der Zusammenhang Test/Finding → Problem → Korrekturentscheidung → Wirkung wird jedoch erst in Kapitel 6 bis 8 erläutert.

### O – Operative / UX / technische Detailentscheidung
Für die Nachvollziehbarkeit des Engineering-Prozesses relevant, aber nicht notwendigerweise zentral für die Architekturargumentation. Primäre Thesis-Verwendung: Kapitel 5, Anhang oder nur Projektevidenz.

## Regeln für die Thesis

- ADRs strukturieren weder Kapitel 4 noch Kapitel 5.
- Kapitel 4 folgt der Architekturargumentation.
- Kapitel 5 folgt dem implementierten Engineering Information Flow.
- ADRs werden an der fachlich passenden Stelle als Design-Rationale-Evidence referenziert.
- Testresultate und Blocker werden nicht über ADR-Kontexttexte in Kapitel 4 oder 5 vorweggenommen.
- Bei V-ADRs darf Kapitel 5 den akzeptierten Endzustand beschreiben, jedoch nicht die Testursache erläutern.
- Die Original-ADR bleibt unverändert.
- Für den Anhang kann eine redaktionelle Thesis-Einordnung vorangestellt werden, ohne den Originalinhalt umzuschreiben.

## Vorläufige Klassifikationsmatrix

> „V?“ bedeutet: Verification-/Finding-Bezug vor finaler Thesis-Zitation noch einmal gegen ADR-Kontext und zugehörige Findings/Checkpoints prüfen.

| ADR | Kurzbezeichnung | Klasse | Verification-/Finding-getrieben? | Primärer Thesis-Ort | Kapitel-4-Relevanz | Anhang | Bemerkung |
|---|---|---|---|---|---|---|---|
| ADR-005 | Project Workspace Architecture | A/R | nein | 5.1/5.2 | mittel | optional | Projektgrenzen und Workspace als technische Basis |
| ADR-009 | Textual Source Processing Boundary | A/R | nein | 4.2 / 5.3 | hoch | ja | Source-Verarbeitungsgrenze und Trennung von Vorbereitung/Interpretation |
| ADR-010 | Project Source Registry Architecture | R | nein | 5.2 | niedrig | optional | technische Source-Registrierung |
| ADR-011 | Semantic Information Unit and Ontology Boundary | A/R | nein | 4.2 / 5.3–5.4 | hoch | ja | semantische Informationsgrenze und Ontologie-Boundary |
| ADR-012 | Processing State and Artifact Organization | A/R | nein | 4.4 / 5.1–5.2 | hoch | ja | Processing State, Artifact Organization, Provenance-Basis |
| ADR-013 | Preliminary Coverage and Potential Model Support | R/A | nein | 5.4/5.7 | gering–mittel | optional | Coverage und Model-Support |
| ADR-014 | Project Dashboard Architecture | O/R | nein | 5.10 | gering | optional | UI/Evidence Navigation |
| ADR-015 | Project-Bound Agentic Ingestion Integration | R | nein | 5.2–5.4 | gering | optional | technische Integration des source-bound Processing |
| ADR-016 | Human Review Workspace and Approved Input Promotion Architecture | A/R | nein | 4.4 / 5.5 | sehr hoch | ja | zentrale Human-Authority- und Promotion-Boundary |
| ADR-017 | Simple-by-Default Interaction and Progressive Disclosure | O/R | nein | 5.10 | gering | optional | UX-Entscheidung |
| ADR-018 | Model Candidate Layer and Structural Comparability | A/R | nein | 4.5 / 5.7 | sehr hoch | ja | Trennung Engineering Authority ↔ Model Candidate |
| ADR-019 | Internal Engineering Model Assembly Architecture | A/R | nein | 4.5 / 5.8 | sehr hoch | ja | representation-neutrales IEM und deterministische Assembly |
| ADR-020 | Hybrid Target Projection and Coverage Architecture | A/R | V? | 4.5 / 5.7 | hoch | ja/optional | Target Projection, Coverage und unresolved Handling |
| ADR-021 | SYSIDE-Compatible SysML v2 Generation Architecture | A/R | nein | 4.5 / 5.8 | sehr hoch | ja | deterministische SysML-v2-Repräsentation und Syntaxauthority |
| ADR-022 | SysML v2 Validation Layer Architecture | A/R | nein | 4.4/4.5 / 5.9 | sehr hoch | ja | Generation und Validation getrennt |
| ADR-023 | Final Model Review and Output Publication Architecture | A/R | nein | 4.4/4.5 / 5.9 | sehr hoch | ja | Final Human Model Authority und Publication Gate |
| ADR-024 | Guided Workflow / Workflow Architecture | O/R | nein | 5.10 | gering | optional | Human-facing Workflow |
| ADR-025 | Semantic Proposal Consolidation and Persona-Aware Consensus | A/R/V | ja bzw. prüfen | 5.4; Ursache später 8 | mittel | ja | finalen Zustand nutzbar, Finding-Historie nicht in Kap. 4/5 vorwegnehmen |
| ADR-026 | Source-Anchored Multi-Persona Interpretation and Cross-Unit Semantic Synthesis | A/R/V | ja, BLK-/WP-12-Bezug | 5.4; Ursache später 8 | mittel | ja | Source-Anchoring und Persona-Verarbeitung; Korrekturhistorie später |
| ADR-027 | Source-Grounded Evidence Detection and Persona Interpretation Architecture | A/R/V? | prüfen | 4.2/4.4 / 5.3–5.4 | hoch | ja | Source Grounding und Persona Interpretation |
| ADR-028a | Context-Preserving Canonical Engineering Subject Discovery | A/R/V? | prüfen | 5.3–5.4 | mittel | optional | im Repo existiert zusätzlich ein zweites ADR-028; Dateiname immer mitführen |
| ADR-028b | Model Derivation Mode and Review Escalation Architecture | A/R | prüfen | 4.5 / 5.7 | hoch | ja | Derivation Strategy und Review Escalation |
| ADR-029 | Human-Reviewed Model Placement Before Model Assembly | A/R | V? | 4.4/4.5 / 5.7 | sehr hoch | ja | zentrale Model-Placement-Authority-Boundary |
| ADR-030 | Semantic Interpretation and Controlled Classification Alignment | A/R/V? | prüfen | 5.4 | mittel | optional | semantische Klassifikation |
| ADR-031 | Semantic Field Consistency Alignment | R/V? | prüfen | 5.4; Ursache ggf. 8 | gering | optional | eher Implementierungs-/Consistency-Entscheidung |
| ADR-032 | Project-Level Multi-Source Reconciliation and Controlled Engineering Evolution | A/R/V | ja, explizit WP-12 / BLK-002 | finaler Zustand 4.5/5.6; Ursache erst 6–8 | nur selektiv | ja | Kontext enthält Testergebnis/BLK-002 und darf in Kap. 4 nicht ungekürzt verwendet werden |
| ADR-033 | Multi-Source / concern-centric refinement | R/V | ja, BLK-002-Kontext | historische/empirische Einordnung 8 | nein bzw. nur final akzeptierter Rest | ja | nicht als aktive finale Thesis-Authority-Chain übernehmen |
| ADR-034 | Source Provenance Does Not Constrain Concern Grouping | R/V | ja, Refinement von ADR-033; BLK-002 | historische/empirische Einordnung 8 | nein bzw. nur Forschungs-/Entwicklungsevidenz | ja | retained prototype/research evidence, nicht mandatory active thesis-MVP gate |

## Kandidaten für Kapitel 4

### Priorität A – zentrale Architekturentscheidungen
- ADR-009 – Textual Source Processing Boundary
- ADR-011 – Semantic Information Unit and Ontology Boundary
- ADR-012 – Processing State and Artifact Organization
- ADR-016 – Human Review Workspace and Approved Input Promotion Architecture
- ADR-018 – Model Candidate Layer and Structural Comparability
- ADR-019 – Internal Engineering Model Assembly Architecture
- ADR-021 – SYSIDE-Compatible SysML v2 Generation Architecture
- ADR-022 – SysML v2 Validation Layer Architecture
- ADR-023 – Final Model Review and Output Publication Architecture
- ADR-029 – Human-Reviewed Model Placement Before Model Assembly

### Priorität B – abhängig vom konkreten Claim
- ADR-020 – Hybrid Target Projection and Coverage Architecture
- ADR-025 – Semantic Proposal Consolidation and Persona-Aware Consensus
- ADR-027 – Source-Grounded Evidence Detection and Persona Interpretation Architecture
- ADR-028b – Model Derivation Mode and Review Escalation Architecture

### Sonderfall Multi-Source
ADR-032 bis ADR-034 werden nicht wie normale initiale Architekturentscheidungen in Kapitel 4 verwendet.

ADR-032 enthält ausdrücklich den WP-12-/BLK-002-Auslöser. Kapitel 4 darf den final akzeptierten Architekturzustand beschreiben, aber nicht über die ADR-Zitation bereits das Testergebnis oder den Blocker erzählen.

Für Kapitel 4 ist deshalb zunächst das finale CATIA-Modell die primäre Engineering-Model Authority. ADR-032 kann höchstens sehr selektiv als Design-Rationale für die final erhaltene source-local Boundary dienen. Die Entstehungslogik und der Verification-Auslöser gehören in Kapitel 6 bis 8.

ADR-033 und ADR-034 bleiben wichtige Engineering-/Research-Evidence, gehören aber nach aktuellem accepted state nicht als mandatory aktive Thesis-MVP-Gates in die Architekturargumentation.

## Vorschlag für den ADR-Anhang

### Anhang X – Architecture Decision Records

#### X.1 Rolle der ADRs im Entwicklungsprozess
Kurze Einordnung:
- ADRs als zeitgenössische Engineering-Artefakte
- Dokumentation von Kontext, Entscheidung und Konsequenzen
- nicht identisch mit wissenschaftlicher Literatur
- Verwendung als Design-Rationale- und Projektevidenz

#### X.2 ADR-Übersicht
Redaktionelle Tabelle mit:
- ADR-ID / Dateiname
- Titel
- Status
- Kategorie A/R/V/O
- betroffener Engineering-Information-Flow-Schritt
- Thesis-Referenz

#### X.3 Ausgewählte thesis-relevante ADRs
Für ausgewählte ADRs:
1. Thesis-Einordnung (neu, kurz)
2. Original-ADR inhaltlich unverändert

Die Originale dürfen typografisch vereinheitlicht werden, jedoch nicht rückwirkend inhaltlich „verschönert“ werden.

## Noch zu prüfen

- [ ] exakte vollständige Liste aller ADR-Dateien im finalen Repository erfassen
- [ ] doppeltes ADR-028 sauber über Dateinamen unterscheiden
- [ ] ADR-020 auf Verification-/Finding-Auslöser prüfen
- [ ] ADR-025 bis ADR-031 jeweils gegen Findings-/Checkpoint-Kontext prüfen
- [ ] ADR-033 exakten final akzeptierten Status und Verhältnis zu ADR-032/034 erfassen
- [ ] entscheiden, welche ADRs vollständig in den Anhang kommen
- [ ] Kapitel-4-Zitationen setzen, ohne Verification-Historie vorwegzunehmen
- [ ] Kapitel-5-Zitationen entlang des Engineering Information Flow setzen
- [ ] Kapitel 8 mit den tatsächlich finding-getriebenen ADRs rückbinden
