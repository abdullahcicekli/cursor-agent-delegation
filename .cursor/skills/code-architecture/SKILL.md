---
name: code-architecture
description: Generic architectural patterns — layered, hexagonal, clean, DDD-lite, MVC, modular monolith — and decision criteria for picking one. Used by architect agent.
estimated-tokens: 2200
used-by: [architect, engineer]
---

# Code Architecture

## Universal principles

- **Dependency direction points inward.** Outer rings depend on inner rings; inner rings know nothing about outer rings.
- **Public surface is minimal.** Module exports only what callers need; everything else is internal.
- **Boundaries are explicit.** Cross-module calls go through declared interfaces, not implementation details.
- **Symmetry over cleverness.** Pick a structural pattern and apply it consistently across the codebase, even when one off-pattern shortcut would save lines.

## Pattern catalog

### Layered

```
presentation → application → domain → infrastructure
```

- Each layer depends only on layers below it.
- Domain has zero framework dependencies.
- Infrastructure (DB, HTTP, queues) implements interfaces declared by the domain.

**Use when**: backend service with clear request/response flow, transactional business logic, single primary persistence.

### Hexagonal (ports & adapters)

```
       (driver adapters: HTTP, CLI, queue consumers)
                       ↓
               [application core]
                       ↓
       (driven adapters: DB, external API, file system)
```

- Core defines ports (interfaces); adapters implement them.
- Core is testable without booting any infrastructure.

**Use when**: multiple input channels (HTTP + CLI + queue), need to swap infrastructure, heavy unit-test coverage of business logic.

### Clean architecture

- Same as hexagonal in spirit; adds explicit "use case" / "interactor" layer between adapters and domain entities.

**Use when**: large codebase, many use cases, strict testability requirements.

### MVC / MVVM (UI)

- View ↔ Controller/ViewModel ↔ Model.
- ViewModel exposes view state; never imports view types.

**Use when**: UI-heavy app with clear screen boundaries.

### Modular monolith

- Single deployment, multiple modules with strict internal boundaries.
- Modules expose a public interface; internals are package-private / not exported.
- Cross-module calls are explicit; no shared mutable state.

**Use when**: small team, fast iteration, future-proof for service extraction.

### Feature-based folder layout

```
src/
  features/
    auth/
      domain/
      application/
      infrastructure/
      ui/
    billing/
      ...
  shared/
```

- Each feature is self-contained.
- `shared/` only for genuine cross-feature primitives (types, utilities).

**Use when**: teams own features end-to-end; encourages vertical slicing over horizontal layers.

## Decision criteria

| Question | If yes → favor |
|---|---|
| Multiple input channels? | hexagonal |
| Heavy domain logic, transactional? | layered or clean |
| UI-driven? | feature-based + MVVM at the leaves |
| Future service extraction likely? | modular monolith |
| Greenfield CRUD app, small scope? | layered (don't over-engineer) |

## ADR format

Every non-trivial architectural decision is documented:

```markdown
# ADR-NNNN: [title]

**Status**: Proposed | Accepted | Superseded by ADR-MMMM
**Date**: YYYY-MM-DD

## Context
[problem, constraints]

## Decision
[what we decided]

## Consequences
[positive, negative, neutral]

## Alternatives considered
[option, why rejected]
```

Store under `docs/adr/` if the project supports it.

## Anti-patterns

- **Anemic domain**: data classes with no behavior; logic scattered across services.
- **Service-locator / god-DI-container**: dependencies fetched magically instead of injected explicitly.
- **Layer-skipping**: presentation talking directly to infrastructure.
- **Premature microservices**: extract to a service when the team or load demands it, not before.
- **Speculative abstractions**: an interface with one implementation and no concrete plan for a second.
