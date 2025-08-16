{
  "conventions": {
    "identifier_regex": "[A-Za-z_][A-Za-z0-9_]*",
    "types": ["int","string","void"],
    "case_sensitive": true
  },
  "placeholders": {
    "NamespaceName":"identifier for namespace",
    "TypeName":"identifier for class",
    "Identifier":"generic identifier (variables, params)",
    "Type":"primitive or qualified user type NamespaceName.TypeName",
    "Name":"public member identifier",
    "name":"private field identifier",
    "paramName":"parameter identifier; JSON keys must match exactly",
    "ReturnType":"same as Type",
    "mod":"modifier token; allowed values depend on element"
  },
  "elements": {
    "namespace": {"syntax":"namespace <NamespaceName>"},
    "class": {"syntax":"type <TypeName> { members }","instance":"new <NamespaceName>.<TypeName>()"},
    "field": {"syntax":"<mod> <Type> <name>;","modifiers":["public","private","readonly"]},
    "property": {"syntax":"<mod> <Type> <Name> { get; set; }","errors":["PROPERTY_VALIDATION_FAILED"]},
    "method": {"syntax":"<mod> <ReturnType> <Name>(...)","modifiers":["public","private","static"]},
    "interface": {"syntax":"interface <TypeName> { <ReturnType> <MethodName>(<Type <paramName>, ...>); }"}
  },
  "errors":["INTERFACE_SIGNATURE_MISMATCH","PROPERTY_VALIDATION_FAILED","METHOD_INVOCATION_FAILED","TYPE_NOT_FOUND","UNRECOGNIZED_CONSTRUCT"]
}