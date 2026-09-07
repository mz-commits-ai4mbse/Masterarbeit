# AP – Literatur-Vollständigkeitscheck der gesamten Masterarbeit

**Stand:** 2026-09-07<br>
**Scope:** gesamte Masterarbeit, Kapitel 1–9. Nicht auf Kapitel 6 begrenzt.

## 1. Ziel

Vor der finalen Endredaktion wird ein vollständiger Literatur-Audit über die gesamte Thesis durchgeführt.

Der Audit soll sicherstellen, dass:

- allgemeine wissenschaftliche Aussagen ausreichend belegt sind,
- jede verwendete Quelle den konkreten Claim im Volltext tatsächlich stützt,
- normative Aussagen mit der passenden Primärquelle belegt werden,
- vorhandene Literatur möglichst vollständig und wissenschaftlich sinnvoll genutzt wird,
- nicht verwendete Literatur bewusst als nicht erforderlich oder nicht passend klassifiziert wird,
- keine Quelle nur zur Vergrößerung des Literaturverzeichnisses eingebaut wird,
- Projekt-, Repository- und Model-Evidence nicht fälschlich als wissenschaftliche Literatur behandelt werden,
- alle BibLaTeX-Keys technisch konsistent sind.

Leitprinzip:

> Ziel ist maximale sinnvolle Nutzung der vorhandenen Literatur, nicht Citation Stuffing.

## 2. Authority und Arbeitsbasis

Arbeitsgrundlage in dieser Reihenfolge:

1. aktuelle lokale / Overleaf-Fassung,
2. `Masterarbeit/literatur.bib`,
3. Volltexte unter `Literatur/`,
4. `Arbeitsstand/Literatur_Jagdliste_2026-09-04.md`,
5. bestehende Claim-/Literaturmatrizen,
6. GitHub nur als Vergleichsstand, falls lokal bereits weiter.

Zu prüfen sind mindestens alle tatsächlich eingebundenen Kapiteldateien 1–9 sowie ggf. eingebundene Inhalte aus `LSP.tex` / `LSR.tex`.

## 3. AP-A – Technische Literaturinventur

### A1 – BibTeX-Inventar

Aus `Masterarbeit/literatur.bib` erfassen:

- alle Citation Keys,
- Autor/Jahr/Titel,
- Quellentyp,
- DOI/URL/Standardkennung,
- vorhandener Volltext im Ordner `Literatur/`,
- Primär- oder Sekundärquelle.

### A2 – Citation-Inventar der Thesis

Alle Cite-Befehle aus der tatsächlich kompilierten Thesis erfassen.

Ergebnis:

- **used keys**
- **unused keys**
- **undefined cite keys**
- **Dubletten**
- **Volltexte ohne `.bib`-Eintrag**
- **`.bib`-Einträge ohne geprüften Volltext**

### A3 – künstliche Bibliographieeinträge ausschließen

Prüfen auf:

- `\nocite{*}`,
- temporäre `\nocite{...}`,
- Quellen, die nur für das Literaturverzeichnis zitiert werden,
- Preprint und finale Publikation parallel ohne inhaltlichen Grund.

**Output:** `Arbeitsstand/Literatur_Vollstaendigkeits_Audit.md`

## 4. AP-B – Claim-wise Reading

Für jede Quelle, die einen materiellen wissenschaftlichen Claim trägt:

1. relevante Volltextstelle identifizieren,
2. Thesis-Claim danebenstellen,
3. prüfen, ob die Quelle genau diesen Claim stützt,
4. zu starke Generalisierung reduzieren oder zweite Quelle suchen,
5. Primärquelle bevorzugen,
6. keine Claims nur aus Titel oder Abstract ableiten.

Bestehende bzw. zu aktualisierende Matrix:

`Arbeitsstand/Literatur_Claim_Matrix.md`

Empfohlene Felder:

| Kapitel | Thesis-Claim | Citation Key | Volltextstelle geprüft | Bewertung | Aktion |
|---|---|---|---|---|---|

Bewertung:

- `SUPPORTED`
- `SUPPORTED WITH NARROWER WORDING`
- `SECOND SOURCE NEEDED`
- `SOURCE DOES NOT SUPPORT CLAIM`
- `PROJECT EVIDENCE, NO LITERATURE REQUIRED`

## 5. AP-C – Kapitelweiser Vollständigkeitscheck

### Kapitel 1 – Einleitung

Prüfen:

- MBSE-/Brownfield-/Industriekontext,
- heterogene Legacy-Engineering-Information,
- maschinenlesbare und versionierbare Architekturartefakte,
- Architecture-as-Code-Motivation,
- Relevanz generativer KI für Engineering-Artefakte.

Wahrscheinliche Kandidaten:

- Hendriks / MBSE High-Tech Equipment,
- Bucaioni et al. 2025, falls beschafft und verifiziert,
- `Stirbu2022`,
- weitere aktuelle MBSE-/Legacy-Quellen.

