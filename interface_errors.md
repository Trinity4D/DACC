# Interface & Errors

## Interface & External Calls
**Syntax:** `interface <TypeName> { <ReturnType> <MethodName>(<Type <paramName>, ...>); }`  

**Behavior:**
1. Locate implementing type by exact `<NamespaceName>.<TypeName>` and verify it implements the interface.
2. Resolve method by exact signature match (method name, parameter count, parameter types).
3. Serialize parameters as deterministic JSON with keys matching `<paramName>`.
4. Invoke method; receiver returns JSON:
   - success: `{ "status": "OK", "return": <value> }`
   - error: `{ "status": "ERROR", "error": <ERROR_CODE>, "message": <text> }`
5. On signature mismatch, return `INTERFACE_SIGNATURE_MISMATCH`.

## Errors
- `INTERFACE_SIGNATURE_MISMATCH`
- `PROPERTY_VALIDATION_FAILED`
- `METHOD_INVOCATION_FAILED`
- `TYPE_NOT_FOUND`
- `UNRECOGNIZED_CONSTRUCT`