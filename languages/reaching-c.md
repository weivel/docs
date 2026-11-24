# How each language reaches C

The one place a language implementation is its own: the seam through which it calls the core's binary and a C
library's definitions. Everything above the seam is the same contract in every language
([structure.md](structure.md)); the seam differs because each language crosses into C through a different foreign
interface. Each seam is held to the overhead bounds the kit keeps for that language
([the budgets](https://github.com/weivel/conformance/tree/HEAD/docs/benchmarks.md)).

| language | the core | a C library's definition | detail |
|---|---|---|---|
| C | opened with the platform's loader from the caller's path, every function into one table, so one build runs against whichever core is loaded | the library's own pointer, which a C plugin casts to the struct in the library's header; a description is checked, never bound | [core](https://github.com/weivel/c/tree/HEAD/docs/core.md), [C libraries](https://github.com/weivel/c/tree/HEAD/docs/host/libraries.md) |
| Java | one multi-release jar: the foreign function and memory API on Java 22 and later, JNA on Java 8 to 21, chosen by the Java that runs it; every struct read through one table of offsets the tests compare with the C compiler's layout | without a description, the very address as a `MemorySegment` or a JNA `Pointer`; with one, a `BoundDefinition` built from the description at the load | [core](https://github.com/weivel/java/tree/HEAD/docs/modules/core.md), [using the jar](https://github.com/weivel/java/tree/HEAD/docs/using.md), [C libraries](https://github.com/weivel/java/tree/HEAD/docs/modules/host/libraries.md), [descriptions](https://github.com/weivel/java/tree/HEAD/docs/modules/host/descriptions.md) |
| TypeScript | koffi, a native addon that calls C from JavaScript, loading the core and the standard from the package's own `native/` unless the caller passes a path | without a description, a `LibraryDefinition` holding koffi's pointer to the very struct; with one, a `BoundDefinition` with one async method per operation, an operation marked blocking running on koffi's worker thread | [core](https://github.com/weivel/typescript/tree/HEAD/docs/modules/core.md), [C libraries](https://github.com/weivel/typescript/tree/HEAD/docs/modules/libraries.md), [descriptions](https://github.com/weivel/typescript/tree/HEAD/docs/modules/descriptions.md), [building](https://github.com/weivel/typescript/tree/HEAD/docs/building.md) |
