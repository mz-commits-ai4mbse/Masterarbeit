# Literatur-Claim-Matrix

**Zweck:** Arbeitsmatrix zur Zuordnung der vorhandenen Literatur zu Themenblöcken, Kapiteln, Claims und Claim Boundaries der Masterarbeit.

**Arbeitsweise:** Diese Matrix ist zunächst eine thematische Zuordnung. Beim Schreiben wird sie schrittweise zu einer Evidence Matrix erweitert, indem für konkrete Claims die tatsächlich tragenden Seiten/Abschnitte im Volltext ergänzt werden.

---

## 1. Themen- und Kapitelzuordnung

| Themenblock | Kapitel | Was wir damit belegen wollen | Primärquellen | Ergänzende Quellen | Boundary / Vorsicht |
|---|---|---|---|---|---|
| Systems Engineering / MBSE | 1, 2.1 | Rolle von MBSE, modellbasierte Zusammenarbeit, Industrie-/Brownfield-Kontext | Hendriks2022 | bestehende MBSE-Literatur, SheardPickard2025 | Keine pauschale Behauptung, dass MBSE automatisch bessere Ergebnisse erzeugt |
| RFLP | 2.1, 3.3, 4 | Requirements–Functional–Logical–Physical als Strukturierungslogik | LiLockettLawson2020 | ggf. weitere vorhandene RFLP-/MBSE-Quellen | Nicht behaupten, dass RFLP die einzig sinnvolle Struktur ist |
| SysML v2 Grundlagen | 2.2 | Sprache, Kernelemente, textuelle Notation, normative Grundlage | SysMLv2 | Teodorov2025 | Normative Aussagen bevorzugt aus OMG |
| SysML v2 Forschungsstand | 2.2, 2.9 | aktuelle Forschung zu Executability, Validation und Navigation | Teodorov2025 | Stein2026, vorhandene SysML-v2-Arbeiten | Forschungsergebnisse nicht mit Normanforderungen vermischen |
| Architecture as Code / Everything as Code | 2.3, 8.6 | versionskontrollierte, maschinenlesbare Entwicklungsartefakte; AaC-Kontext | Stirbu2022 | später Bucaioni2025 | Stirbu ist EaC, nicht automatisch vollständige AaC-Definition |
| Legacy-/Brownfield-Transformation | 2.4, 4 | Probleme heterogener Bestandsdaten, Transformation und Modellmigration | vorhandene Transformationsliteratur wie Kapos, Zhao, Ahlbrecht, Chis | Hendriks2022 | Keine Universalität aus einzelnen Transformationsansätzen ableiten |
| LLM-basierte SE-Artefakterzeugung | 2.5, 2.9, 8.2–8.4 | Möglichkeiten und Grenzen von LLMs bei SE-Artefakten | Topcu2025, Stein2026 | Pan, Cibrian, weitere vorhandene LLM/SysML-Quellen | Keine Gleichsetzung von syntaktisch valide mit fachlich korrekt |
| LLM Failure Modes / Vertrauen | 2.5, 8.2, 8.4, 8.9 | Fehlerbilder, Overtrust, Bedarf an Review/Grounding | Topcu2025 | Barke2023, Sergeyuk2025 | Nicht behaupten, dass unsere Architektur alle Failure Modes verhindert |
| Multi-Agent / Debate | 2.5, 8.3 | mehrere Perspektiven, Debate als Mechanismus zur Qualitätsverbesserung | Du2024 | TriemDing2024 | Verbesserungen aus Benchmark-Settings nicht auf den PoC übertragen |
| Human-in-the-Loop | 2.6, 8.3 | Begriffe, Kontrollformen, Rolle von Domain Experts | MosqueiraRey2023, Lazaros2026 | TriemDing2024 | HITL nicht automatisch mit Human Authority gleichsetzen |
| Human Authority / Intervention | 2.6, 4, 8.3 | menschliche Entscheidungshoheit an definierten Stellen | MosqueiraRey2023 | Lazaros2026, TriemDing2024 | Architekturprinzip der Thesis ist eigene Ableitung, nicht direkter Literaturbefund |
| Ontologien / semantische Grundlage | 2.7, 4 | semantische Konsistenz, gemeinsame Begriffe, formale Konzeptualisierung | ISO21838_2_2021, OrellanaMandrick2019, Lu2022 | Kulvatunyou2022, Liu2025SysDO, Dong2026 | Ontologien verhindern keine Halluzinationen und garantieren kein Plug-and-play |
| Domain Ontologies / industrielle Ontologien | 2.7 | Rolle domänenspezifischer und industrieller Ontologien | Kulvatunyou2022, Liu2025SysDO | Dong2026 | Nicht jede Ontologie ist direkt für SysML v2 geeignet |
| Provenienz | 2.8, 4.6, 8.5 | Herkunft, Ableitung und Nachvollziehbarkeit von Informationen | MoreauMissier2013 | Simmhan2005 | PROV-DM liefert Begriffsmodell, nicht automatisch unser konkretes Provenance-Schema |
| Traceability / Requirements | 2, 3, 4 | Anforderungen, Constraints und Traceability | ISO29148 | SysMLv2 | Standard nur für Claims nutzen, die dort tatsächlich normativ gestützt sind |
| Architekturbeschreibung | 3, 4 | Architecture Description, Views/Viewpoints und Architekturartefakte | ISO42010_2022 | ISO42030 | 42010 = Beschreibung, 42030 = Bewertung, nicht vermischen |
| Architekturbewertung | 3, 6 | methodische Einordnung von Evaluation/Assessment | ISO42030 | Wieringa2014 | ISO 42030 nicht als Validierungsmethode für alles verwenden |
| Design Science / Forschungsdesign | 3.1 | gestaltungsorientierte Forschungslogik, Artefakt und Evaluation | Wieringa2014 | später Peffers | Nicht behaupten, die Thesis folge vollständig einer DSR-Methodik, bevor das Vorgehen sauber abgeglichen ist |
| Systematische Literaturrecherche | 3.2 | strukturierter Such- und Auswahlprozess | KitchenhamCharters2007, Page2021 | Wohlin2014, Petticrew | PRISMA beschreibt Reporting, nicht allein die gesamte SE-Literaturmethodik |
| Snowballing | 3.2 | ergänzende Literatursuche über Zitationsketten | Wohlin2014 | KitchenhamCharters2007 | Als Ergänzung zur Datenbanksuche darstellen |
| Evidenzbewertung | 3.2 / ggf. Appendix | Einordnung der Qualität vorhandener Evidenz | GRADE-Literatur | Wohlin/Petticrew | GRADE stammt aus anderem Anwendungskontext; nur vorsichtig adaptieren |
| PoC / Artefaktvalidierung | 3, 5, 6 | Prototyp als Mittel zur Untersuchung der Architektur | Wieringa2014 | ISO42030 | PoC ist Evidenz für Machbarkeit, nicht allgemeine externe Validität |
| Agentic architecture / Agent harness | 2.5, 5, 8 | technische Einordnung agentischer Orchestrierung | VanCliefMcDermott2026, Ning2026 | Sunkaraneni2026 | Preprints/Synthesen nur ergänzend und nicht für zentrale harte Claims |
| AI Coding Assistants | 8.9 | Reflexion der Nutzung generativer Assistenz in Entwicklung | Barke2023, Sergeyuk2025 | SheardPickard2025 | Nicht automatisch auf Systems Engineering oder Thesis-PoC übertragen |
| Rolle des Systems Engineers im AI-/Digital-Kontext | 1, 8.9, 9 | Veränderung von Rollen und Arbeitsweisen | SheardPickard2025 | Hendriks2022 | eher Kontext/Ausblick, kein Kernbeleg für Architekturentscheidungen |
| Forschungslücke | 2.10 | Kombination aus heterogenen Legacy-Daten, Bewertung, Synthese, Provenienz, Human Authority und SysML v2 | Stein2026 + gesamte Literaturbasis | Topcu2025, Transformations-, HITL- und Provenienzquellen | Nur formulieren: „In der durchgeführten Literaturrecherche wurde kein Ansatz identifiziert, der …“ |

---

## 2. Quellenrollen

- **A = Kernquelle:** trägt einen zentralen Claim und sollte im Volltext gezielt gelesen werden.
- **B = Stützquelle:** trianguliert oder erweitert einen Claim, ist aber nicht allein tragend.
- **C = Kontext/Ausblick:** hilfreich für Diskussion und Einordnung, aber nicht für tragende Aussagen.

---

## 3. Beispielhafte Claim-Matrix

| Claim | A | B | C |
|---|---|---|---|
| LLMs können plausible SE-Artefakte erzeugen, zeigen aber relevante Failure Modes | Topcu2025 | Stein2026 | Barke2023 |
| Human-in-the-Loop umfasst unterschiedliche Formen menschlicher Intervention | MosqueiraRey2023 | Lazaros2026 | TriemDing2024 |
| Provenance beschreibt nachvollziehbare Beziehungen zwischen Entitäten, Aktivitäten und Agenten | MoreauMissier2013 | Simmhan2005 | — |
| RFLP strukturiert Systementwicklung entlang Requirements, Functional, Logical, Physical | LiLockettLawson2020 | bestehende MBSE-Literatur | — |
| Textuelle, versionierbare Artefakte sind Kernidee von Everything as Code | Stirbu2022 | später Bucaioni2025 | — |

---

## 4. Noch offene Kernquellen

- **Bucaioni et al. 2025, „Architecture as Code“**
  - vorgesehen für 2.3 und 8.6
  - soll zentrale AaC-Definition und Motivation absichern
- **Peffers et al., „A Design Science Research Methodology for Information Systems Research“**
  - vorgesehen für Kapitel 3
  - ergänzende DSR-Referenz neben Wieringa

---

## 5. Geplante Erweiterung beim Schreiben

Für jeden tatsächlich verwendeten Claim sollen später ergänzt werden:

- exakte BibTeX-Quelle
- Seite(n) / Abschnitt(e)
- konkrete unterstützte Aussage
- Evidenztyp
- Claim Boundary / Einschränkung
- Kapitel / Unterkapitel, in dem der Claim verwendet wird

Damit entwickelt sich dieses Dokument schrittweise von einer Literaturzuordnung zu einer belastbaren Evidence Matrix.
