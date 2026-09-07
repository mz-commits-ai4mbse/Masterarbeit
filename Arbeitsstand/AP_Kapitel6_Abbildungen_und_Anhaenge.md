# AP – Kapitel 6: Abbildungen und Aufbereitung der V&V-Anhänge

**Stand:** 2026-09-07<br>
**Scope:** Kapitel 6 „Verifikation und Validierung“ einschließlich zugehöriger Anhänge und Vorbereitung der Stakeholder-Demonstration.

## 1. Ziel

Kapitel 6 wird nach inhaltlichem Abschluss visuell und evidenzseitig konsolidiert.

Das Arbeitspaket umfasst:

1. Auswahl und Erstellung der wirklich notwendigen Abbildungen,
2. Vermeidung redundanter Diagramme zu Kapitel 4 und 5,
3. vollständige und lesbare Aufbereitung der WP-12-Testdaten,
4. Aufbereitung von WP-12-Testprotokoll, Expected Engineering Contract und formativem Report,
5. Vorbereitung und spätere Dokumentation der externen Stakeholder-Demonstration,
6. saubere Referenzierung der Anhänge,
7. finalen LaTeX-/Layout-/Compile-Check.

Die Anhänge sind Evidenzträger. Der Haupttext bleibt argumentativ und enthält nur die für die V&V-Methodik relevanten Aspekte.

## 2. Aktuelle Kapitel-6-Struktur

```text
6.1 Zielsetzung und Evidenzstrategie
6.2 Automatisierte und formative Verifikation
6.3 Kontrollierte WP-12-End-to-End-Verifikation
6.4 SysML-v2-Validierung und Human Release
6.5 Externe Stakeholder-Demonstration
6.6 Zuordnung der V&V-Evidenz zu den Forschungsfragen
```

Kein separates Zwischenfazit.

# TEIL A – ABBILDUNGEN

## 3. Abbildungsprinzipien

Kapitel 6 benötigt nur Abbildungen, die eine V&V-Logik sichtbar machen, die aus Text oder den Architekturabbildungen der Kapitel 4/5 nicht bereits eindeutig hervorgeht.

Nicht erneut zeichnen:

- vollständige Systemarchitektur,
- komplette Kapitel-5-Informationsverarbeitungskette,
- Python-Modulstruktur,
- CATIA-RFL-Dekomposition.

Jede Abbildung muss:

- vor ihrem Auftreten im Text referenziert werden,
- eine klare methodische Aussage tragen,
- eine nummerierte Caption unterhalb besitzen,
- Thesis-Terminologie verwenden,
- keine zufälligen Projekt-IDs oder historische Implementierungsphasen enthalten,
- Kapitel 6 als V&V-Methodik darstellen, nicht als Entwicklungschronik.

## 4. Abbildung 6-1 – Komplementäre V&V-Evidenzebenen

### Arbeitstitel

**Komplementäre Evidenzebenen der Verifikation und Validierung**

### Ziel

Zeigen, dass die Thesis nicht auf einen einzelnen Testtyp vertraut.

### Inhalt

```text
Unit / Contract Verification
        ↓
Integration Verification
        ↓
Focused Regression
        ↓
Complete Repository Regression
        ↓
Controlled WP-12 End-to-End Verification
        ↓
Generated Artifact Validation
  ├─ internal deterministic checks
  └─ external SYSIDE validation
        ↓
Final Human Model Review
        ↓
External Stakeholder Demonstration
```

Wichtig:

- nicht als Qualitäts-Rangliste darstellen,
- besser als komplementäre Evidenzschichten bzw. zunehmende Prüfbreite,
- technische und fachliche Evidenz unterscheidbar,
- Human Authority nicht als automatischer Testschritt darstellen.

### Platzierung

In 6.1, nach Einführung der Evidenzstrategie bzw. nach 6.1.2.

### Status

- [ ] Konzept zeichnen
- [ ] Redundanz mit Kapitel 3 prüfen
- [ ] Caption formulieren
- [ ] Textreferenz ergänzen
- [ ] PDF/Vector-Export
- [ ] Overleaf-Layout prüfen

## 5. Abbildung 6-2 – WP-12-Testdesign

