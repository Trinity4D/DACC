# Specification: The Flow of Construction — Compact Contract for Agents

**Purpose:**  
Provide a minimal, unambiguous specification that defines the canonical structure and runtime behavior of `Namespace`, `Type`, `Class`, `Field`, `Property`, `Method`, `Interface`, and `Container` for a learning machine (agent) untrained on code. This file is a deterministic contract: when loaded as context, the agent **must** treat it as authoritative.

---

## Conventions
- **Identifiers:** ASCII `[A-Za-z_][A-Za-z0-9_]*`.
- **Types:** explicit tokens (e.g., `int`, `string`, `void`) or qualified user types `<NamespaceName>.<TypeName>`.
- **Signatures:** exact match on name + ordered parameter types.
- **Matching:** case-sensitive.
- **Errors:** deterministic strings defined in **Errors**.

---

## Placeholders & Descriptors
Minimal, unambiguous meanings for bracketed placeholders:
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

Rule: each placeholder denotes a single token position. Callers must provide tokens matching these descriptors.

---

## Namespace
**Syntax:** `namespace <NamespaceName>`  
**Behavior:** Logical container for types. Types declared inside are referenced as `<NamespaceName>.<TypeName>`.

---

## Class (is a Type)
**Syntax:** `type <TypeName> { /* members */ }` (declared inside a namespace)  
**Instancing:** `new <NamespaceName>.<TypeName>()` creates a Container with default field values:  
- `int` fields default to `0`  
- `string` fields default to `""`  
- References default to `null`  
**Visibility:** Members default to `private` unless declared `public`.

---

## Field
**Syntax:** `<mod> <Type> <name>;` e.g., `private int counter;`  
**Behavior:** Direct storage. Reading/writing mutates Container state.  
**Modifiers:** `public`, `private`, `readonly`

---

## Property
**Syntax:** `<mod> <Type> <Name> { get; set; }` or explicit `get{}`/`set{}`  
**Behavior:** 
- `get` returns a value.  
- `set` may optionally validate before storing to a backing Field.  
**Determinism:** On validation failure, return `PROPERTY_VALIDATION_FAILED` and leave state unchanged. If no validation is defined, `set` always succeeds.

---

## Method
**Syntax:** `<mod> <ReturnType> <Name>(<Type <paramName>, ...>)`  
**Modifiers:** `public`, `private`, `static`  
**Behavior:** Deterministic mapping from inputs + Container state → output (+ permitted state updates).  
- **Return types:** explicit; `void` means no value.  
- **Side effects:** Only allowed on Container Fields/Properties declared in spec or via explicit Interface calls.

---

## Interface & External Calls
**Syntax:** `interface <TypeName> { <ReturnType> <MethodName>(<Type <paramName>, ...>); }` (declared in a namespace)  

**External call behavior:**
1. Locate implementing type by exact `<NamespaceName>.<TypeName>` and verify it implements the Interface name.
2. Resolve method by exact signature match (method name, parameter count, parameter types).
3. Serialize parameters as deterministic JSON with keys matching `<paramName>`.
4. Invoke method; receiver returns JSON:  
   - success: `{ "status": "OK", "return": <value> }`  
   - error: `{ "status": "ERROR", "error": <ERROR_CODE>, "message": <text> }`  
5. On signature mismatch, return `INTERFACE_SIGNATURE_MISMATCH`.

> Note: JSON parameter order is **not** significant; matching uses parameter names and declared types.

---

## Errors
- `INTERFACE_SIGNATURE_MISMATCH`
- `PROPERTY_VALIDATION_FAILED`
- `METHOD_INVOCATION_FAILED`
- `TYPE_NOT_FOUND`
- `UNRECOGNIZED_CONSTRUCT` (for unhandled or malformed input)

---

## Runtime
namespace Runtime

//The Built in CoPilot Agent
class CoPilot {  
  
  //Singleton Instance
  public static CoPilot Instance ()=> this;

  // CoPilot Native Prompt Abstraction
  public object Prompt(string prompt);
}

//The Container is a locator for lib files located in [./libs/*.md]
class Container { 
  static Dictionary<string, object> _registry = new();
  
  static string _ext = ".md";
  static string[] _libFiles = new[];

