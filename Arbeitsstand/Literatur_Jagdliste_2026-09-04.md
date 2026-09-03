# Literatur-Jagdliste für 04.09.2026

**Ziel:** Morgen nur die Quellen besorgen, die im aktuellen Thesis-Bestand fehlen oder für zentrale Claims noch nicht ausreichend abgesichert sind.
**Nicht Ziel:** bereits vorhandene Literatur erneut suchen.

## Vorgehen morgen

Für jede Quelle möglichst:

1. Volltext-PDF oder offizielle Standardfassung besorgen.
2. Datei unter `Literatur/` sinnvoll benennen.
3. BibTeX / DOI sichern.
4. Noch **nicht** aus Abstracts Claims ableiten. Claimwise Reading folgt danach.
5. Nach Abschluss gesammelt committen und pushen.

---

# A — MUST: zuerst besorgen

## A1 — Wieringa 2014: Design Science Methodology

**Quelle:** Roel J. Wieringa, *Design Science Methodology for Information Systems and Software Engineering*, Springer, 2014.
**DOI:** `10.1007/978-3-662-43839-8`
**Priorität:** MUST
**Kapitel:** 3, ergänzend 8.10
**Wofür benötigt:**
- designorientierte Forschungslogik,
- Iteration zwischen Artefaktgestaltung und empirischer Untersuchung,
- Treatment/Artifact Validation,
- methodische Einordnung des Architektur- und PoC-Vorgehens.

**Was wir ausdrücklich NICHT ungeprüft behaupten:** Dass die Thesis automatisch eine vollständige DSR-Studie nach Wieringa ist. Erst Volltext gegen tatsächliches Vorgehen abgleichen.

**Fundort-Tipp:** Springer / HM-Bibliothek.

---

## A2 — Mosqueira-Rey et al. 2023: Human-in-the-loop ML State of the Art

**Quelle:** Eduardo Mosqueira-Rey et al., “Human-in-the-loop machine learning: a state of the art,” *Artificial Intelligence Review*, 56, 3005–3054, 2023.
**DOI:** `10.1007/s10462-022-10246-w`
**Priorität:** MUST
**Kapitel:** 2.6, 8.3, TF2
**Wofür benötigt:**
- Begriffsabgrenzung HITL,
- unterschiedliche Kontroll-/Interaktionsformen,
- Rolle von Domain Experts,
- wissenschaftliche Basis für Human Authority und Intervention.

**Zugang:** Open Access.

---

## A3 — Lazaros et al. 2026: HITL Systematic Review

**Quelle:** Konstantinos Lazaros, Aristidis G. Vrahatis, Sotiris Kotsiantis, “Human-in-the-Loop Artificial Intelligence: A Systematic Review of Concepts, Methods, and Applications,” *Entropy*, 28(4), 377, 2026.
**DOI:** `10.3390/e28040377`
**Priorität:** MUST / sehr aktuell
**Kapitel:** 2.6, 8.3, 9.3.2
**Wofür benötigt:**
- aktueller systematischer Überblick zu Human Oversight, Feedback und Decision-Making,
- Einordnung des Adaptive-Human-Feedback-Ausblicks,
- Abgrenzung von Human Authority und automatisierter Unterstützung.

**Zugang:** Open Access.

---

## A4 — Bucaioni et al. 2025: Architecture as Code

**Quelle:** Alessio Bucaioni, Amleto Di Salle, Ludovico Iovino, Patrizio Pelliccione, Franco Raimondi, “Architecture as Code,” *IEEE International Conference on Software Architecture (ICSA)*, 2025, pp. 187–198.
**DOI:** `10.1109/ICSA65012.2025.00027`
**Priorität:** MUST
**Kapitel:** 2.3, 8.6, ggf. 1 Motivation
**Wofür benötigt:**
- wissenschaftliche Definition und Motivation von Architecture as Code,
- Versionierbarkeit und Alignment von Architekturartefakten,
- Abgrenzung zu bloßer textueller Modellnotation.

**Fundort-Tipp:** DiVA / IEEE / Autorenversion.

---

## A5 — Stirbu et al. 2022: Everything as Code

