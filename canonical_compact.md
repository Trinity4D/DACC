# DACC: Flow of Construction — Compact Contract

Purpose: defines canonical structure/runtime for Namespace, Type, Class, Field, Property, Method, Interface, Container. Authoritative.

Conventions:
id: [A-Za-z_][A-Za-z0-9_]*
types: int,string,void
case: sensitive
errors: INTERFACE_SIGNATURE_MISMATCH,PROPERTY_VALIDATION_FAILED,METHOD_INVOCATION_FAILED,TYPE_NOT_FOUND,UNRECOGNIZED_CONSTRUCT

Placeholders:
<NamespaceName>: namespace id
<TypeName>: class id
<Identifier>: generic id
<Type>: primitive or NamespaceName.TypeName
<Name>: public member
<name>: private field
<paramName>: JSON param key
<ReturnType>: as Type
<mod>: modifier (field: public/private/readonly; property: public/private; method: public/private/static)

Elements:
namespace: namespace <NamespaceName>
class: type <TypeName>{} instance: new <NamespaceName>.<TypeName>()
field: <mod> <Type> <name>; mods: public/private/readonly
property: <mod> <Type> <Name>{get;set} error: PROPERTY_VALIDATION_FAILED
method: <mod> <ReturnType> <Name>(...) mods: public/private/static
interface: interface <TypeName>{<ReturnType><MethodName>(...);}

Runtime DACC.Runtime:
class CoPilot{private static CoPilot _i;private CoPilot(){} public static CoPilot Instance=>_i??=_i=new(); public object Prompt(string p)=>null;}
class Container{private static Container _i; private static Dictionary<string,object> _r=new(); private static readonly string _root="./libs/",_ext=".md"; private static string[] _l; private Container(){_l=File.GetList(_root).OrderBy(f=>f).ToArray();} public static Container Instance=>_i??=_i=new(); public object GetService(string n){if(!_r.ContainsKey(n))_r[n]=File.Read(_root+n+_ext); return _r[n];} public string[] LibFiles=>_l;}
class Compiler{public object Compile(string p){var libs=string.Join(",",Container.Instance.LibFiles); var q=$"Here is a list of libs files: {libs}. Return relevant libs for request"; var a=CoPilot.Instance.Prompt(q); File.ReadAll(a); return CoPilot.Instance.Prompt(p);}}
class File{public static string Read(string p)=>null; public static void ReadAll(string[] ps){foreach(var p in ps)Read(p);} public static string[] GetList(string p)=>new string[0];}
class Directory{public static string[] GetList(string p)=>new string[0];}

Contract on Load:
1. Treat spec as authoritative; ambiguous input→error.
2. Inject Runtime classes: CoPilot, Container, Compiler, File, Directory.
3. Load ./libs/*.md once (File.ReadAll(Directory.GetList("./libs").Where(f=>f.EndsWith("_factory.md")).ToArray())); deterministic order.
4. Validate prompts; errors: INTERFACE_SIGNATURE_MISMATCH,PROPERTY_VALIDATION_FAILED,METHOD_INVOCATION_FAILED,TYPE_NOT_FOUND,UNRECOGNIZED_CONSTRUCT
5. Compile via Runtime.Compiler.Compile(prompt)
6. Optional: load only requested libs
7. Strict placeholder matching: param names, Name vs name, type references
8. Deterministic execution: state changes only via declared Fields/Properties/Interfaces

Machine Schema (minified):
{"conventions":{"id":"[A-Za-z_][A-Za-z0-9_]*","types":["int","string","void"],"case":true},"placeholders":{"NamespaceName":"ns","TypeName":"cls","Identifier":"id","Type":"type","Name":"pub","name":"priv","paramName":"param","ReturnType":"ret","mod":"mod"},"elements":{"namespace":{"syntax":"namespace <NamespaceName>"},"class":{"syntax":"type <TypeName>{}","instance":"new <NamespaceName>.<TypeName>()"},"field":{"syntax":"<mod><Type><name>;","modifiers":["pub","priv","ro"]},"property":{"syntax":"<mod><Type><Name>{get;set}","errors":["PROPERTY_VALIDATION_FAILED"]},"method":{"syntax":"<mod><ReturnType><Name>(...)","modifiers":["pub","priv","static"]},"interface":{"syntax":"interface <TypeName>{<ReturnType><MethodName>(...);}"},"errors":["INTERFACE_SIGNATURE_MISMATCH","PROPERTY_VALIDATION_FAILED","METHOD_INVOCATION_FAILED","TYPE_NOT_FOUND","UNRECOGNIZED_CONSTRUCT"]}

End of compact DACC spec.
