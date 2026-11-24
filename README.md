weivel is a plugin platform: a host loads plugins, and weivel decides whether each one is compatible, the order they activate in, what each may reach and which provider answers it, how a wait for a service settles, and what is revoked when a plugin stops. Those decisions are made once, in a core written in C, so a plugin in any language meets the same ones. Around the core stand a replaceable C library of defaults, the standard, which reads the JSON manifest, finds plugins on disk and defines the services a host offers; one language implementation each for C, Java and TypeScript, which carries out the core's answers in its language and decides nothing; and a conformance kit, which holds every one of them to one body of vectors and checks. This tree is the top of weivel's docs: how the repositories relate and talk, what weivel never forces on its users, and what every language implementation holds. Anything about one repository lives in that repository's own `docs/`, which the docs here link down into.

- [architecture/](architecture/) - how weivel fits together: what each part owns, every path by which the parts talk, and what weivel never forces
- [languages/](languages/) - what every language implementation holds, how a new one is added, and how each language reaches C

<!-- generated: org-repos -->
- [weivel/c](https://github.com/weivel/c) - the C language implementation, for a host written in C that runs plugins written in C
- [weivel/conformance](https://github.com/weivel/conformance) - the conformance kit: weivel's behaviour as vectors, the runner, the drift checks and every timing budget
- [weivel/core](https://github.com/weivel/core) - the plugin engine in C, where every decision is made, and the contract every language implementation follows
- [weivel/java](https://github.com/weivel/java) - the Java language implementation, one jar for Java 8 and later
- [weivel/standard](https://github.com/weivel/standard) - the replaceable defaults in C: the JSON manifest, layout and discovery, identities, the service definitions and the mirror list
- [weivel/typescript](https://github.com/weivel/typescript) - the TypeScript language implementation, for plugins written in TypeScript or JavaScript
<!-- end generated -->
