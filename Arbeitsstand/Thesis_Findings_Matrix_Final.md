# Thesis Findings Matrix — Final Reduced Version

**Stand:** 03.09.2026
**Zweck:** Verbindliche Verdichtung der vollständigen Findings-Historie für Kapitel 7 und 8.

## Grundregel

Die vollständige BLK-, SEM-, OBS- und PASS-Historie bleibt im Anhang. Der Haupttext wird **nicht nach Finding-IDs gegliedert**. Kapitel 7 berichtet beobachtete Ergebnisse. Kapitel 8 interpretiert diese Ergebnisse und beantwortet die Forschungsfragen.

OBS-Findings sind **keine eigenständigen wissenschaftlichen Kernergebnisse**. Einzelne OBS können als sekundäre Evidenz für Human Authority, Interaktionsaufwand oder Systemgrenzen verwendet werden.

## Finale 10 Ergebniscluster

| Nr. | Ergebniscluster | Kapitel 7: rein deskriptive Beobachtung | Primäre Projektevidenz | Sekundäre Evidenz | Kapitel 8: reservierte Interpretation | RQ | Claim Boundary |
|---|---|---|---|---|---|---|---|
| **R1** | **Technischer End-to-End-Machbarkeitsnachweis** | Project 000116 erreichte Gate-3 PASS, reale SYSIDE-Validierung PASS, Human Publication Approval PASS und immutable Publication PASS. Finaler Integrationsstand: 6335 passed, 12 skipped. | FINAL-G3, FINAL-REG | Golden E2E, WP12 Final Review Handoff | Die Architektur konnte im abgegrenzten PoC technisch durchgängig instanziiert werden. Technische PASS-Zustände sind keine allgemeine Engineering-Correctness-Garantie. | HF, TF3 | Single-Source-/PoC-Scope; Testanzahl kein Qualitätsscore. |
| **R2** | **Source Boundary und Source Purity** | Frühe Runs enthielten Processing-/Orchestration-Kontext als vermeintliche Engineering Information. Im R4c-Live-Pfad dominierte diese Kontamination den Review nicht mehr. | BLK-003, SEM-001, PASS-006 | OBS-019 als Discovery-Precision-Limit | Verlässliche Verarbeitung benötigt eine explizite Grenze zwischen Engineering Source Content und Processing/Reference Context. | TF1, HF | Im untersuchten Workflow belegt, nicht universell für alle LLM-Systeme. |
| **R3** | **Gemeinsame Evidence- und Subject Identity vor Persona Interpretation** | Im historischen Run 877791 lagen nach D3 109 und nach D4 weiterhin 109 Subjects vor. Die R4c-Architektur etablierte Canonical Subjects vor der Persona-Interpretation und wurde live validiert. | BLK-003, SEM-003, SEM-004 | PASS-006 | Persona-Diversität sollte denselben source-grounded Gegenstand interpretieren. Nachgelagerte Konsolidierung kann eine zu spät gesetzte Identität nicht zuverlässig kompensieren. | TF1, HF | 109→109 allein beweist keine allgemeine Ineffektivität; Interpretation stützt sich auf Finding-Kette + Recovery + Retest. |
| **R4** | **Unsicherheit, Consensus, Shared Ambiguity und Persona Variance** | Legitimate uncertainty wurde als Open Question erhalten. Shared multi-option placement und echte Persona-Varianz traten als unterschiedliche Zustandsformen auf. | SEM-008, SEM-013, PASS-004 | SEM-005, OBS-031 | Unsicherheit ist nicht automatisch Fehler. Consensus, gemeinsame Ambiguität und echte Varianz benötigen unterschiedliche Review-Interaktionen. | TF2 | Keine Aussage, dass die konkrete Interaktionslogik universell optimal ist. |
| **R5** | **Gestufte Human Engineering Authority und revidierbare Entscheidungen** | Human Review blieb vor AEI, Placement, Final Model Review und Publication erforderlich. Decisions konnten nach Korrekturen explizit reopened bzw. neu persistiert werden. | PASS-003, PASS-007, SEM-014 | OBS-014, OBS-020, OBS-021, OBS-025, OBS-026, OBS-031 | HITL ist in diesem Problemraum kein einzelner Approve-Schritt. Effective Intervention verlangt Authority an materiellen Engineering-Entscheidungen bei möglichst geringer unnötiger Interaktion. | TF2, HF | Keine pauschale Aussage „mehr Human = sicherer“. |
| **R6** | **Approved Engineering Information muss vollständige Human Authority transportieren** | Der frühe Phase-H-Handoff enthielt akzeptierte Relationships nicht vollständig. Nach BLK-005 wurden 17 Approved Subjects und 21 accepted semantic Relationships autoritativ gebunden; sechs Relationships waren intentionally_not_projected. | BLK-005, PASS-009, PASS-010 | BLK-004, PASS-008 | Downstream-Modellableitung benötigt den vollständigen human-autorisierten Informationsbestand und darf Relationships nicht still verlieren oder umdeuten. | TF2, TF3, HF | Aussage gilt für den realisierten AEI-/Phase-H-Vertrag. |
| **R7** | **Engineering Meaning ≠ Target Representation ≠ Target-Model Formulation** | BLK-006 blockierte den Modellpfad; SEM-012 zeigte unterschiedliche Engineering- und Zielmodellrollen. SEM-015 wurde für den Golden-Scope implementiert und validiert, allgemeine Coverage bleibt partial. | BLK-006, SEM-002, SEM-012, SEM-015 | SEM-011, SEM-014 | Zwischen semantischer Bedeutung und Syntax sind explizite Placement- und Formulation-Verantwortungen erforderlich. | TF3, HF | Nicht alle SysML-v2-Constructs abgedeckt. |
| **R8** | **Deterministische Assembly/Generation und getrennte Validation/Release** | BLK-007 zeigte einen unerlaubten Persona-Aufruf während Assembly. Nach Korrektur war Assembly deterministisch. Der Downstream erreichte generiertes SysML v2, externe SYSIDE-Validierung, Final Human Review und Publication als getrennte Zustände. | BLK-007, FINAL-G3, WP12-BLK-SEM015-L-001 | ADR-019/021/022/023 nur Design Rationale | Nach Human-authorized Meaning/Placement sollte downstream kein versteckter semantischer LLM-Entscheidungsschritt auftreten. Toolvalidität und Engineering Approval bleiben getrennt. | TF3, HF | SYSIDE PASS bedeutet nicht fachliche Korrektheit. |
| **R9** | **Genuine Multi-Source benötigt project-wide Identity, Provenance und Project Fit** | Der erste Multi-Source-Test scheiterte an BLK-002. Nach Korrektur durchlief Project 308131 den Genuine-Multi-Source-Pfad erfolgreich; source-lokale AEI-Sets und Provenance blieben getrennt, während ein gemeinsamer Project-level Candidate Path entstand. | BLK-002, FINAL-MS | finaler BLK-002-Closeout | Source-lokale Eindeutigkeit reicht für projektweite Verarbeitung nicht. Multi-Source benötigt project-aware Identity, Provenance, Admission und explizite Authority. | TF1, HF | Kein Nachweis für beliebige Source-Anzahl, langfristiges Change Management oder automatische Conflict Resolution. |
| **R10** | **PoC-Grenzen bleiben explizit** | Predicate-variant Consolidation, Relationship-Lifecycle-Automation, allgemeine SysML-v2-Construct-Coverage und allgemeine Target-Model Formulation sind teilweise offen. Mehrstufige LLM-Verarbeitung und Placement können zeit- bzw. interaktionsintensiv sein. | SEM-009, SEM-010, SEM-011, SEM-015, SEM-015-F01 | SEM-007, OBS-012, OBS-017, OBS-027, OBS-031 | Der PoC demonstriert Machbarkeit innerhalb eines kontrollierten Scopes, nicht Production Readiness oder universelle Übertragbarkeit. | alle / Limitationen | Offene Findings werden nicht als Failure des akzeptierten MVP umgedeutet, aber begrenzen den Claim. |