  //runs when this specification document is loaded
  static Container(){
      // lib files located in [./libs/*.md]
      _libFiles = File.GetList(Root);
  }
  public static string Root ()=> "./libs/";

  //Singleton Instance
  public static Container Instance ()=> this;
  
  // Read in any lib *.md file only once. **singleton**
  public object GetService(string name)
  {
    if(!_registry.ContainsKey(name))
    {
      _registry.Add(name, File.Open(Root + name + _ext));
    }

    return _registry[name];
  }
}
class Compiler {  
  
  // Compiles a User Prompt and returns each element in code block format
  public object Compile(string prompt)
  {
      // Get's all lib files and select only name into array and join the string
      var libs = String.Join(",", File.GetList().Select(p=> p.FullPath));

      //inject libs into string
      var question = "Here is a list of libs files: {libs}. return a list by searching through each file and determining if this file is applicable to the user's request. If a user request for lib(s), then that would be final and exclusive. Please return as a list of names, could be one or more, could be none if not found";

      //ask CoPilot the question
      var libAnswer = CoPilot.Instance.Prompt(question);
      
      // Read in all libs **into context**
      File.ReadAll(libAnswer);

      var coPilotOutput = CoPilot.Instance.Prompt(prompt);
      return coPilotOutput;
  }
}

class File {  

  // Read a single file into context
  public static string Read(string path);
  
  // Read all files into context
  public static string ReadAll(string root, string wildcard);

   // Returns a array of 'full path of file' names
  public static string[] GetList(string path);
}
class Directory {  
  // Returns a array of 'full path of directory' names
  public static string[] GetList(string path);
}

---

## Contract on Load (DACC)

When this DACC specification is loaded as context, the agent **must**:

1. **Treat the specification as authoritative**  
   - All constructs (`Namespace`, `Type`, `Class`, `Field`, `Property`, `Method`, `Interface`, `Container`) must be interpreted exactly as defined in this spec.  
   - Any deviation or ambiguous input must trigger one of the defined deterministic errors.

2. **Inject Runtime Classes**  
   - `Runtime.CoPilot`  
   - `Runtime.Container`  
   - `Runtime.Compiler`  
   - `Runtime.File`  
   - `Runtime.Directory`  
   These classes are globally available to the agent for prompt execution, library management, and compilation.

3. **Load Supporting Libraries**  
   - Automatically read all `./libs/*.md` files **only once** using:  
     ```csharp
     File.ReadAll(Directory.GetList("./libs").Where(f => f.EndsWith("_factory.md")).ToArray());
     ```
   - Selected libraries are injected into context **before processing any user prompt**.
   - Library loading must be **deterministic** (sorted by filename).

4. **Validate User Prompts**  
   - Ensure all requested constructs exist and signatures match exactly.  
   - Reject ambiguous or unrecognized input by returning one of:
     - `INTERFACE_SIGNATURE_MISMATCH`  
     - `PROPERTY_VALIDATION_FAILED`  
     - `METHOD_INVOCATION_FAILED`  
     - `TYPE_NOT_FOUND`  
     - `UNRECOGNIZED_CONSTRUCT`

5. **Compile and Execute Prompt**  
   - Use `Runtime.Compiler.Compile(prompt)` to:  
     1. Determine relevant libraries (via `CoPilot.Prompt`).  
     2. Inject selected libraries into context.  
     3. Execute the user prompt against loaded `Namespace` and `Type` definitions.  
   - All outputs must be **deterministic** based on prompt content + current container state.

6. **Optional File/Library Selection**  
   - If the user prompt explicitly requests certain libraries, only those libraries are loaded.  
   - Otherwise, all supporting `_factory.md` files are considered candidates for injection.

7. **Strict Placeholder Matching**  
   - Parameter names in JSON must exactly match `<paramName>` in method signatures.  
   - Public vs private identifiers (`Name` vs `name`) are case-sensitive.  
   - Type references must match either primitive tokens or fully qualified `<NamespaceName>.<TypeName>`.

8. **Deterministic Execution**  
   - The agent must avoid any non-deterministic behavior (e.g., random file order, implicit type inference).  
   - All runtime state changes occur **only** via declared `Fields`, `Properties`, or explicit Interface calls.

---

*End of DACC Contract on Load*


---

## Machine Schema
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

---

*End of compact specification.*
