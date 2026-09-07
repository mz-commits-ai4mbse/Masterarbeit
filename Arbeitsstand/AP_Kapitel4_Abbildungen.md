# AP Kapitel 4 – Abbildungen und Tabellen

## Ziel des Arbeitspakets

Dieses Arbeitspaket bündelt alle Abbildungs- und Tabellenkandidaten für Kapitel 4
„Konzeption der Informationsverarbeitungsarchitektur“.

Die Abbildungen sollen die Argumentation des Kapitels unterstützen und nicht das
CATIA-Modell vollständig reproduzieren. Maßgeblich ist deshalb nicht, wie viele
Modellelemente vorhanden sind, sondern welche Sichten dem Leser helfen, die
Architektur ohne Öffnen des `.mdzip` zu verstehen.

Leitprinzipien:

- Kapitel 4 bleibt auf Architekturkonzept und Engineering Information Flow fokussiert.
- CATIA-Abbildungen werden bevorzugt dort verwendet, wo bereits eine geeignete Präsentationssicht existiert.
- Vollständige Requirement-, Function-, Logical-Component-, Interface- oder Item-Flow-Listen gehören nicht in den Haupttext.
- Detailansichten dürfen nur aufgenommen werden, wenn sie einen zusätzlichen argumentativen Zweck erfüllen und nicht lediglich eine bereits gezeigte Sicht wiederholen.
- Tabellenüberschriften stehen oberhalb der Tabelle.
- Abbildungsunterschriften stehen unterhalb der Abbildung.
- Jede Abbildung und Tabelle wird im Fließtext explizit eingeführt und anschließend interpretiert.
- Abbildungen sollen möglichst auch für die spätere argumentative Rückbindung in Kapitel 5 und 6 nutzbar sein, ohne dort erneut vollständig erklärt werden zu müssen.

---

# 1. Priorisierte Hauptabbildungen

## Abb. 4.1 – Systemkontext und Stakeholder

**Priorität:** Muss

**Vorgesehener Abschnitt:** 4.1.1 System of Interest und externe Akteure

**Zweck:**
- Systemgrenze des Turing Generators zeigen
- Engineering Sources als externe Informationsquelle darstellen
- vier menschliche Stakeholderrollen zeigen
- sichtbar machen, dass Human Authority nicht im LLM oder in externen Quellen liegt

**CATIA-Kandidat:**
- `IV_TuringGeneratorStakeholderContext`

**Darzustellende Elemente:**
- Turing Generator System
- Engineering Sources
- ACT_01 System Modeler
- ACT_02 Domain Expert
- ACT_03 System Architect
- ACT_04 Information Manager
- relevante Context Interactions

**Nicht darstellen:**
- interne System Functions
- Logical Components
- Subsysteme
- Implementierungsdetails

**Arbeitsauftrag:**
- CATIA-View auf Lesbarkeit prüfen
- ggf. Layout für Thesis optimieren
- nur technisch notwendige Beschriftungen einblenden
- Export in vektororientiertem oder hochauflösendem Format
- Caption und Fließtextreferenz finalisieren

**Arbeitstitel Caption:**
„Systemkontext des Turing Generators mit externen Engineering Sources und menschlichen Stakeholderrollen“

---

## Abb. 4.2 – End-to-End Engineering Information Flow

**Priorität:** Muss, zentrale Red-Thread-Abbildung des Kapitels

**Vorgesehener Abschnitt:** 4.2.1 Funktionale Verarbeitungskette

**Zweck:**
- gesamten Weg der Engineering-Information von Source bis Publication zeigen
- zentrale Transformationen sichtbar machen
- Leserführung für Kapitel 4, 5 und 6 schaffen
- verdeutlichen, dass der rote Faden nicht „LLM → SysML-Code“, sondern eine kontrollierte Folge von Informations- und Authority-Zuständen ist

**Primärer CATIA-Kandidat:**
- `GV_EngineeringDataTransformationFlow`