## Finding-Priorität nach Thesis-Relevanz

### Kernbelege im Haupttext

- BLK-002
- BLK-003
- BLK-005
- BLK-006
- BLK-007
- SEM-001
- SEM-002
- SEM-003
- SEM-004
- SEM-008
- SEM-011
- SEM-012
- SEM-013
- SEM-014
- SEM-015
- PASS-003
- PASS-004
- PASS-006
- PASS-007
- PASS-009
- PASS-010
- FINAL-G3
- FINAL-MS
- FINAL-REG

### Unterstützende formative Evidenz

- BLK-001
- BLK-004
- SEM-005
- SEM-007
- SEM-009
- SEM-010
- SEM-015-F01
- PASS-001
- PASS-002
- PASS-005
- PASS-008
- einzelne relevante OBS bei TF2 oder Systemgrenzen

### Anhang / UX / Repository-Transparenz

- SEM-006
- OBS-001 bis OBS-033
- OBS-DASH-001
- alle technischen Detailketten, die nicht zur Argumentation im Haupttext benötigt werden

## Kapitel-7-Regel

Kapitel 7 darf bei jedem Ergebniscluster nur dokumentieren:

- beobachteten Ausgangszustand,
- Finding oder Gate-Status,
- durchgeführte Korrektur, sofern für den Verlauf nötig,
- Retest-Status,
- quantitative Artefakt-/Testwerte.

Kapitel 7 formuliert **keine** Architekturerkenntnis.

## Kapitel-8-Regel

Kapitel 8 verwendet die zehn Cluster als argumentative Achsen. Dort werden:

1. Beobachtungen interpretiert,
2. mit Literatur abgeglichen,
3. Gegenbefunde und Limitationen diskutiert,
4. TF1, TF2, TF3 und HF beantwortet.

## Ergebnis

Die ursprünglichen 30 P1-Einzelfindings werden nicht als 30 „Ergebnisse“ behandelt. Sie sind jetzt auf **10 wissenschaftlich handhabbare Ergebniscluster** verdichtet. Die vollständige Finding-Historie bleibt separat im Anhang erhalten.