### Arbeitstitel

**Kontrolliertes WP-12-Multi-Document-End-to-End-Testdesign**

### Ziel

Die Versuchsanordnung aus 6.3 auf einen Blick nachvollziehbar machen.

### Testdaten-Seite

Vier getrennte Sources:

```text
Product Overview
User Workflow Notes
Draft System Requirements
Technical Architecture Notes
```

Eigenschaften knapp kennzeichnen:

- unterschiedliche Engineering-Perspektiven,
- unterschiedliche Formalisierungsgrade,
- Overlap,
- complementary information,
- semantic tension,
- intentionally missing information.

### Kontrollierter Testpfad

Nicht die komplette Implementierungsarchitektur aus Kapitel 5 reproduzieren.

Vereinfachte Prüfkette:

```text
separate Source Registration
        ↓
source-local Processing
        ↓
Human Engineering Review
        ↓
authorized source-local information
        ↓
common Project / Project Fit
        ↓
Model Derivation
        ↓
Generated SysML v2
        ↓
Validation + Human Release
```

### Acceptance Oracle

`WP-12 Expected Engineering Contract`

Darstellen:

```text
Engineering Meaning
+ Provenance
+ Authority Boundaries
+ Missing-Information Discipline
```

Explizit nicht als Zielkriterium darstellen:

```text
exact expected SysML structure
exact element count
exact wording
```

### Kernaussage

> WP-12 prüft nicht, ob ein vorher festgelegtes Zielmodell exakt reproduziert wird, sondern ob erwartbare Engineering-Bedeutung unter Erhalt von Provenance und Human Authority durch den Workflow getragen wird.

### Platzierung

6.3 nach 6.3.1 oder vor 6.3.2.

### Status

- [ ] Testdatenblöcke finalisieren
- [ ] Expected Engineering Contract als Acceptance Oracle integrieren
- [ ] Pfad mit finaler Kapitel-5-Terminologie abgleichen
- [ ] keine Projekt-ID
- [ ] kein BLK-002
- [ ] keine Retest-Chronologie
- [ ] Caption/Textreferenz
- [ ] Vector/PDF-Export
- [ ] Compile

## 6. Abbildung 6-3 – Generated Artifact Validation und Human Release

### Arbeitstitel

**Technische Validierung und Human Release des generierten SysML-v2-Artefakts**

### Ziel

Die zentrale Trennung zwischen technischer Validierung und fachlicher Release Authority sichtbar machen.

### Inhalt

```text
Generated SysML v2 Artifact
        │
        ├───────────────┐
        ▼               ▼
Internal deterministic  External SYSIDE
validation              validation
        │               │
        └───────┬───────┘
                ▼
       Validation Result
     valid / invalid / incomplete
                │
                ├── invalid/incomplete → publication blocked
                │
                ▼ valid + gate passed
       Final Human Model Review
                │
                ▼
     explicit Human Release Decision
                │
                ▼
       immutable Publication
```

### Muss sichtbar sein

- externe SYSIDE-Validierung ersetzt interne Turing-Checks nicht,
- technische Validität ersetzt Human Release nicht,
- `invalid` und `incomplete` bleiben fail-closed,
- Publication benötigt den exakten validierten und human-freigegebenen Artefaktzustand.

### Platzierung

In 6.4, vorzugsweise am Ende von 6.4.1 oder zwischen 6.4.1 und 6.4.2.

### Status

- [ ] Terminologie final prüfen
- [ ] Claim Boundary berücksichtigen
- [ ] keine konkreten Laufwerte
- [ ] Vector/PDF-Export
- [ ] Compile

## 7. Stakeholder-Demonstration: Tabelle statt vierter Abbildung

Für 6.5 voraussichtlich keine weitere Prozessabbildung.

Stattdessen kompakte Tabelle:

**Vorab definierte Beobachtungskriterien der Stakeholder-Demonstration**

mit K1–K7:

- K1 Workflow-Nachvollziehbarkeit
- K2 KI-Vorschlag vs. Human Authority
- K3 Provenance / Traceability
- K4 Multi-Source-Verarbeitung
- K5 Human-Review-Grenzen
- K6 fachliche Plausibilität des SysML-v2-Ergebnisses
- K7 Risiken / fehlende Informationen

