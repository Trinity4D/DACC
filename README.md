# &#x20;Deterministic Agent Construction Contract

# ![DACC Logo](logo.png)

**DACC** is a cutting-edge project designed to provide a deterministic, structured framework for building and managing agents, classes, methods, and runtime environments. It aims to simplify the flow of construction and enhance code clarity for developers and AI agents alike.

---

## Features

- Canonical structure for Namespaces, Types, Classes, Fields, Properties, Methods, Interfaces, and Containers.
- Deterministic runtime behavior for AI agents.
- Modular and extendable design using supporting libraries.
- Minimal but precise specification to guide agent construction and execution.

---
# DACC Quick Reference

## 1. Purpose
- Defines canonical structure and runtime behavior for:  
  `Namespace`, `Type`, `Class`, `Field`, `Property`, `Method`, `Interface`, `Container`  
- Deterministic contract — **agent must treat as authoritative**.

## 2. Conventions
- Identifiers: `[A-Za-z_][A-Za-z0-9_]*`  
- Types: `int`, `string`, `void` or `<NamespaceName>.<TypeName>`  
- Signatures: exact name + ordered parameters  
- Matching: **case-sensitive**  
- Errors: deterministic strings (see Errors section)

## 3. Placeholders
| Placeholder | Meaning |
|-------------|---------|
| `<NamespaceName>` | Namespace identifier |
| `<TypeName>` | Class/type identifier |
| `<Identifier>` | Generic variable/param identifier |
| `<Type>` | Primitive or qualified type |
| `<Name>` | Public member (Property/Method) |
| `<name>` | Private field |
| `<paramName>` | JSON param key |
| `<ReturnType>` | Return type |
| `<mod>` | Modifier (`public`, `private`, `readonly` etc.) |

## 4. Elements & Syntax
| Element | Syntax | Notes |
|---------|-------|------|
| Namespace | `namespace <NamespaceName>` | Logical container |
| Class | `type <TypeName> { /* members */ }` | Instance: `new <NamespaceName>.<TypeName>()` |
| Field | `<mod> <Type> <name>;` | Direct storage, mutates container |
| Property | `<mod> <Type> <Name> { get; set; }` | Validation failure → `PROPERTY_VALIDATION_FAILED` |
| Method | `<mod> <ReturnType> <Name>(<Type <paramName>, ...>)` | Deterministic mapping from inputs+state |
| Interface | `interface <TypeName> { <ReturnType> <MethodName>(<Type <paramName>, ...>); }` | External call contract |

## 5. Runtime (DACC.Runtime)
- **CoPilot**: singleton, handles `Prompt(string)`  
- **Container**: singleton, manages `./libs/*.md`  
- **Compiler**: selects & injects relevant libraries, executes user prompts  
- **File**: read abstraction  
- **Directory**: list files abstraction  

## 6. Contract on Load
1. Treat spec as authoritative  
2. Inject runtime classes: `CoPilot`, `Container`, `Compiler`, `File`, `Directory`  
3. Load supporting libraries once (`_factory.md` files), deterministic  
4. Validate user prompts → return deterministic errors if invalid  
5. Compile/execute prompts via `Runtime.Compiler.Compile(prompt)`  
6. Optional: load only requested libraries  
7. Strict placeholder matching (case-sensitive, param names, types)  
8. Deterministic execution only via declared fields/properties/interfaces  

## 7. Errors
- `INTERFACE_SIGNATURE_MISMATCH`  
- `PROPERTY_VALIDATION_FAILED`  
- `METHOD_INVOCATION_FAILED`  
- `TYPE_NOT_FOUND`  
- `UNRECOGNIZED_CONSTRUCT`  

## 8. Machine Schema (JSON)
- Conventions, placeholders, elements, modifiers, and errors  
- Fully maps the DACC spec for programmatic consumption


## Installation

```bash
# Clone the repository
git clone https://github.com/Trinity4D/DACC.git

# Navigate to the project folder
cd DACC

# (Optional) Install dependencies if needed
```

---

## Internals

Brief overview of the internal structures

```csharp
namespace DACC.Runtime

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
      var question = "libs: {libs} ibjected here";

      //ask CoPilot the question
      var libAnswer = CoPilot.Instance.Prompt(question);
      
      // Read in all libs **into context**
      File.ReadAll(libAnswer);

      var seed = "Please Invoke Each ";

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
```

## Usage

Provide a brief example of how to use DACC in code:

```csharp
// Example usage
using DACC.Runtime;

var compiler = new Compiler();
compiler.Compile("Your prompt here");
```

---

## Contributing

We welcome contributions! Please follow these steps:

1. Fork the repository.
2. Create a feature branch: `git checkout -b feature/my-feature`
3. Commit your changes: `git commit -m 'Add some feature'`
4. Push to the branch: `git push origin feature/my-feature`
5. Open a Pull Request

---

## License

This project is licensed under the **MIT License**. See the [LICENSE](LICENSE) file for details.

---

## Contact

For questions or support, contact **Zack Evans** at [[z.evans@caprisoft.co.za](mailto\:z.evans@caprisoft.co.za)].