**Möglicher ergänzender CATIA-Kandidat:**
- `AFV_EngineeringTransformationLifecycle_Overview`
  nur falls die Rework-/Gate-Logik in einer separaten Darstellung wirklich benötigt wird

**Inhalt der verdichteten Hauptsicht:**
- Engineering Source
- Project / Source Context
- Process Information
- Human Review
- Project Fit
- Derive Architecture
- Architecture Validation
- SysML v2 Generation
- SysML v2 Validation
- Final Human Model Review
- Publication

**Gestaltungsziel:**
- eine verständliche horizontale oder vertikale Hauptkette
- Human Review und Project Fit optisch als Gates erkennbar
- LLM nicht als „Hauptsystem“ ins Zentrum stellen
- Evidence / Provenance / Traceability ggf. als durchgehende Querschnittslinie oder bewusst erst in Abb. 4.4 zeigen

**Nicht überladen mit:**
- allen 13 System Functions
- allen Rework-Schleifen
- vollständiger State Machine
- allen internen Schritten von SFB_005
- allen Model-Candidate-/Placement-Unterstufen

**Arbeitsauftrag:**
- prüfen, ob CATIA-View für Thesis direkt ausreichend ist
- falls zu technisch: CATIA als inhaltliche Basis verwenden und eine präsentationsorientierte Red-Thread-Grafik daraus ableiten
- so gestalten, dass sie in Kapitel 5 und 6 referenziert werden kann

**Arbeitstitel Caption:**
„End-to-End Engineering Information Flow des Turing Generators“

---

## Abb. 4.3 – Logische Architektur und Subsystemdekomposition

**Priorität:** Muss

**Vorgesehener Abschnitt:** 4.3.2 / 4.3.3

**Zweck:**
- von der funktionalen Sicht zur Logical Architecture überleiten
- neun Subsysteme als stabile Verantwortungsgrenzen darstellen
- Cross-SBS-Interaktionen sichtbar machen
- responsibility-based Zerlegung statt sequenzieller Prozesszerlegung zeigen

**Primärer CATIA-Kandidat:**
- `SBSLA_10 Turing Generator Cross-Subsystem Logical Architecture`

**Mögliche ergänzende CATIA-Kandidaten:**
- `IV_TuringGeneratorLogicalArchitecture`
- `TuringGeneratorLogicalArchitecture_Presentation`

**Entscheidungskriterium:**
Die Hauptabbildung soll die **neun SBS** zeigen. Die System-Level Logical Architecture ist nur dann zusätzlich sinnvoll, wenn sie einen klaren Erkenntnisgewinn gegenüber der Cross-SBS-Sicht bietet.

**Darzustellende Subsysteme:**
1. SBS_01 User Interaction and Guided Workflow
2. SBS_02 Project and Source Context Management
3. SBS_03 Processing Orchestration and State Control
4. SBS_04 Engineering Information Processing
5. SBS_05 Human Review and Engineering Authority
6. SBS_06 Evidence Coverage and Traceability
7. SBS_07 Architecture Derivation and Model Assembly
8. SBS_08 SysML v2 Artifact Generation
9. SBS_09 Generated Artifact Validation and Publication

**Wichtig:**
- SBS_09 nicht als alleinige Publication Authority erscheinen lassen
- Interfaces nur soweit einblenden, wie sie für die Verantwortungsabgrenzung nötig sind
- keine Implementation Modules oder Python Packages zeigen
- externe Kontexte ggf. in derselben Abbildung oder als kleine Randgruppe zeigen:
  - LLM Inference Context
  - Semantic Reference Knowledge
  - Target Model and Framework References
  - SysML v2 Modeling References

**Arbeitsauftrag:**
- beide CATIA-Sichten gegen Lesbarkeit vergleichen
- entscheiden, ob externe Kontexte in Abb. 4.3 integriert oder als kleine ergänzende Darstellung innerhalb 4.3 behandelt werden
- Cross-SBS Item Flows stark reduzieren bzw. nur presentation-relevant zeigen

**Arbeitstitel Caption:**
„Verantwortungsbasierte logische Architektur und Subsystemdekomposition des Turing Generators“

