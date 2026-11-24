How weivel fits together, across its repositories: which part owns which decision or default, every path by which a plugin, a host, a language implementation, the core and a C library talk to one another, and what weivel leaves to its users. Each file here says what crosses a repository boundary and links down to the repository whose docs hold the detail; how one part works inside is answered in that part's own `docs/`.

- [parts.md](parts.md) - the core, the standard, the language implementations and the conformance kit: what each owns, what it does not, and how each pins the others
- [communication.md](communication.md) - every path: plugin to host, plugin to plugin, language to core, language to a C library, a program to its provider, process to process, and what is not built
- [scope.md](scope.md) - what weivel never forces on its users: names, formats, languages and providers, and what holds each
