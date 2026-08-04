---
title: "Case Study: Inspection-Readiness Tracking Model"
description: Designing a matrix-based tracking model to make regulatory inspection readiness measurable across staff files, training, appraisals, and supervision.
---

[Home](../) · [Skills matrix](../skills/skills-matrix.html) · [Roadmap](../roadmap.html)

# Case Study: Inspection-Readiness Tracking Model

**Context:** the same CQC-regulated UK domiciliary care provider. Preparing for a regulatory inspection covering six evidence areas across a 136-file staff estate.

**My role:** Business Analyst — decomposing the regulatory requirement into trackable data, designing the matrix structure, and defining the gap-detection logic.

**Note on scope:** this case study describes the tracking model and method. The provider's actual compliance position and the specific gaps found are not published — they are commercially and regulatorily sensitive, and publishing them would tell you nothing about my analysis that the model doesn't already show.

## Problem

Inspection readiness was being tracked as a to-do list, not as data. Six evidence areas — staff files, policies and procedures, training, appraisals, supervision, and spot checks — each sat with a different owner, in a different format, with no single view of what was complete, what was outstanding, and what was expiring.

The failure mode of a list is that it tells you what has been *done*. It does not tell you what is *missing*, and inspection turns on the missing.

## Approach

1. **Decompose the requirement.** Translate each of the six evidence areas into the specific record types that evidence it, and the state each record must be in to count.
2. **Define completeness per record, not per area.** An employee is not "compliant"; a named document either exists, is in date, and is verifiable, or it does not.
3. **Separate completion from expiry.** Training completion and document validity are different failure modes. A matrix that only tracks completion silently passes expired records.
4. **Route the exceptions.** Build the model so the gap list — not the completion percentage — is the working output.

## What Was Built

Four linked tracking matrices:

| Matrix | Tracks | Primary output |
| --- | --- | --- |
| Staff Matrix | Per-employee file completeness by document type | Missing-document exception list |
| Training Matrix | Mandatory and online course completion per employee | Below-threshold employee list |
| Appraisal Matrix | Appraisal currency and scheduling | Overdue appraisal list |
| Master HR Matrix | Document expiry, including right-to-work and insurance validity | Forward expiry calendar |

**Design decisions worth naming:**

- Expiry dates are held as dates, not as a yes/no flag — so the model produces a forward view rather than a snapshot.
- Supervision and spot checks are recorded in the care management system by senior staff, with office staff self-completing; the matrix reconciles both routes rather than assuming one.
- Appraisal completion was deliberately sequenced into the individual file reviews rather than run as a parallel exercise, to avoid contacting the same employee twice.

## Outcome

The model converted a six-area readiness question into four exception lists that could be worked down and re-measured weekly, with each gap owned by a named person.

## What I'd Do Differently

- The matrices are spreadsheet-based. The right end state is the same logic in the care management system or Power BI, so status is queried rather than maintained.
- Definitions of "complete" should have been signed off by the registered manager in writing before the first review pass, not agreed as the reviews progressed.

<sub>Last updated: {{ page.last_updated }}</sub>
