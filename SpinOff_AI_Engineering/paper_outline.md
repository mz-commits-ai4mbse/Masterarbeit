# Making AI Work in Engineering
## Key Questions, Challenges, and Design Principles for Controlled AI-Supported Engineering

Status: Working outline
Source baseline: Masterarbeit, final submission state, commit 05d8cce

---

## Purpose

This paper presents a structured perspective on the questions, challenges, and
design principles that need to be addressed when introducing generative AI into
engineering workflows.

The paper is intended as a scientifically grounded, practice-oriented contribution.
It does not prescribe one specific technical implementation. Instead, it identifies
the conditions under which probabilistic AI can become a controlled component of
engineering work.

The underlying findings originate from the development and investigation of an
AI-supported information-processing architecture for the transformation of
heterogeneous legacy engineering information into SysML v2. The paper generalizes
those findings only where supported by the thesis results and the literature used
therein.

---

# I. Introduction

## Purpose

Establish the opportunity and the actual problem.

Generative AI makes engineering activities accessible that previously required
substantial manual interpretation of heterogeneous or weakly structured information.

However, introducing an LLM into an engineering workflow is not equivalent to
establishing a reliable engineering process.

Engineering outputs may affect requirements, architectures, models, verification
activities, documentation, or later technical decisions. Their origin, interpretation,
authority, and quality therefore need to remain controlled.

## Core message

The main challenge is not whether AI can generate engineering-related content.
The challenge is how probabilistic AI output can be embedded into a process in which
engineering meaning, decision authority, traceability, and verification remain
controlled.

## Main claims

- C1: Start from a defined engineering task.
- C4: Engineering heterogeneity is also a semantic problem.
- C6: Plausible or formally valid AI output is not necessarily engineering-correct.

## Likely references

- Henderson2024
- Campo2023
- Maheshwari2018
- Topcu2025
- Stein2026

---

# II. What Makes AI-Supported Engineering Difficult?

This section introduces the problem space before deriving design principles.

## A. Engineering Task and Information Basis

### Core questions

- What engineering activity should AI support?
- What result is expected?
- Which information may justify an engineering statement?
- Which information provides contextual support only?
- Which processing conditions influence the result?
- What constitutes an acceptable engineering outcome?

### Main claims

- C1
- C2
- C3

### Core message

AI adoption should begin with the engineering task and its information basis rather
than with the availability of a particular model or AI technology.

Engineering sources, contextual information, and processing context may all
influence an AI-generated result, but they should not implicitly carry the same
engineering authority.

---

## B. Interpretation, Heterogeneity, and Uncertainty

### Core questions

- Are differences between sources merely technical or also semantic?
- Do sources use different terminology, abstraction levels, or granularity?
- Where is semantic interpretation required?
- Can multiple interpretations be plausible?
- How are ambiguity, disagreement, and missing information exposed?
- When should processing stop rather than infer missing engineering information?

### Main claims

- C4
- C5
- C7
- C8

### Core message

Technical access to engineering information does not resolve semantic heterogeneity.

Probabilistic AI is especially relevant where information must be interpreted.
However, agreement between AI-generated interpretations is decision evidence rather
than proof of engineering correctness. Ambiguity and uncertainty should therefore
remain visible process states.

---

## C. Engineering Authority and Controlled Processing

### Core questions

- Which AI outputs are proposals?
- Which decisions have engineering consequences?
- Who is authorized to approve such decisions?
- What exactly is being authorized?
- Is engineering meaning already sufficiently determined?
- Is the target representation uniquely defined?
- Which downstream steps can become rule-bound?

### Main claims

- C9
- C10
- C11
- C12
- C13

### Core message

Probabilistic AI output may support an engineering decision without acquiring
engineering authority itself.

Where necessary, engineering meaning should be distinguished from its structural or
target-specific representation. Once the relevant meaning, authority, and rules have
been resolved, downstream processing can become increasingly constrained and
reproducible.

Unresolved evidence, authority, or transformation rules should not silently
propagate into later engineering states.

---

## D. Traceability, Assurance, and Integration

### Core questions

- Which source supports a result?
- Which AI-generated interpretation contributed to it?
- Which human decision authorized it?
- Which processing context was active?
- How is the resulting artifact checked independently?
- What happens when validation fails?
- How does the workflow integrate with existing engineering roles and quality gates?
- How does human review scale?

### Main claims

- C14
- C15
- C16
- C17
- C18
- C19

### Core message

Traceability needs to cover the combined AI-human engineering process rather than
only the final artifact.

Verification and validation should be separated from generation. Productive use also
requires integration into existing engineering processes, responsibilities, and
quality mechanisms.