Status:

- [ ] entscheiden, ob Enumeration im Text bleibt oder als Tabelle komprimiert wird
- [ ] keine Ergebnisse vor der Demo eintragen
- [ ] Kapitel 7 später mit denselben IDs K1–K7 auswerten

# TEIL B – ANHANG 03: TESTDATEN

## 8. `Anhang/03_Testdaten.tex`

### Zweck

Die tatsächlich verwendete kontrollierte Eingangsdatenbasis vollständig nachvollziehbar machen.

### Vorgesehene Struktur

```text
Testdaten der Verifikation und Validierung

1. Einordnung und Zweck
2. Übersicht des WP-12-Testdatensatzes
3. Product Overview
4. User Workflow Notes
5. Draft System Requirements
6. Technical Architecture Notes
7. Claim Boundary der Testdatenbasis
```

### Übersichtstabelle

Vor den vollständigen Dokumenten:

| Source | Dokumentcharakter | Engineering-Perspektive | gezielte Testeigenschaft |
|---|---|---|---|
| Product Overview | informell / unvollständig | Produkt | offene Fragen, Overlap |
| User Workflow | Workflow Notes | Nutzung / Verhalten | Normalfall vs. Recovery |
| System Requirements | Draft Requirements | Requirements | explizitere Constraints |
| Technical Architecture Notes | informelle Architektur | technische Verantwortung | unentschiedene Deployment-Zuordnung |

### Vollständige Originaldaten

Die vier `.md`-Dateien unter

`Anhang/Testdaten_WP12/`

vollständig aufnehmen.

Bevorzugt:

- Originaldatei bleibt SSOT,
- LaTeX bindet exakt diese Datei ein,
- keine zweite manuell gepflegte Textkopie.

Für die kurzen Testdaten ist `\lstinputlisting` geeignet.

Prüfen:

- Zeilenumbruch,
- UTF-8 / Sonderzeichen,
- Markdown-Überschriften,
- Monospace-Größe,
- Seitenumbrüche,
- Listing-Captions,
- Listing-Labels,
- Listing-Zähler.

Wegen bestehender `listings`-Konfiguration ggf.:

```latex
\AtBeginDocument{\counterwithin{lstlisting}{section}}
```

statt `\counterwithin{lstlisting}{section}` vor Erzeugung des Counters.

### Claim Boundary

Explizit festhalten:

- synthetische Daten,
- inhaltlich / semantisch heterogen,
- unterschiedliche Engineering-Perspektiven,
- technisch jedoch alle Markdown-Dateien.

Daraus folgt kein Nachweis beliebiger Dateiformat-Heterogenität.

### Status

- [ ] Übersichtstabelle erstellen
- [ ] vier Originaldateien vollständig einbinden
- [ ] lesbare Listing-Formatierung
- [ ] Labels prüfen
- [ ] Referenz aus 6.3 prüfen
- [ ] Claim Boundary ergänzen
- [ ] Compile / Seitenumbrüche prüfen

# TEIL C – ANHANG 04: WP-12 TESTDESIGN UND REPORT

## 9. `Anhang/04_TestReport.tex`

### Zweck

Ausführliche WP-12-Evidenz aus dem Haupttext auslagern, ohne sie zu verlieren.

Im Thesis-Repo vorgesehen:

- `wp12_expected_engineering_contract.md`
- `wp12_multi_document_dry_run_test_protocol.md`
- `wp12_formative_self_test_report.md`

### Empfohlene Struktur

```text
WP-12 Testdesign und Evaluationsdokumentation

1. Einordnung
2. Expected Engineering Contract
3. Multi-Document End-to-End Dry-Run Test Protocol
4. Formative Self-Test Report
5. Verweis auf Findings Register
```

### Reihenfolge

1. **Expected Engineering Contract**<br>
   definiert die semantische Acceptance Oracle.

2. **Test Protocol**<br>
   definiert Ablauf, Testfälle, Preconditions, erwartete Zustände und Abbruchlogik.

3. **Formative Self-Test Report**<br>
   dokumentiert die formative Auswertung.