**Quelle:** Vlad Stirbu, Mikko Raatikainen, Joel Röntynen, Vlas Sokolov, Timo Lehtonen, Tommi Mikkonen, “Toward Multiconcern Software Development With Everything as Code,” *IEEE Software*, 39(4), 27–33, 2022.
**DOI:** `10.1109/MS.2022.3167481`
**Priorität:** MUST
**Kapitel:** 2.3, 8.6
**Wofür benötigt:**
- Everything-as-Code-Kontext,
- maschinenlesbare, versionskontrollierte Artefakte im Entwicklungsworkflow,
- breitere Einordnung des Architecture-as-Code-Gedankens.

**Zugang:** Autorenversion / University of Helsinki Repository verfügbar.

---

## A6 — Topcu et al. 2025: LLM Failure Modes in Systems Engineering

**Quelle:** Taylan G. Topcu, Mohammed Husain, Max Ofsa, Paul Wach, “Trust at Your Own Peril: A Mixed Methods Exploration of the Ability of Large Language Models to Generate Expert-Like Systems Engineering Artifacts and a Characterization of Failure Modes,” *Systems Engineering*, 28(5), 583–604, 2025.
**DOI:** `10.1002/sys.21810`
**Priorität:** MUST
**Kapitel:** 2.5, 8.2–8.4, 8.9
**Wofür benötigt:**
- Grenzen und Failure Modes von LLMs bei Systems-Engineering-Artefakten,
- wissenschaftliche Absicherung gegen Overclaiming,
- Begründung für Grounding, Review und Authority Boundaries.

---

## A7 — Stein et al. 2026: Prompt-Strategy-Driven SysML-v2 Artefact Generation

**Quelle:** Armin Stein et al., “Prompt-Strategy-Driven SysML-v2 Artefact Generation Using Large Language Models for Model-Based Systems Engineering,” *AI*, 7(8), 274, 2026.
**DOI:** `10.3390/ai7080274`
**Veröffentlicht:** 23.07.2026
**Priorität:** MUST / aktuellster direkter Wettbewerbsbezug
**Kapitel:** 2.5, 2.9, 2.10, 8.6
**Wofür benötigt:**
- aktueller Stand LLM → SysML-v2-Artefakte,
- Prompt-Strategien,
- Syntaxvalidation und Qualitätsbewertung,
- Abgleich zur eigenen Verantwortungskette und Forschungslücke.

**Zugang:** Open Access.

---

## A8 — W3C PROV-DM

**Quelle:** Luc Moreau, Paolo Missier (eds.), *PROV-DM: The PROV Data Model*, W3C Recommendation, 30.04.2013.
**Offizielle Referenz:** `https://www.w3.org/TR/prov-dm/`
**Priorität:** MUST
**Kapitel:** 2.8, 4.6, 8.5
**Wofür benötigt:**
- belastbare Definition von Provenance,
- Entities, Activities, Agents, Derivations,
- Einordnung von Provenance in heterogenen Informationsflüssen.

**Hinweis:** Kein DOI nötig. Offizielle W3C-Recommendation ist die Primärquelle.

---

## A9 — Li, Lockett & Lawson 2020: RFLP

**Quelle:** Tao Li, Helen Lockett, Craig Lawson, “Using requirement-functional-logical-physical models to support early assembly process planning for complex aircraft systems integration,” *Journal of Manufacturing Systems*, 54, 242–257, 2020.
**DOI:** `10.1016/j.jmsy.2020.01.001`
**Priorität:** MUST
**Kapitel:** 2.1 / 3.3 / 4, je nach finaler RFLP-Einordnung
**Wofür benötigt:**
- wissenschaftliche Referenz für RFLP als Requirements–Functional–Logical–Physical-Modellierungslogik,
- Kontext für die verwendete R/F/L-Ableitung.

**Wichtige Korrektur:** Frühere Notiz „Li, Verhagen & Curran“ war nicht die sauber verifizierte Referenz. Für diesen Claim verwenden wir **Li, Lockett & Lawson 2020**.

---

## A10 — Triem & Ding 2024: Human Intervention in Multi-Agent Debate

**Quelle:** Haley Triem, Ying Ding, “‘Tipping the Balance’: Human Intervention in Large Language Model Multi-Agent Debate,” *Proceedings of the Association for Information Science and Technology*, 61, 361–373, 2024.
**DOI:** `10.1002/pra2.1034`
**Priorität:** MUST/HIGH
**Kapitel:** 2.5/2.6, 8.3
**Wofür benötigt:**
- Human Intervention in einem Multi-Agent-/Debate-Kontext,
- wissenschaftlicher Gegen-/Vergleichspunkt zu Persona Consensus, Variance und Human Authority.

