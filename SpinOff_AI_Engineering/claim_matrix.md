# Claim Matrix — AI-Supported Engineering Spin-Off Paper

Source baseline:
Masterarbeit final submission state, commit 05d8cce.

Purpose:
Maintain traceability between generalized paper claims, evidence from the thesis,
existing literature, and the permitted level of generalization.

---

## C1 — Start with the engineering task

**Claim**

AI-supported engineering should start from a clearly defined engineering task rather
than from the technology itself.

**Why it matters**

The task determines required information, outputs, acceptance criteria, authority
boundaries, and meaningful automation scope.

**Thesis basis**

- Chapter 3: research design and task framing
- Chapter 4: bounded transformation architecture
- Chapter 9: transfer to further engineering tasks

**Literature already available**

- Henderson2024
- Call2022
- Campo2023
- Nanfuka2023

**Generalization**

Use as a design premise, not as a universally empirically proven adoption rule.

**Paper location**

I, II-A, III-P1

---

## C2 — Define the information basis

**Claim**

The information basis of an AI-supported engineering workflow must be explicitly
defined.

**Why it matters**

Engineering output cannot be evaluated if it is unclear which information may justify
the result.

**Thesis basis**

- Chapter 4: Informationsgrundlage und Kontext

**Literature**

- MoreauMissier2013
- Abonyi.2024
- Chis.2025

**Generalization**

Strong for document- and information-centered engineering workflows.

**Paper location**

II-B, III-P2

---

## C3 — Distinguish information roles

**Claim**

Engineering sources, contextual information, and processing context should not
implicitly carry the same authority.

**Why it matters**

Prompts, ontologies, general domain context, and source engineering information may all
influence an AI output without having the same evidential status.

**Thesis basis**

Explicit source-role architecture in Chapter 4.

**Literature**

- MoreauMissier2013
- Abonyi.2024
- Chis.2025

**Generalization**

Do not claim that exactly three source categories are universally necessary.
Generalize the need for explicit information roles and authority.

**Paper location**

II-B, III-P2

---

## C4 — Heterogeneity is semantic

**Claim**

Heterogeneity in legacy engineering information is not merely a technical format
problem; it is also a semantic problem.

**Thesis basis**

- Chapter 2: Brownfield Engineering
- Chapter 2: Data, Process and Knowledge levels

**Literature**

- Maheshwari2018
- Henderson2024
- Bressan.2000
- Legat2014
- Tian.2025
- Chis.2025

**Generalization**

Strongly supported beyond SysML v2.

**Paper location**

I, II-C

---

## C5 — Use probabilistic AI for interpretation

**Claim**

Probabilistic AI is particularly useful where engineering information requires
interpretation rather than purely deterministic transformation.

**Thesis basis**

- Chapter 2: distinction between formal model transformation and semantic
  interpretation of legacy information
- Chapter 4: interpretation and controlled transformation

**Literature**

- Pan.2025
- Cibrian.2025
- Stein2026
- Kapos.2014
- Li.2022
- Zhou.2024

**Generalization**

Phrase as a design principle. Do not claim universal optimality.

**Paper location**

II-D, III-P3

---

## C6 — Plausibility is not correctness

**Claim**

A plausible or formally valid AI-generated artifact is not necessarily an
engineering-correct artifact.

**Thesis basis**

- Chapter 2
- Chapter 4 validation concept
- Chapter 8 discussion

**Literature**

- Topcu2025
- Cibrian.2025
- Stein2026
- Souza.2026

**Generalization**

Strongly supported.

**Paper location**

I, II-H, III-P10

---

## C7 — Consensus is evidence, not proof

**Claim**

Agreement between multiple AI-generated interpretations can provide useful decision
evidence but does not establish engineering correctness.

**Thesis basis**

- Chapter 4: consensus, variance, shared ambiguity, stability

**Literature**

- Du2024
- TriemDing2024
- Sunkaraneni2026

**Generalization**

Multi-perspective processing is optional; the key principle is the treatment of
agreement as evidence rather than authority.

**Paper location**

II-D, III-P4

---

## C8 — Make uncertainty explicit

**Claim**

Uncertainty should become an explicit process state rather than being silently resolved
by the AI system.

**Thesis basis**

- Chapter 4: variance and shared ambiguity
- Chapter 4: fail-closed behavior

**Literature**

- Topcu2025
- Du2024
- TriemDing2024
- HITL literature

**Generalization**

The exact uncertainty representation is use-case specific.

**Paper location**

II-D, III-P4, III-P8

---

## C9 — AI does not acquire engineering authority

**Claim**

Probabilistic AI output should support engineering decisions rather than implicitly
acquire engineering authority.

**Thesis basis**

- Chapter 4: Human Authority
- Chapter 8
- Chapter 9
- final Abstract

**Literature**

- MosqueiraRey2023
- Lazaros2026
- Topcu2025
- Stein2026

**Generalization**

Do not claim that every AI output requires manual approval.
Authority should be assigned at consequential engineering decision boundaries.

**Paper location**

II-E, III-P5

---

## C10 — Human involvement needs explicit authority

**Claim**

Human involvement is most useful when expressed as explicit decision authority at
relevant process boundaries rather than as an unspecified human-in-the-loop concept.

**Thesis basis**

- Human Authority
- Human Engineering Authority
- Model Authority
- final human release

**Literature**

- MosqueiraRey2023
- Lazaros2026

**Generalization**

The specific authority roles are use-case dependent.

**Paper location**

II-E, III-P5

---

## C11 — Separate meaning and representation