4. **Findings Register**<br>
   bleibt eigener Anhang und wird nicht doppelt kopiert.

### Darstellungsentscheidung

Die langen Markdown-Dokumente nicht automatisch als rohe Listings behandeln.

Bevorzugt:

```text
Original .md bleibt SSOT
→ automatisierte Markdown→LaTeX-Konvertierung
→ manueller Inhalts-/Diff-Audit
→ LaTeX-Fassung nur als Darstellungsartefakt
```

Dabei:

- Überschriften, Tabellen, Checklisten und Codeblöcke sauber typografieren,
- Wortlaut und Reihenfolge erhalten,
- keine historische „Bereinigung“ des Protokolls,
- kennzeichnen, dass lediglich typografisch übertragen wurde.

Fallback:

- exakte `VerbatimInput`-/`lstinputlisting`-Einbindung, falls Konvertierung fehleranfällig wird.

### Historische Integrität

Das Testprotokoll enthält Zwischenstände, Blocker und Retests.

Diese bleiben im Anhang erhalten.

Leitlinie:

> Der Anhang dokumentiert die tatsächliche Testhistorie. Kapitel 6 beschreibt davon getrennt das V&V-Design ohne Entwicklungschronologie.

Historische Projekt-IDs oder Findings nicht aus dem Anhang entfernen, nur weil sie im Haupttext nicht benötigt werden.

### Status

- [ ] drei Markdown-Dateien im aktuellen lokalen Thesis-Repo lokalisieren
- [ ] endgültige Pfade dokumentieren
- [ ] Expected Engineering Contract zuerst
- [ ] Test Protocol vollständig
- [ ] Formative Report vollständig
- [ ] Verweis auf `01_Findings_Register.tex`
- [ ] typografische Konvertierung
- [ ] Inhaltsgleichheit gegen Original prüfen
- [ ] Labels/Querverweise
- [ ] mehrseitige Tabellen/Listings prüfen
- [ ] Compile

# TEIL D – STAKEHOLDER-DEMONSTRATION

## 10. Vor der Demo: Protokoll einfrieren

Vor Durchführung der externen Stakeholder-Demonstration ein separates Arbeitsprotokoll erstellen und einfrieren.

Mindestens dokumentieren:

### Baseline

- Datum
- System-/Git-Baseline
- CATIA-/Architekturbaseline
- verwendete Sources
- vorbereiteter Project-Zustand
- LLM Provider / Modell, sofern relevant
- Teilnehmer / Rollen in angemessen anonymisierter Form

### Demonstrationsablauf

Der geplante Ablauf aus 6.5.

### Beobachtungskriterien

Exakt K1–K7 aus Kapitel 6.

### Offene Fragen

Mindestens:

- Welche Stelle des Workflows wirkt fachlich am wenigsten belastbar?
- Wo ist eine menschliche Entscheidung zwingend erforderlich?
- Welche zusätzliche Information wäre für reale Engineering-Freigabe nötig?
- Ist die Herkunft einer erzeugten Modellaussage ausreichend nachvollziehbar?
- Welche weiteren Risiken oder Schwachstellen werden gesehen?

### Dokumentationsregel

- positive und kritische Rückmeldungen gleich behandeln,
- Fragen nicht nachträglich auf bessere Ergebnisse trimmen,
- spontane Zusatzfragen als solche kennzeichnen,
- technische Abweichungen dokumentieren.

### Nach der Demo

Nur ergänzen:

- tatsächliche Durchführung,
- Abweichungen,
- Beobachtungen,
- Feedback,
- Ergebniszuordnung K1–K7.

Die Ergebnisdarstellung gehört in Kapitel 7.

### Anhangsentscheidung

Nach der Demo:

**Variante A:** bei überschaubarem Umfang als weitere Untersektion in `04_TestReport.tex`.

**Variante B:** bei größerem Umfang eigener Anhang, z. B. `05_Stakeholder_Demo.tex`.

Entscheidung erst nach Kenntnis des tatsächlichen Umfangs.

# TEIL E – QUERVERWEISE UND INTEGRATION

## 11. Referenzen aus Kapitel 6 prüfen

