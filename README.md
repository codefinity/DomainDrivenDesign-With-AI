<a href="https://www.reddit.com/r/DDDWithAI/" target="_blank" rel="noopener noreferrer"><img src="assets/DDDWithAI-Header.png" alt="DDDWithAI: Domain-Driven Design × AI-assisted development"></a>

# DomainDrivenDesign-With-AI

Experiments in using AI to carry out Domain-Driven Design (DDD) work, starting from a real-sized business requirements document (BRD) and moving towards a solution design.

## Index

- [Community](#community)
- [Experiments](#experiments)
  - [Experiment 1: Getting bounded contexts from a BRD](Experiment-%231-Getting-Bounded-Contexts-From-BRD/1.1/README.md)

## Community

<a href="https://www.reddit.com/r/DDDWithAI/" target="_blank" rel="noopener noreferrer"><b>r/DDDWithAI</b></a> is the Reddit community for this repository, where Domain-Driven Design meets AI-assisted development.

DDD has always depended on conversations: with domain experts, between teams, and across boundaries. AI tools can now take part in that work, from reading requirements and proposing bounded contexts to drafting aggregates and writing code. The community explores where they help, where they fall short, and how to keep the domain model at the centre.

Topics include:

- **Strategic design with AI:** finding subdomains and bounded contexts, building context maps, and choosing relationship patterns.
- **Tactical design with AI:** modelling aggregates, entities, value objects and domain events, and keeping invariants intact.
- **Ubiquitous language:** using AI to pull the domain's terms out of requirements, interviews and existing code.
- **Event storming and modelling workshops** run with or assisted by AI.
- **From model to code:** turning a domain model into a working system, such as a modular monolith or microservices.
- **Prompts, workflows and tools** that worked, and the ones that did not.

Join to:

- discuss the experiments in this repository and suggest new ones
- share your own experiments, prompts and results
- ask questions and review each other's models

Contributions and critique of these experiments are welcome there, or as issues and pull requests here.

## Experiments

Each experiment lives in its own folder, with a README describing its goal, input, output and results.

| # | Experiment | Input | Output |
| --- | --- | --- | --- |
| 1 | [Getting bounded contexts from a BRD](Experiment-%231-Getting-Bounded-Contexts-From-BRD/1.1/README.md) | HexCommerce BRD v3.0 | Context map: 20 bounded contexts, 4 external systems, 45 relationships |