### Kapitel 2 – Theoretischer Hintergrund und Stand der Technik

Hier den dichtesten Literaturcheck durchführen.

Prüffelder:

- MBSE und RFLP,
- SysML v2,
- Architecture as Code / Everything as Code,
- Legacy-/Brownfield-Transformation,
- LLM-basierte Modellgenerierung,
- Multi-Agent-/Persona-Ansätze,
- Human-in-the-Loop / Human Oversight,
- Grounding,
- Provenance,
- Ontologien / semantische Regeln,
- LLM→SysML-/SysML-v2-Arbeiten,
- Forschungslücke.

Prioritäre Kandidaten:

- `SysMLv2`
- `LiLockettLawson2020`
- `Stirbu2022`
- Bucaioni 2025
- `Stein2026`
- `Topcu2025`
- `MosqueiraRey2023`
- `Lazaros2026`
- `TriemDing2024`
- `Du2024`
- `MoreauMissier2013`
- `Simmhan2005`
- `ISO21838_2_2021`

Die Forschungslücke muss aus Literaturvergleich entstehen, nicht nur aus dem eigenen Projektbedarf.

### Kapitel 3 – Methodisches Vorgehen

Prüffelder:

- Design-Science-/Artefakt-Evaluation,
- strukturierte Literaturrecherche,
- PRISMA,
- Snowballing,
- RFLP,
- Architecture Description / Stakeholder Concerns,
- Requirements / Constraints,
- formative Verifikation,
- PoC als Untersuchungsartefakt.

Prioritäre Kandidaten:

- `Wieringa2014`
- `Wohlin.2012`
- `KitchenhamCharters2007`
- PRISMA / Page
- Wohlin Snowballing
- `LiLockettLawson2020`
- `ISO42010_2022`
- `ISO29148`

Peffers nur ergänzen, wenn nach Volltextprüfung ein echter zusätzlicher methodischer Nutzen besteht.

### Kapitel 4 – Konzeption der Informationsverarbeitungsarchitektur

Prüffelder:

- Architecture Description / Concerns / Viewpoints,
- RFLP-Bezug,
- Provenance,
- Human Authority,
- semantische Leitplanken,
- ontologische Regeln,
- Traceability und Information Boundaries.

Prioritäre Kandidaten:

- `ISO42010_2022`
- `LiLockettLawson2020`
- `MoreauMissier2013`
- ggf. `Simmhan2005`
- `ISO21838_2_2021`

`ISO42030` nur verwenden, wenn tatsächlich eine Aussage zur **Architecture Evaluation** gemacht wird.

### Kapitel 5 – Prototypische Realisierung

Nur allgemeine oder normative Aussagen nachbelegen.

Prioritäre Kandidaten:

- `SysMLv2`
- ggf. `MoreauMissier2013`
- relevante LLM-/HITL-Quellen nur für allgemeine Aussagen.

Konkrete IDs, Module, Manifeste, Fingerprints und Authority Contracts sind Projekt-/Repository-Evidence und brauchen keine künstliche Literaturreferenz.

### Kapitel 6 – Verifikation und Validierung

Bereits passend:

- `Wieringa2014`
- `Wohlin.2012`
- `ISO42010_2022`
- `SysMLv2`

Zusätzlich prüfen:

- `ISO42030` nur bei echtem Architecture-Evaluation-Claim,
- IEEE 1012 bzw. ISO/IEC/IEEE 29119 nur, wenn Volltext beschafft, Claim geprüft und `.bib` sauber ergänzt wurde.

Keine Norm nur deshalb einführen, weil Kapitel 6 „V&V“ heißt.

WP-12-Protokolle, Findings und Tests bleiben Projektevidenz.

### Kapitel 7 – Ergebnisse

Primär eigene Evidenz berichten.

Literatur nur, wenn eine Definition oder standardisierte Ergebniskategorie erklärt werden muss.

Vergleich mit dem Stand der Technik grundsätzlich nach Kapitel 8.

### Kapitel 8 – Diskussion, Reflexion und Beantwortung der Forschungsfragen

Hier Literatur aus Kapitel 2 aktiv wieder aufgreifen.

Besonders prüfen:

- `Stein2026`
- `Topcu2025`
- `MosqueiraRey2023`
- `Lazaros2026`
- `TriemDing2024`
- `Du2024`
- `MoreauMissier2013`
- `Simmhan2005`
- `Stirbu2022`
- Bucaioni 2025
- `Barke2023`
- weitere Coding-Assistant-Quellen aus dem Bestand.

Literatur dient zur Einordnung der eigenen Ergebnisse, nicht als Ersatz für eigene Evidenz.

### Kapitel 9 – Fazit und Ausblick

Nur gezielte Literaturverwendung.

Prüfen:

- keine neuen theoretischen Argumente ohne vorherige Einführung,
- Adaptive-Human-Feedback-/Learning-Ausblick ggf. mit `Lazaros2026` und geeigneten HITL-Quellen,
- keine neue Forschungsfront aufmachen, die in Kapitel 8 nicht vorbereitet wurde.

