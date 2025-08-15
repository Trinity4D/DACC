# &#x20;Deterministic Agent Construction Contract (DACC)



**DACC** provides a **deterministic, structured framework** for building and managing agents, classes, methods, and runtime environments. It simplifies the flow of construction, enhances clarity, and enforces deterministic behavior for AI agents and developers.

---

## 🌟 Features

- Canonical structure for **Namespaces, Types, Classes, Fields, Properties, Methods, Interfaces, Containers**.
- Deterministic runtime behavior for AI agents.
- Modular, extendable design via supporting libraries (`_factory.md` files).
- Minimal yet precise specification guiding agent construction and execution.
- Clear error contracts for predictable outcomes.

---

## 📖 Quick Reference

### 1. Purpose

Defines canonical structure and runtime behavior for:

`Namespace`, `Type`, `Class`, `Field`, `Property`, `Method`, `Interface`, `Container`

> Deterministic contract — **agent must treat as authoritative**.

### 2. Conventions

- **Identifiers:** `[A-Za-z_][A-Za-z0-9_]*`
- **Types:** `int`, `string`, `void` or `<NamespaceName>.<TypeName>`
- **Signatures:** exact match on name + parameter order
- **Matching:** **case-sensitive**
- **Errors:** deterministic strings (see Errors section)

### 3. Placeholders

| Placeholder       | Meaning                                    |
| ----------------- | ------------------------------------------ |
| `<NamespaceName>` | Namespace identifier                       |
| `<TypeName>`      | Class/type identifier                      |
| `<Identifier>`    | Generic variable/parameter identifier      |
| `<Type>`          | Primitive or qualified type                |
| `<Name>`          | Public member (Property/Method)            |
| `<name>`          | Private field                              |
| `<paramName>`     | JSON parameter key                         |
| `<ReturnType>`    | Return type                                |
| `<mod>`           | Modifier (`public`, `private`, `readonly`) |

### 4. Elements & Syntax

| Element   | Syntax                                                     | Notes                                             |
| --------- | ---------------------------------------------------------- | ------------------------------------------------- |
| Namespace | `namespace <NamespaceName>`                                | Logical container                                 |
| Class     | `type <TypeName> { /* members */ }`                        | Instance: `new <NamespaceName>.<TypeName>()`      |
| Field     | `<mod> <Type> <name>;`                                     | Direct storage, mutates container                 |
| Property  | `<mod> <Type> <Name> { get; set; }`                        | Validation failure → `PROPERTY_VALIDATION_FAILED` |
| Method    | `<mod> <ReturnType> <Name>(...)`                           | Deterministic mapping from inputs + state         |
| Interface | `interface <TypeName> { <ReturnType> <MethodName>(...); }` | External call contract                            |

---

## ⚙️ Runtime Components

- **CoPilot:** Singleton agent handling `Prompt(string)`
- **Container:** Singleton managing `./libs/*.md` files
- **Compiler:** Selects and injects relevant libraries, executes user prompts
- **File:** Abstraction for reading library files
- **Directory:** Abstraction for listing directories

### Contract on Load

1. Treat spec as authoritative
2. Inject runtime classes: `CoPilot`, `Container`, `Compiler`, `File`, `Directory`
3. Load supporting `_factory.md` libraries **once**, deterministically
4. Validate user prompts → return deterministic errors if invalid
5. Compile/execute prompts via `Runtime.Compiler.Compile(prompt)`
6. Optional: load only explicitly requested libraries
7. Strict placeholder matching (case-sensitive, param names, types)
8. Deterministic execution only via declared fields/properties/interfaces

### Errors

- `INTERFACE_SIGNATURE_MISMATCH`
- `PROPERTY_VALIDATION_FAILED`
- `METHOD_INVOCATION_FAILED`
- `TYPE_NOT_FOUND`
- `UNRECOGNIZED_CONSTRUCT`

---

## 🏗 Internals Overview

```csharp
namespace DACC.Runtime

class CoPilot {
    public static CoPilot Instance => this;
    public object Prompt(string prompt);
}

class Container {
    static Dictionary<string, object> _registry = new();
    static string _ext = ".md";
    static string[] _libFiles = new[];

    static Container() {
        _libFiles = File.GetList(Root);
    }
    public static string Root => "./libs/";
    public static Container Instance => this;

    public object GetService(string name) {
        if (!_registry.ContainsKey(name)) {
            _registry.Add(name, File.Read(Root + name + _ext));
        }
        return _registry[name];
    }
}

class Compiler {
    public object Compile(string prompt) {
        var libs = string.Join(",", File.GetList().Select(p => p.FullPath));
        var question = $"libs: {libs} injected here";
        var libAnswer = CoPilot.Instance.Prompt(question);
        File.ReadAll(libAnswer);
        return CoPilot.Instance.Prompt(prompt);
    }
}

class File {
    public static string Read(string path);
    public static void ReadAll(string[] paths);
    public static string[] GetList(string path);
}

class Directory {
    public static string[] GetList(string path);
}
```

---

## 💡 Usage Example

```csharp
using DACC.Runtime;

var compiler = new Compiler();
var output = compiler.Compile("Analyze current agent state and respond deterministically");
```

---

## 📥 Installation

```bash
# Clone repository
git clone https://github.com/Trinity4D/DACC.git
cd DACC
# Optional dependencies installation
```

---

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/my-feature`
3. Commit changes: `git commit -m 'Add some feature'`
4. Push: `git push origin feature/my-feature`
5. Open a Pull Request

---

## 📜 License

MIT License — see [LICENSE](LICENSE)

---

## 📬 Contact

Zack Evans — [z.evans@caprisoft.co.za](mailto\:z.evans@caprisoft.co.za)

