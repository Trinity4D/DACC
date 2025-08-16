# Namespace, Class, Field, Property, Method

## Namespace
**Syntax:** `namespace <NamespaceName>`  
**Behavior:** Logical container for types. Types declared inside are referenced as `<NamespaceName>.<TypeName>`.

## Class (is a Type)
**Syntax:** `type <TypeName> { /* members */ }`  
**Instancing:** `new <NamespaceName>.<TypeName>()` creates a Container with default field values:
- `int` fields default to `0`
- `string` fields default to `""`
- References default to `null`
**Visibility:** Members default to `private` unless declared `public`.

## Field
**Syntax:** `<mod> <Type> <name>;`  
**Behavior:** Direct storage. Reading/writing mutates Container state.  
**Modifiers:** `public`, `private`, `readonly`

## Property
**Syntax:** `<mod> <Type> <Name> { get; set; }`  
**Behavior:** get returns a value, set may validate before storing to a backing field.  
**Determinism:** On validation failure, return `PROPERTY_VALIDATION_FAILED` and leave state unchanged.

## Method
**Syntax:** `<mod> <ReturnType> <Name>(<Type <paramName>, ...>)`  
**Modifiers:** `public`, `private`, `static`  
**Behavior:** Deterministic mapping from inputs + Container state → output (+ permitted state updates).