---

## Abb. 4.4 – Authority- und Traceability-Kette

**Priorität:** Hoch

**Vorgesehener Abschnitt:** 4.4.1 / 4.4.2

**Zweck:**
- zentrale Forschungslogik von Evidence, Provenance und Human Authority zeigen
- Informationszustände und Authority-Grenzen in einer einzigen vereinfachten Sicht verbinden
- zeigen, welche Schritte Evidence erzeugen und welche Schritte Authority etablieren

**Vorgesehene konzeptionelle Kette:**
- Engineering Source
- Source-Grounded Evidence
- Engineering Interpretation / Candidate
- Human Engineering Review
- Approved Engineering Information
- Project Fit / Admission
- Model Candidate
- Human Model Placement Review
- Internal Engineering Model
- Generated SysML Artifact Set
- SysML Validation Result
- Final Human Model Review
- Publication Authorization

**Parallel / unterhalb:**
Provenance-Backbone mit:
- Project
- Source
- Processing Run
- Processing Attempt
- Human Decision
- Artifact Identity / Fingerprint
- downstream Model / Generated Artifact

**Mögliche CATIA-Grundlagen:**
- SBS_05 Human Review and Engineering Authority
- SBS_06 Evidence Coverage and Traceability
- System Function Interaction Network
- vorhandene Evidence-Interaktionen der Logical Architecture

**Empfehlung:**
Diese Abbildung muss nicht zwingend ein 1:1-CATIA-Screenshot sein. Eine bewusst reduzierte Thesis-Grafik kann hier besser sein, solange sie exakt aus dem akzeptierten Modell abgeleitet wird.

**Wichtig hervorheben:**
- LLM / Persona Agreement = Evidence, nicht Approval
- technische Validation = keine Engineering Authority
- Human Decisions sind an exakte Review-Gegenstände gebunden
- Publication setzt technische und menschliche Bedingungen voraus

**Arbeitsauftrag:**
- prüfen, ob eine vorhandene CATIA-Sicht ausreichend verständlich ist
- sonst aus CATIA-Modell eine reduzierte Authority-/Traceability-Grafik ableiten
- mögliche Doppelfunktion mit Abb. 4.2 vermeiden

**Arbeitstitel Caption:**
„Authority-Grenzen und End-to-End-Traceability entlang der Informationsverarbeitung“

---

## Abb. 4.5 – Multi-Source Project Fit und Modellableitung

**Priorität:** Mittel bis hoch

**Vorgesehener Abschnitt:** 4.5.1 / 4.5.2

**Zweck:**
- den für die Thesis wichtigen Multi-Source-Sonderfall verständlich machen
- source-lokale Verarbeitung von projektweiter Modellableitung trennen
- zeigen, dass keine synthetische merged Approved Engineering Information erzeugt wird
- Project Fit als Admission-Grenze erklären

**Darstellungslogik:**

Source A<br>
→ source-local processing<br>
→ Human Engineering Review<br>
→ Approved Engineering Information A

Source B<br>
→ source-local processing<br>
→ Human Engineering Review<br>
→ Approved Engineering Information B

anschließend:

A + B<br>
→ jeweils Project Fit<br>
→ Project-Admitted source-local Engineering Information<br>
→ ONE Project-level Model Candidate Set<br>
→ Human Candidate Review<br>
→ Placement Proposal<br>
→ Human Model Placement Review<br>
→ Internal Engineering Model

**Wichtig:**
- Source Provenance bleibt erhalten
- source-lokale Authority bleibt erhalten
- keine automatische Source-Hierarchie
- keine automatische Cross-Source-Truth-Arbitration
- kein synthetisches merged AEI als neue Authority-Stufe
- Model Candidate Set ist abgeleitete Architekturinformation

**Mögliche CATIA-Grundlagen:**
- `UC_007 Assess Project Source Admissibility`
- Project Fit Admission Behavior in SFB_004
- Project Model Synthesis in SFB_004
- SBS_07 Project Model Candidate Derivation and Target Projection

**Entscheidungskriterium:**
Nur aufnehmen, wenn Abb. 4.2 den Multi-Source-Sonderfall nicht bereits so klar transportiert, dass eine weitere Abbildung redundant wäre.

**Arbeitstitel Caption:**
„Source-lokale Autorisierung, Project Fit und projektweite Modellableitung bei mehreren Engineering Sources“

---

# 2. Optionale Zusatzabbildungen

## Kandidat A – Detaillierte source-lokale Informationsverarbeitung

**CATIA-Kandidat:**
- `GV_EngineeringInformationProcessingFlow`

**Möglicher Abschnitt:** 4.2.1

**Inhalt:**
- deterministic Source Projection
- Source Analysis Units
- source-grounded Evidence
- Canonical Engineering Subjects
- Persona Interpretation
- Consensus / Variance
- Semantic Governance
- Coverage / Evidence Assessment
- Human Review Package

**Bewertung:**
Nur aufnehmen, wenn die interne Verarbeitung in Abb. 4.2 zu stark kollabiert ist und der Text ohne zweite Funktionssicht schwer verständlich bleibt.

**Risiko:**
Hohe Doppelung mit Kapitel 3.4 sowie mit späterer PoC-Darstellung in Kapitel 5.

**Status:** optional, eher Anhang oder Kapitel 5 als zusätzliche Hauptabbildung

---

## Kandidat B – Mapping des Drei-Schichten-Modells auf die finale Architektur

**CATIA-Kandidat:**
- `GV_ThreeLayerArchitectureMapping`

**Möglicher Abschnitt:** 4.2.3 Einordnung des initialen Drei-Schichten-Modells

**Zweck:**
- explizit zeigen, wie Data, Process und Knowledge Layer aus der frühen Lösungshypothese auf die finale Logical Architecture abgebildet werden
- Entwicklung von Kapitel 3 zu Kapitel 4 sichtbar machen

**Stärken:**
- Modell existiert bereits
- schafft direkte Rückbindung an die frühe Lösungshypothese
- zeigt besonders gut, dass Knowledge/Governance querschnittlich wirken

**Risiko:**
- kann die RFL-Logik unnötig mit einer zweiten Architekturklassifikation überlagern
- könnte an dieser Stelle bereits Teile von 4.3 vorwegnehmen

**Entscheidung:**
Nach Einfügen von Abb. 4.2 und Abb. 4.3 prüfen. Nur verwenden, wenn sie den Übergang von der Lösungshypothese zur finalen Architektur wirklich verständlicher macht.

**Status:** optional

---

## Kandidat C – Use-Case-Übersicht

**CATIA-Kandidat:**
- `GV_UseCaseOverview`

**Möglicher Abschnitt:** 4.1

**Zweck:**
- Stakeholderinteraktion und funktionale Intention zeigen

**Bewertung:**
Eher nicht im Haupttext erforderlich, da 4.1 bereits System Context, User Needs / Requirements und eine repräsentative Requirement-Tabelle enthält.

**Mögliche Verwendung:**
- Anhang
- nur falls 4.1 ohne Use-Case-Sicht zu abstrakt bleibt

**Status:** niedrige Priorität

---

## Kandidat D – Engineering Lifecycle mit Gates und Rework

**CATIA-Kandidat:**
- `AFV_EngineeringTransformationLifecycle_Overview`

**Möglicher Abschnitt:** 4.2.2 Informationszustände und Kontrollgrenzen

**Zweck:**
- fail-closed Gates
- Rework
- Human Review
- Project Fit
- Final Publication Conditions

**Bewertung:**
Nur sinnvoll, wenn Abb. 4.2 bewusst sehr kompakt bleibt und die Gate-/Rework-Logik sonst nicht angemessen visualisiert werden kann.

**Risiko:**
Kann sehr technisch und groß werden.

**Status:** optional, ggf. eher Kapitel 6 für Verifikationslogik referenzieren

---

# 3. Tabellenplan