**Claim**

Engineering meaning, structural representation, and target-specific formulation may
require separate decisions.

**Thesis basis**

- Chapter 4: Von fachlicher Bedeutung zur Modellrepräsentation
- Chapter 8 discussion

**Literature**

- OrellanaMandrick2019
- Lu2022
- Liu2025SysDO
- Dong2026

**Generalization**

Strong for model- and artifact-oriented tasks; may be unnecessary for simple tasks.

**Paper location**

II-F, III-P6

---

## C12 — Constrain downstream processing

**Claim**

Once engineering meaning and relevant decisions are sufficiently determined,
downstream processing should be rule-bound wherever appropriate.

**Thesis basis**

- Chapter 4: Regelgebundene Transformation
- Chapter 9 conclusion

**Literature**

- Pan.2025
- Cibrian.2025
- Kapos.2014
- Li.2022
- Zhou.2024

**Generalization**

Do not state that deterministic processing is always superior.
The claim concerns already-resolved decision spaces.

**Paper location**

II-F, III-P7

---

## C13 — Fail closed

**Claim**

When required evidence, authority, or transformation rules are missing, the workflow
should expose the unresolved state rather than infer a convenient continuation.

**Thesis basis**

- Chapter 4: fail-closed architecture
- Chapter 8

**Literature**

Primarily thesis-derived design principle, supported indirectly by:
- Topcu2025
- Cibrian.2025
- Stein2026

**Generalization**

Present explicitly as a design principle derived from the investigated architecture,
not as established universal consensus.

**Paper location**

II-F, III-P8

---

## C14 — Provenance includes processing conditions

**Claim**

Provenance should cover both source information and relevant processing conditions
under which AI-derived results were produced.

**Thesis basis**

- Chapter 4: source and processing provenance
- Chapter 8

**Literature**

- MoreauMissier2013
- Simmhan2005

**Generalization**

Required granularity depends on risk and use case.

**Paper location**

II-G, III-P9

---

## C15 — Trace the combined AI-human process

**Claim**

Traceability should connect source information, AI-generated interpretations, human
decisions, and resulting engineering states.

**Thesis basis**

- Chapter 4
- Chapter 8
- Chapter 7 reference run

**Literature**

- MoreauMissier2013
- Simmhan2005
- ISO29148

**Generalization**

Strong for persistent engineering artifacts and lifecycle-relevant information.

**Paper location**

II-G, III-P9

---

## C16 — Preserve provenance across multiple sources

**Claim**

Multi-source engineering workflows require integration of related information without
losing source-local provenance.

**Thesis basis**

- Chapter 4 multi-source architecture
- Chapter 8 Project Fit discussion
- multi-source verification

**Literature**

- Maheshwari2018
- Legat2014
- MoreauMissier2013

**Generalization**

Project Fit is implementation-specific. The controlled integration boundary is the
generalizable principle.

**Paper location**

II-G

---

## C17 — Separate verification from generation

**Claim**

Verification and validation should be independent of the generation step.

**Thesis basis**

- Chapter 4 validation
- Chapter 6
- Chapter 7
- final Abstract and Conclusion

**Literature**

- Cibrian.2025
- Stein2026
- Topcu2025

**Generalization**

Verification mechanisms are target- and task-specific.

**Paper location**

II-H, III-P10

---

## C18 — Productive use requires workflow integration

**Claim**

Productive AI-supported engineering requires integration into existing engineering
roles, processes, and quality mechanisms.

**Thesis basis**

- Chapter 9 Outlook

**Literature**

- Henderson2024
- Call2022
- Campo2023
- Nanfuka2023

**Generalization**

This is partly an adoption implication and future-work topic, not an empirically
validated outcome of the prototype.

**Paper location**

II-H, IV, V

---

## C19 — Human review must scale

**Claim**

Scaling AI-supported engineering also creates a scaling problem for human review and
authorization.

**Thesis basis**

- Chapter 9: scaling human review and authorization

**Literature**

- MosqueiraRey2023
- Lazaros2026
- adoption literature

**Generalization**

Present as an unresolved challenge, not as a solved mechanism.

**Paper location**

II-H, IV, V

---

## C20 — Human decisions can become future learning evidence

**Claim**

Persisted human engineering decisions can provide a future learning basis if their
context, provenance, and versioning remain controlled.

**Thesis basis**

- Chapter 9: Lernen aus menschlichen Engineering-Entscheidungen

**Literature**

- Christiano2017
- Ouyang2022
- Settles2009

**Generalization**

Future direction only.

**Paper location**

V or omit if page budget becomes tight.

---

# Evidence Categories

## Category A — Strong literature + thesis support

C4
C6
C9
C14
C15
C17

These may be stated relatively directly.

## Category B — Generalized design principles derived from the thesis

C2
C3
C5
C7
C8
C10
C11
C12
C13
C16

These should be presented as design principles or implications rather than universal
laws.

## Category C — Adoption and future-work implications

C1
C18
C19
C20

These require cautious wording and should not be presented as empirically proven by
the prototype.

---

# Citation Rule for the Paper

1. Prefer the original literature already cited in the thesis.
2. Do not cite the Master's thesis as a substitute for available original literature.
3. Thesis-derived architecture findings may be presented as findings of the underlying
   investigation.
4. Clearly distinguish:
   - literature-supported statements,
   - findings from the investigated architecture,
   - generalized design implications,
   - future challenges.
5. Add new literature only if a paper claim cannot be supported by either:
   - existing thesis literature, or
   - evidence from the underlying investigation.