Scaling AI-supported engineering therefore creates not only a technical scaling
problem but also a human review and authorization challenge.

---

# III. Design Principles for Controlled AI-Supported Engineering

This section translates the challenges into generalized design principles.

## Principle 1 — Start from the Engineering Task

AI support should be designed around a bounded engineering purpose, required inputs,
expected outputs, and acceptance criteria.

Related claims:
C1

---

## Principle 2 — Establish a Controlled Information Basis

The workflow should explicitly identify which information may justify engineering
statements and which information only supports interpretation or processing.

Information roles and their engineering authority should remain distinguishable.

Related claims:
C2, C3

---

## Principle 3 — Use Probabilistic AI Where Interpretation Is Required

Probabilistic AI is particularly valuable where heterogeneous or semantically open
engineering information has to be interpreted.

Tasks with already-defined source and target semantics may instead benefit from more
deterministic processing.

Related claims:
C4, C5

---

## Principle 4 — Make Uncertainty Explicit

Agreement, disagreement, missing information, and ambiguity should remain visible
states of the engineering process rather than being silently resolved by generation.

Multiple AI perspectives may provide additional decision evidence, but agreement
alone does not establish engineering correctness.

Related claims:
C7, C8

---

## Principle 5 — Keep Engineering Authority Explicitly Assigned

AI-generated interpretations should serve as decision support.

Engineering-relevant state transitions require clearly assigned authority at the
points where meaning, use, representation, or release are materially affected.

The exact authority roles are use-case specific.

Related claims:
C9, C10, C11

---

## Principle 6 — Constrain Downstream Processing Once Decisions Are Resolved

Once engineering meaning, required authority, and transformation rules are
sufficiently determined, subsequent processing should be constrained and
reproducible wherever appropriate.

If required evidence, authority, or rules remain unresolved, the process should expose
the unresolved state rather than infer a convenient continuation.

Related claims:
C11, C12, C13

---

## Principle 7 — Preserve Provenance and Traceability

Sources, relevant processing conditions, AI-generated interpretations, human
decisions, intermediate states, and resulting engineering artifacts should remain
connected.

For multi-source workflows, information may be integrated without losing its
source-local provenance.

Related claims:
C14, C15, C16

---

## Principle 8 — Verify Results Independently of Their Generation

The resulting engineering artifact needs verification or validation appropriate to
its purpose and target representation.

The process that generates an artifact should not be treated as sufficient evidence
of its correctness.

Related claims:
C6, C17

---

# IV. A Structured Path Toward Productive Use

This section provides a practice-oriented framework for structuring an AI-supported
engineering initiative.

The sequence is not intended as a universal or prescriptive implementation lifecycle.
It represents a structured set of questions that should be resolved when designing an
AI-supported engineering workflow.

## Step 1 — Define the Engineering Task and Intended Outcome

Clarify:
- engineering purpose
- expected result
- scope
- acceptance criteria
- boundaries of AI support

Relevant claims:
C1

---

## Step 2 — Establish the Information Basis

Clarify:
- relevant sources
- information authority
- contextual information
- source identity
- processing context

Relevant claims:
C2, C3

---

## Step 3 — Identify Where Interpretation Is Required

Clarify:
- semantic heterogeneity
- domain knowledge
- ambiguous terminology
- missing formal structure
- areas where deterministic rules are insufficient

Relevant claims:
C4, C5

---

## Step 4 — Define How Uncertainty Is Handled

Clarify:
- ambiguity
- conflicting interpretations
- incomplete information
- escalation conditions
- stopping conditions

Relevant claims:
C7, C8, C13

---

## Step 5 — Assign Engineering Authority

Clarify:
- which decisions are consequential
- who owns them
- what is being authorized
- when renewed authorization is required

Relevant claims:
C9, C10, C11

---

## Step 6 — Define Controlled States, Rules, and Constraints

Clarify:
- what constitutes an authorized engineering state
- which transformations may be rule-bound
- which target-specific constraints apply
- what prevents unresolved information from propagating

Relevant claims:
C11, C12, C13

---

## Step 7 — Establish Traceability and Independent Verification

Clarify:
- source-to-result traceability
- AI-output-to-decision traceability
- processing provenance
- verification mechanisms
- validation and release criteria

Relevant claims:
C14, C15, C16, C17

---

## Step 8 — Integrate, Evaluate, and Scale

Clarify:
- integration with existing engineering roles
- quality gates
- review effort
- operational ownership
- scalability of human authorization
- criteria for expanding the use case

Relevant claims:
C18, C19

---

# Proposed Central Figure