## Tab. 4.1 – Architekturtreibende User Needs und Anforderungen

**Status:** bereits in 4.1 vorgesehen

**Zweck:**
Repräsentative Verbindung von:
- Bedarfskategorie
- User Needs
- Stakeholder Requirements
- Architekturwirkung

**Wichtig:**
Keine vollständige Requirement-Liste.

---

## Tab. 4.2 – Neun Subsysteme und zentrale Verantwortlichkeiten

**Status:** bereits in 4.3 vorgesehen

**Zweck:**
Die neun Subsysteme kompakt lesbar machen und Abb. 4.3 ergänzen.

**Wichtig:**
- exakte CATIA-Subsystemnamen verwenden
- keine vollständigen Subsystem Requirements / Functions / Logical Components
- ggf. Spalte „zentrale Boundary“ ergänzen, falls dies nach Einfügen der Abbildung noch einen Mehrwert bietet

---

## Optional Tab. 4.3 – Authority-Stufen

**Priorität:** niedrig bis mittel

**Möglicher Abschnitt:** 4.4

**Mögliche Spalten:**
- Authority-/Kontrollstufe
- Review-Gegenstand
- Entscheidung
- resultierender Status
- verantwortlicher Architekturkontext

**Beispiele:**
- Human Engineering Review
- Project Fit
- Human Candidate Review
- Human Model Placement Review
- Technical Validation
- Final Human Model Review
- Publication Gate

**Entscheidung:**
Nur nutzen, wenn Abb. 4.4 die unterschiedlichen Authority-Stufen nicht bereits klar genug transportiert.

---

# 4. Empfohlener finaler Haupttextumfang

## Mindestset

1. Abb. 4.1 System Context
2. Tab. 4.1 Architekturtreibende User Needs / Requirements
3. Abb. 4.2 End-to-End Engineering Information Flow
4. Abb. 4.3 Logical Architecture / neun Subsysteme
5. Tab. 4.2 Subsysteme und Verantwortlichkeiten
6. Abb. 4.4 Authority / Traceability

Dieses Set deckt die zentrale Argumentation des Kapitels vollständig ab.

## Erweiterungsset

Zusätzlich maximal:

7. Abb. 4.5 Multi-Source Project Fit / Model Derivation
8. optional `GV_ThreeLayerArchitectureMapping`

Mehr Hauptabbildungen sind voraussichtlich nicht sinnvoll. Weitere CATIA-Views sollten bei Bedarf in den Anhang oder als Modell-/Implementierungsevidenz in Kapitel 5 und 6 verwendet werden.

---

# 5. Abbildungslogik entlang des Kapitels

Die Abbildungen sollen gemeinsam eine konsistente Leserführung bilden:

**Abb. 4.1**<br>
„Was liegt innerhalb und außerhalb des Systems?“

↓

**Abb. 4.2**<br>
„Wie bewegt sich Engineering Information funktional durch das System?“

↓

**Abb. 4.3**<br>
„Welche logischen Verantwortlichkeiten tragen diese Verarbeitung?“

↓

**Abb. 4.4**<br>
„Wie bleiben Evidence, Traceability und Human Authority entlang dieses Weges kontrolliert?“

↓

**Abb. 4.5**<br>
„Wie funktioniert dieser Ansatz im anspruchsvollen Multi-Source-Fall?“

Damit folgt die Visualisierung derselben Argumentation wie der Kapitelaufbau: System Context → Functional Flow → Logical Architecture → Cross-Cutting Governance → Multi-Source Deep Dive.

---

# 6. Gestaltungsregeln für alle Abbildungen