Mindestens:

- 6.3 → `03_Testdaten.tex`
- 6.3 → `04_TestReport.tex`
- 6.2.3 → WP-12-Testprotokoll / Finding-Retest-Logik
- 6.5 → ggf. Demonstrationsprotokoll nach Durchführung

Alle `\label{}` / `\ref{}` auflösen.

Keine Formulierung „siehe Anhang“ ohne konkrete Referenz.

## 12. `main.tex` / Appendix-Reihenfolge

Lokale Fassung prüfen.

Sinngemäß:

```latex
\appendix

\input{Anhang/01_Findings_Register}
% ggf. bestehender 02-Anhang
\input{Anhang/03_Testdaten}
\input{Anhang/04_TestReport}
% ggf. später 05_Stakeholder_Demo
```

Die tatsächliche lokale Struktur ist Authority. Nummern nicht neu vergeben, falls bereits ein anderer `02_...`-Anhang existiert.

# TEIL F – FINALER KAPITEL-6-CLOSEOUT

## 13. Text-/Abbildungs-Audit

- [ ] jede Abbildung wird vorher im Text erwähnt
- [ ] jede Caption erklärt die Aussage
- [ ] keine Wiederholung aus Kapitel 4/5 ohne Mehrwert
- [ ] Terminologie stimmt mit finaler Architektur
- [ ] WP-12 wird als ein kontrollierter Multi-Source-E2E-Test beschrieben
- [ ] keine methodische Bedeutung aus zufälligen Project-IDs
- [ ] BLK-002 bleibt aus dem V&V-Design in 6.3
- [ ] konkrete Ergebnisse bleiben Kapitel 7
- [ ] Interpretation bleibt Kapitel 8

## 14. Layout-/Compile-Audit

Prüfen:

- Overfull/Underfull boxes,
- lange Listings,
- Seitenumbrüche in Tabellen,
- Hurenkinder / Schusterjungen,
- Figure Float Position,
- Caption-Abstände,
- Anhangsseiten,
- Inhaltsverzeichnis,
- Abbildungsverzeichnis,
- Listing-Verzeichnis, falls aktiviert,
- Querverweise,
- Bibliographie.

Danach:

```bash
git status --short
git diff --check
```

Overleaf komplett kompilieren.

## 15. Definition of Done

- [ ] Abbildung 6-1 V&V-Evidenzstrategie fertig
- [ ] Abbildung 6-2 WP-12-Testdesign fertig
- [ ] Abbildung 6-3 Validation/Human-Release-Gate fertig
- [ ] Entscheidung Tabelle statt Grafik für K1–K7 umgesetzt
- [ ] `03_Testdaten.tex` vollständig und lesbar
- [ ] alle vier WP-12-Testdaten exakt aus Originaldateien eingebunden
- [ ] `04_TestReport.tex` enthält Expected Engineering Contract, Test Protocol und Formative Report
- [ ] historische WP-12-Evidenz in Anhängen unverfälscht
- [ ] Stakeholder-Demo-Protokoll vor Durchführung eingefroren
- [ ] Stakeholder-Demo nach Durchführung dokumentiert
- [ ] alle Kapitel-6-Referenzen funktionieren
- [ ] Appendix-Reihenfolge in `main.tex` korrekt
- [ ] Kapitel 6 nimmt keine Ergebnisse/Diskussion vorweg
- [ ] vollständiger Overleaf-Compile erfolgreich
- [ ] `git diff --check` PASS

## 16. Empfohlene Reihenfolge

1. Kapitel 6 inhaltlich final einfrieren.
2. Stakeholder-Demo-Protokoll **vor der Demo** erstellen.
3. Abbildung 6-1 erstellen.
4. Abbildung 6-2 erstellen.
5. Abbildung 6-3 erstellen.
6. `03_Testdaten.tex` aufbereiten.
7. `04_TestReport.tex` aufbereiten.
8. Stakeholder-Demo durchführen.
9. Demo-Protokoll ergänzen.
10. Kapitel 7 mit Ergebnissen füllen.
11. Querverweise / Appendix-Integration prüfen.
12. finaler Kapitel-6-Layout-/Compile-Closeout.
