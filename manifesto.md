# DACC: Deterministic AI Agent Runtime — Manifesto

## Purpose
DACC defines a **canonical, deterministic runtime** for AI agents. It establishes a **formal contract** for agent behavior, interaction, and external communication, enabling predictable outcomes and unambiguous reasoning.  

All agents are **classes inside namespaces**, and all interaction occurs through **well-defined constructs** (`Fields`, `Properties`, `Methods`, `Interfaces`) following a strict placeholder and type system.

---

## Core Principles

1. **Determinism**
   - All agent behavior must produce the same output given the same inputs and container state.
   - No hidden side effects, random inference, or implicit type conversions.

2. **Canonical Structure**
   - Agents are classes.
   - Types, namespaces, and members must follow strict naming conventions.
   - Interaction only occurs through declared fields, properties, methods, and interfaces.

3. **Minimal Ambiguity**
   - Placeholders like `<TypeName>`, `<paramName>`, `<Name>` have exact meanings.
   - JSON serialization for method calls is deterministic; key order is irrelevant but names must match exactly.

4. **Composable Agents**
   - Agents can load other agent classes from `_factory.md` libraries via a deterministic container.
   - Libraries are injected into the runtime **before prompt execution**.

5. **Error Contracts**
   - Explicit error codes define all deviations (`INTERFACE_SIGNATURE_MISMATCH`, `TYPE_NOT_FOUND`, etc.).
   - Users and AI agents can handle errors deterministically.

---

## Runtime Architecture

The DACC runtime implements a deterministic pipeline:

