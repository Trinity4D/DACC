# Conventions & Placeholders

## Conventions
- **Identifiers:** ASCII `[A-Za-z_][A-Za-z0-9_]*`.
- **Types:** explicit tokens (e.g., `int`, `string`, `void`) or qualified user types `<NamespaceName>.<TypeName>`.
- **Signatures:** exact match on name + ordered parameter types.
- **Matching:** case-sensitive.
- **Errors:** deterministic strings defined in Errors section.

## Placeholders
- `<NamespaceName>`: identifier naming a namespace. Must match `identifier_regex`.
- `<TypeName>`: identifier naming a class (Type). Must match `identifier_regex`.
- `<Identifier>`: generic identifier token (e.g., variable or parameter names).
- `<Type>`: either a primitive token (`int`, `string`, `void`) or a qualified user type `<NamespaceName>.<TypeName>`.
- `<Name>`: public member identifier (case-sensitive). Use for properties and methods.
- `<name>`: private field identifier (case-sensitive).
- `<paramName>`: parameter identifier; JSON keys in external calls must match this exactly.
- `<ReturnType>`: same as `<Type>`; `void` indicates no return value.
- `<mod>`: modifier token; allowed values depend on element:
  - **Fields:** `public`, `private`, `readonly`
  - **Properties:** `public`, `private`
  - **Methods:** `public`, `private`, `static`