## 6. AP-D – Systematischer Check der bekannten Quellen

Mindestens einzeln klassifizieren:

### Methodik / Standards
- `Wieringa2014`
- `Wohlin.2012`
- `KitchenhamCharters2007`
- `ISO42010_2022`
- `ISO42030`
- `ISO29148`
- `ISO21838_2_2021`
- `SysMLv2`

### HITL / Multi-Agent / LLM
- `MosqueiraRey2023`
- `Lazaros2026`
- `TriemDing2024`
- `Du2024`
- `Topcu2025`
- `Stein2026`

### Provenance
- `MoreauMissier2013`
- `Simmhan2005`

### RFLP / Architecture as Code / Industrie
- `LiLockettLawson2020`
- `Stirbu2022`
- Bucaioni 2025, falls inzwischen aufgenommen
- Hendriks / MBSE High-Tech Equipment

### AI Coding / Reflexion
- `Barke2023`
- weitere einschlägige Quellen aus `literatur.bib`

Klassifikation:

- `USE – zentrale Quelle`
- `USE – ergänzend`
- `KEEP UNUSED – bewusst nicht passend`
- `REMOVE DUPLICATE / REPLACE`
- `FULL TEXT / CLAIM CHECK MISSING`

Die tatsächliche Liste wird automatisiert aus `literatur.bib` ergänzt.

## 7. AP-E – Umgang mit ungenutzter Literatur

Für jede ungenutzte Quelle dokumentieren:

1. sollte integriert werden,
2. redundant zu stärkerer Primärquelle,
3. nur Randbezug,
4. Volltext stützt vorgesehenen Claim nicht,
5. außerhalb Scope,
6. nur für Ausblick geeignet,
7. bibliografischer Altbestand / nicht mehr erforderlich.

Ziel:

> Jede hochrelevante vorhandene Quelle wird entweder sinnvoll verwendet oder bewusst mit Begründung ausgeschlossen.

## 8. AP-F – Normative Quellen gesondert prüfen

Für Standards und Spezifikationen:

- Edition/Jahr prüfen,
- offiziellen Titel prüfen,
- Primärquelle verwenden,
- nicht mehr behaupten als tatsächlich definiert,
- normative Definition und eigenes Architekturprinzip sprachlich trennen.

Besonders:

- ISO/IEC/IEEE 42010 → Architecture Description / Stakeholder / Concern / Viewpoint
- ISO 42030 → Architecture Evaluation
- ISO/IEC/IEEE 29148 → Requirements Engineering
- ISO/IEC 21838-2 → Ontologie-Claims
- OMG SysML v2 → Sprache / Syntax / Semantik / Konstrukte

## 9. AP-G – Bibliographie- und LaTeX-Closeout

Nach allen inhaltlichen Änderungen:

1. alle Cite-Keys auflösen,
2. Biber vollständig laufen lassen,
3. Undefined Citations = 0,
4. Duplicate BibTeX Keys = 0,
5. Pflichtfelder prüfen,
6. DOI/URL konsistent,
7. Autorennamen/Sonderzeichen prüfen,
8. keine ungewollten Preprint-Dubletten,
9. Literaturverzeichnis visuell prüfen,
10. vollständige Overleaf-Kompilation.

Zusätzlich:

```bash
git diff --check
```

## 10. Definition of Done

- [ ] Kapitel 1–9 claim-wise geprüft
- [ ] allgemeine wissenschaftliche Claims belegt oder enger formuliert
- [ ] jede verwendete Quelle im Volltext geprüft
- [ ] keine Claim-Ableitung nur aus Titel/Abstract
- [ ] Projekt-/Repo-Evidence und Literatur sauber getrennt
- [ ] Standards nur für passende normative Aussagen verwendet
- [ ] sämtliche `.bib`-Quellen als verwendet oder bewusst ungenutzt klassifiziert
- [ ] hochrelevante ungenutzte Quellen bewusst geprüft
- [ ] kein Citation Stuffing
- [ ] keine undefinierten Citation Keys
- [ ] Biber/LaTeX sauber
- [ ] `Arbeitsstand/Literatur_Claim_Matrix.md` aktuell
- [ ] `Arbeitsstand/Literatur_Vollstaendigkeits_Audit.md` final

## 11. Empfohlene Reihenfolge

1. Maschinelle Inventur von `.bib` und Thesis-Citations.
2. Unused/Undefined/Duplicate Audit.
3. Kapitel 1–6 Claim Check.
4. Kapitel 7–9 nach deren finaler Ausarbeitung.
5. Claim-wise Full-Text Reading.
6. gezielte Literaturintegration.
7. Kapitel 8 besonders intensiv auf Literaturvergleich prüfen.
8. Bibliographie-/Biber-/Compile-Closeout.

Der Audit ist ein eigener Thesis-Closeout-Workpackage und wird nicht nebenbei während einzelner Kapitelkorrekturen erledigt.
