# Runtime Classes

namespace DACC.Runtime
{
    class CoPilot { /* Singleton; Prompt abstraction */ }
    class Container { /* Manages library files and singleton registry */ }
    class Compiler { /* Compiles user prompts and injects libraries */ }
    class File { /* File abstraction for reading libraries */ }
    class Directory { /* Directory abstraction */ }
}