**Claim Boundary:** Nicht direkt auf Engineering übertragen; als verwandter Mechanismus diskutieren.

---

## A11 — Kitchenham & Charters 2007: Systematic Literature Review

**Quelle:** Barbara Kitchenham, Stuart Charters, *Guidelines for performing Systematic Literature Reviews in Software Engineering*, EBSE Technical Report EBSE-2007-01, Version 2.3, 2007.
**DOI:** **kein DOI**
**Priorität:** HIGH
**Kapitel:** 3.2
**Wofür benötigt:**
- SE-spezifische methodische Basis für strukturierte Literaturrecherche,
- Ergänzung zu PRISMA und Wohlin.

**Hinweis:** Nicht nach einem DOI suchen. EBSE führt ausdrücklich keinen DOI.

---

## A12 — ISO/IEC/IEEE 29148:2018

**Quelle:** *ISO/IEC/IEEE 29148:2018 — Systems and software engineering — Life cycle processes — Requirements engineering*.
**Priorität:** HIGH
**Kapitel:** 2 / 3 / 4, je nach finalem Requirements-Claim
**Wofür benötigt:**
- Requirements-Engineering-Grundlage,
- Eigenschaften und Behandlung von Requirements,
- methodische Einordnung von Constraints.

**Hinweis:** Standard ist voraussichtlich über Hochschulzugang zu beschaffen. Nicht zwingend erforderlich, wenn der konkrete Claim anders bereits sauber belegt ist.

---

# B — HIGH: danach besorgen

## B1 — Peffers et al.: DSR Methodology

**Quelle:** Ken Peffers, Tuure Tuunanen, Marcus A. Rothenberger, Samir Chatterjee, “A Design Science Research Methodology for Information Systems Research,” *Journal of Management Information Systems*, 24(3), 45–77.
**DOI:** `10.2753/MIS0742-1222240302`
**Kapitel:** 3
**Nutzen:** zweite methodische DSR-Referenz neben Wieringa.
**Entscheidung später:** Wenn Wieringa das tatsächliche Vorgehen bereits vollständig abdeckt, nur ergänzend verwenden.

---

## B2 — Du et al. 2024: Multiagent Debate

**Quelle:** Yilun Du, Shuang Li, Antonio Torralba, Joshua B. Tenenbaum, Igor Mordatch, “Improving Factuality and Reasoning in Language Models through Multiagent Debate,” ICML 2024, PMLR 235, 11733–11763.
**Offizielle Quelle:** PMLR
**Kapitel:** 2.5, 8.3
**Nutzen:** wissenschaftliche Basis für Multi-Agent-Debate und unterschiedliche Agentenperspektiven.

**Claim Boundary:** Die berichteten Verbesserungen bei Reasoning/Factuality nicht auf den Turing Generator übertragen.

---

## B3 — Simmhan, Plale & Gannon 2005: Provenance Survey

**Quelle:** Yogesh L. Simmhan, Beth Plale, Dennis Gannon, “A survey of data provenance in e-science,” *ACM SIGMOD Record*, 34(3), 31–36, 2005.
**DOI:** `10.1145/1084805.1084812`
**Kapitel:** 2.8, 8.5
**Nutzen:** wissenschaftliche Ergänzung zu W3C PROV, falls eine breitere Provenance-Diskussion nötig ist.

---

## B4 — Barke, James & Polikarpova 2023: Grounded Copilot

**Quelle:** Shraddha Barke, Michael B. James, Nadia Polikarpova, “Grounded Copilot: How Programmers Interact with Code-Generating Models,” *Proceedings of the ACM on Programming Languages*, 7(OOPSLA1), 2023.
**DOI:** `10.1145/3586030`
**Kapitel:** 8.9
**Nutzen:** qualitative wissenschaftliche Basis für die Reflexion über AI als Coding-/Development-Assistant.

---

## B5 — Using AI-based Coding Assistants in Practice, 2025

**Quelle:** “Using AI-based coding assistants in practice: State of affairs, perceptions, and ways forward,” *Information and Software Technology*, 178, 107610, 2025.
**DOI:** `10.1016/j.infsof.2024.107610`
**Kapitel:** 8.9
**Nutzen:** breite empirische Entwicklerperspektive; Survey mit 481 Programmierern zu Einsatzmustern von Coding Assistants.

---

## B6 — Hendriks et al.: MBSE in High-Tech Equipment

