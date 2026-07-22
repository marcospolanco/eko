# EKO — Executable Knowledge Objects

EKO is a proposal for treating knowledge used by AI systems as a governed, testable, and executable artifact rather than an inert document or retrieved chunk.

> **Status:** Experimental reference design. This repository contains draft schemas, architecture proposals, experiment designs, examples, and explanatory media. It does not yet contain a validated reference runtime or empirical results.

## Start here

### Watch the walkthrough

[![Watch the EKO walkthrough](https://img.youtube.com/vi/QaH7BSplEnY/maxresdefault.jpg)](https://youtu.be/QaH7BSplEnY)

[Watch on YouTube](https://youtu.be/QaH7BSplEnY)

### Listen to the audio overview

[![Listen to the EKO audio overview](https://img.shields.io/badge/▶_Listen_to_Audio-EKO_Overview-blue?style=for-the-badge&logo=podcast)](media/Securing_AI_with_Executable_Knowledge_Objects.mp3)

[Open the EKO podcast](media/Securing_AI_with_Executable_Knowledge_Objects.mp3)

## The idea at a glance

Enterprise knowledge is moving from material that humans interpret to instruction that AI systems may use to make decisions and take action. EKO asks what that knowledge would look like if it received the versioning, testing, authority, deployment, and rollback discipline historically reserved for code.

[![The evolution of enterprise knowledge from documents to executable behavior](media/Evolution_of_Enterprise_Knowledge.png)](media/Evolution_of_Enterprise_Knowledge.png)

*Concept map of the EKO thesis. Click the image to view it at full resolution.*

The proposed execution boundary is:

```text
governed EKO release
  + verified runtime facts
  + externally granted capabilities
  + pinned interpreter
  -> validated workflow instance
  -> bounded execution
  -> durable trace
```

The model may help translate at the authoring and presentation boundaries. It does not become the source of policy authority, capability grants, or release approval.

## Explore the repository

| Path | What it contains |
|---|---|
| [`schemas/`](schemas/) | Draft JSON Schemas for EKO profiles, Rule IR, and resolution profiles |
| [`generator/`](generator/) | Model-assisted generation architecture and safe bootstrapping proposals |
| [`experiments/`](experiments/) | Preregistration-style designs for retrieval, authoring, resolution, and runtime-boundary studies |
| [`examples/`](examples/) | Authoring guidance and progressive EKO examples |
| [`deck/`](deck/) | EKO presentation source and speaker notes |
| [`media/`](media/) | Video, podcast, and NotebookLM media artifacts |

## What remains to be proven

- Whether EKO analysis detects operational knowledge defects that strong RAG-evaluation baselines miss.
- Whether model-assisted prose-to-IR translation can avoid silent invention at acceptable review cost.
- Whether deterministic resolution remains correct across overlapping policies, time, authority, and unknown facts.
- Whether the compile/link/runtime boundary prevents undeclared or unauthorized behavior under adversarial conditions.
- Whether organizations will adopt more than the additive shadow-mode quality layer.

The project is designed to support **proceed**, **narrow**, **revise**, or **stop** outcomes as evidence accumulates.