- gleiche Typografie und vergleichbare Schriftgröße
- gleiche Benennung zentraler Informationsobjekte wie im Fließtext
- keine unnötigen internen IDs in der sichtbaren Grafik
- SBS-IDs dürfen bei der Logical Architecture sichtbar bleiben
- Human Review / Authority optisch konsistent darstellen
- deterministische Verarbeitung und KI-gestützte Interpretation unterscheidbar machen, aber nicht durch übermäßig viele Farben codieren
- Source Provenance bei Multi-Source immer sichtbar erhalten
- technische Validation niemals als Human Approval darstellen
- keine Python-Module, Klassen oder Repository-Pfade in Kapitel-4-Abbildungen
- keine Physical-/Deployment-Sicht ergänzen
- CATIA-Screenshots ggf. zuschneiden und auf Thesis-Lesbarkeit optimieren
- wenn eine CATIA-View inhaltlich korrekt, aber visuell ungeeignet ist: präsentationsorientierte Grafik aus dem akzeptierten Modell ableiten und die Modellinhalte nicht verändern

---

# 7. Wiederverwendung in späteren Kapiteln

## Kapitel 5

Abb. 4.2 kann als Referenzstruktur für die prototypische Realisierung dienen. Kapitel 5 sollte dann je Verarbeitungsbereich auf die Architektur zurückverweisen, statt den Gesamtfluss erneut vollständig zu erklären.

Abb. 4.3 kann zur Zuordnung von Implementierungsbausteinen zu Architekturverantwortlichkeiten verwendet werden.

## Kapitel 6

Abb. 4.2 und Abb. 4.4 können zur Strukturierung der Verifikation und Validierung verwendet werden:
- welche Processing-/Authority-Grenzen geprüft wurden
- welche Evidence für einen PASS erforderlich ist
- welche technische Validation getrennt von Human Review bewertet wird

## Kapitel 8

Abb. 4.4 und Abb. 4.5 können bei der Diskussion der Forschungsfragen, Human Authority, Traceability und Multi-Source-Grenzen referenziert werden.

---

# 8. Abschluss-Checkliste des Arbeitspakets

- [ ] Abb. 4.1 aus CATIA exportiert und lesbar gemacht
- [ ] Tab. 4.1 final gegen CATIA-User-Needs / Requirements geprüft
- [ ] Abb. 4.2 als zentrale Red-Thread-Abbildung finalisiert
- [ ] entschieden, ob `AFV_EngineeringTransformationLifecycle_Overview` zusätzlich nötig ist
- [ ] Abb. 4.3 mit allen neun SBS finalisiert
- [ ] externe Architekturkontexte in 4.3 sinnvoll dargestellt
- [ ] Tab. 4.2 exakte Subsystemnamen gegen CATIA geprüft
- [ ] Abb. 4.4 Authority / Traceability erstellt
- [ ] entschieden, ob Abb. 4.5 Multi-Source im Haupttext erforderlich ist
- [ ] entschieden, ob `GV_ThreeLayerArchitectureMapping` Mehrwert bietet
- [ ] redundante Views entfernt oder in den Anhang verschoben
- [ ] alle Abbildungen im Fließtext eingeführt und interpretiert
- [ ] Captions finalisiert
- [ ] Labels konsistent vergeben
- [ ] Abbildungsnummern nach finaler Reihenfolge geprüft
- [ ] Tabellenüberschriften oberhalb, Abbildungsunterschriften unterhalb
- [ ] Kapitel 4 auf Seitenausgleich nach Einfügen aller Grafiken geprüft
- [ ] erst danach endgültige Layout-/Float-Optimierung durchführen

---

# 9. Vorläufige Empfehlung

Für den Haupttext sollte zunächst mit **vier Pflichtabbildungen** geplant werden:

1. System Context
2. End-to-End Engineering Information Flow
3. Logical Architecture / neun Subsysteme
4. Authority / Traceability

Die Multi-Source-Abbildung sollte als fünfte Abbildung ergänzt werden, wenn nach Einfügen der ersten vier sichtbar wird, dass Project Fit und source-lokale Authority im End-to-End-Flow nicht ausreichend verständlich werden.

Das Drei-Schichten-Mapping bleibt ein guter Reservekandidat, sollte aber erst nach dem Zusammenspiel von Abb. 4.2 und Abb. 4.3 entschieden werden. Dadurch wird vermieden, die finale RFL-Argumentation durch eine zweite Architekturklassifikation unnötig zu überlagern.