## Controlled AI-Supported Engineering Workflow

Engineering Information
        |
        v
+----------------------+
| Information Basis    |
+----------------------+
        |
        v
+----------------------+
| Probabilistic        |
| Interpretation       |
| + Uncertainty        |
+----------------------+
        |
        v
   Human Authority
        |
        v
+----------------------+
| Controlled           |
| Engineering State    |
+----------------------+
        |
        v
+----------------------+
| Rules & Constraints  |
+----------------------+
        |
        v
Engineering Result
        |
        v
Verification / Validation

Provenance and Traceability span the complete workflow.

---

# Proposed Table I

## Key Questions for AI-Supported Engineering

| Area | Key Question | Design Implication |
|---|---|---|
| Task | What engineering outcome is required? | Bound the use case |
| Information | What may justify an engineering statement? | Define information roles |
| Interpretation | Where is semantic judgement required? | Place probabilistic AI deliberately |
| Uncertainty | What if information is ambiguous or incomplete? | Expose unresolved states |
| Authority | Who authorizes consequential decisions? | Assign explicit human authority |
| Processing | What can become rule-bound? | Constrain downstream processing |
| Traceability | Why does this result exist? | Preserve provenance and traceability |
| Assurance | How is correctness checked? | Verify independently of generation |

---

# V. Discussion

## A. Transferability

Several mechanisms investigated in the underlying thesis are not inherently bound to
SysML v2:

- controlled source processing
- explicit information roles
- probabilistic interpretation
- explicit uncertainty
- human authorization
- persistent engineering states
- provenance
- traceability
- rule-bound downstream processing
- independent verification

These mechanisms provide the basis for generalizing the findings to other
information-centered engineering tasks.

---

## B. Context Dependence

The following aspects remain use-case specific:

- source taxonomy
- relevant domain knowledge
- number and role of AI perspectives
- authority model
- representation of engineering states
- downstream transformation rules
- target artifact
- verification mechanisms
- acceptable automation level
- governance model
- review effort

The paper should therefore identify design questions and principles rather than
prescribe one universal implementation.

---

## C. Limits of the Evidence

The underlying investigation focused on:

- heterogeneous legacy engineering information
- transformation into SysML v2
- a controlled prototype
- repeated transformation runs
- bounded single- and multi-source scenarios

Therefore:

- transferable design principles may be derived,
- but empirical validation across all engineering domains is not claimed,
- productive organizational scaling remains future work.

---

## D. Organizational Implication

Productive AI-supported engineering requires the coordinated consideration of:

- engineering process design
- information management
- domain semantics
- AI workflow design
- decision authority
- verification and validation
- provenance and traceability
- integration into existing engineering practices

This implication should remain neutral and should not be formulated as an explicit
sales claim.

---

# VI. Conclusion

## Core conclusion

Generative AI can extend the range of engineering activities that can be supported by
automation, particularly where heterogeneous information requires interpretation.

Its productive use, however, depends on more than model capability.

A controlled AI-supported engineering workflow requires:

- a defined engineering task
- a controlled information basis
- explicit handling of uncertainty
- clearly assigned engineering authority
- constrained downstream processing where possible
- end-to-end provenance and traceability
- independent verification and validation
- integration into existing engineering processes

## Intended closing statement

The transition from AI experimentation to engineering practice is therefore primarily
a systems and process design challenge rather than a prompting problem.

---

# Candidate Title

Making AI Work in Engineering:
Key Questions, Challenges, and Design Principles for Controlled AI-Supported Engineering

## Alternatives

From AI Potential to Engineering Practice:
Design Principles for Controlled AI-Supported Engineering

Engineering with Generative AI:
A Structured Approach to Information, Authority, Traceability, and Verification

---

# Target Length

IEEE two-column format:

- Abstract: 150–200 words
- I. Introduction: 0.75–1 page
- II. Challenges: 1.25–1.5 pages
- III. Design Principles: 2–2.25 pages
- IV. Structured Path: 1–1.25 pages
- V. Discussion: 0.75–1 page
- VI. Conclusion: 0.3–0.5 page

Target total:
approximately 7–8 pages including figure, table, and references.

---

# Citation Strategy

1. Prefer original literature already used in the Master's thesis.
2. Do not cite the Master's thesis where an original source is available.
3. Thesis-derived architecture findings may be presented as findings from the
   underlying investigation.
4. Clearly distinguish:
   - literature-supported statements,
   - findings from the investigated architecture,
   - generalized design implications,
   - future challenges.
5. Add new literature only if a required paper claim cannot be supported by the
   existing thesis literature or the underlying investigation.