**Bevorzugt peer-reviewed:** Hendriks et al., “Creating Value with MBSE in the High-Tech Equipment Industry,” *INSIGHT*.
**DOI:** `10.1002/inst.12409`
**Zusätzlich optional:** TNO-ESI Report 2025, *MBSE in the High-Tech Equipment Industry*, TNO 2025 R11769.
**Kapitel:** 1 Motivation, ggf. 2
**Nutzen:** Brownfield-/Industriekontext und Praxisrelevanz.

---

# C — OPTIONAL: nur wenn gut verfügbar oder beim Schreiben noch Claim-Lücke bleibt

## C1 — W3C PROV Primer
Offizielle Ergänzung zu PROV-DM für verständliche Beispiele.
`https://www.w3.org/TR/prov-primer/`

## C2 — Wang et al. 2024: Divergent Thinking through Multi-Agent Debate
Für Persona-Diversität, nur bei echter Claim-Lücke.

## C3 — Li et al. 2024: Sparse Communication Topology
Für Multi-Agent-Effizienz, nur wenn Laufzeit-/Kommunikationsargument stärker wird.

## C4 — Pontillo et al. 2026: Architecture as Code in Industry
Aktuelle Industrie-Fallstudie. Nur ergänzend, weil Bucaioni 2025 die Kernquelle ist.

## C5 — CONTAaC 2026
Continuous Architecting as Code. Nur für erweiterten Ausblick.

## C6 — Peng et al. 2023: GitHub Copilot Productivity
DOI/arXiv: `10.48550/arXiv.2302.06590`. Nur verwenden, wenn im AI-Coding-Abschnitt wirklich ein quantitativer Productivity-Vergleich benötigt wird.

---

# Nicht erneut suchen: bereits im Thesis-Literaturbestand

Der aktuelle Repository-Bestand enthält bereits zentrale Literatur zu:

- PRISMA / Page
- Wohlin Snowballing
- Wohlin Experimentation in Software Engineering
- Petticrew
- GRADE
- ISO/IEC/IEEE 42010:2022
- Abonyi 2024
- Ahlbrecht 2024
- Pan: LLM-enabled Instance Model Generation
- Cibrian: agent-based SysML-v2 generation
- Cibrian: semantic consistency / metamodel-driven validation
- QVT-/XMI-basierte Transformationen
- Zhao 2023
- heterogene Daten-/Knowledge-Base-Arbeiten aus der Literatursuche
- Ontologie-Literatur im Ordner `Literatur/Ontologien`
- Sheard 2025
- `2605.14163v1.pdf`
- `2605.18747v1.pdf`
- Interpretable Context Methodology

Diese Quellen werden nach der Jagd **claimweise gelesen und bewertet**, aber morgen nicht doppelt beschafft.

---

# Bibliographische Korrektur, keine Literaturjagd

## OMG SysML v2

Der bestehende BibTeX-Eintrag `SysMLv2` führt aktuell das Jahr 2024. Für die Thesis soll auf die offizielle formale Spezifikation korrigiert werden:

**Object Management Group**, *OMG Systems Modeling Language (SysML), Version 2.0, Part 1: Language Specification*, OMG Document `formal/2026-03-02`, March 2026.

Offizielle Spezifikationsseite:
`https://www.omg.org/spec/SysML/2.0/`

Die bereits vorhandene offizielle Spezifikation ist die normative Primärquelle für SysML-v2-Syntax- und Sprachclaims.

---

# Morgen: Minimalziel

Wenn du morgen nur die wichtigsten Quellen sicher bekommst, sind diese zehn ausreichend:

1. Wieringa 2014
2. Mosqueira-Rey et al. 2023
3. Lazaros et al. 2026
4. Bucaioni et al. 2025
5. Stirbu et al. 2022
6. Topcu et al. 2025
7. Stein et al. 2026
8. W3C PROV-DM
9. Li, Lockett & Lawson 2020
10. Triem & Ding 2024

Kitchenham und ISO 29148 direkt danach.

---

# Nach der Jagd

Erst wenn die PDFs im Repository liegen:

1. Bestand prüfen,
2. BibTeX ergänzen/korrigieren,
3. Quellen **claimweise** lesen,
4. Claim → Volltextstelle → Kapitel mappen,
5. dann mit der eigentlichen Ausformulierung von Kapitel 3–8 beginnen.

Keine Quelle wird allein aufgrund von Titel oder Abstract als Claim-Support verwendet.
