# What weivel never forces

weivel serves any project that hosts plugins, so it takes every choice a project could reasonably make differently
as a value from its caller and keeps every default it offers in one replaceable place, the standard. This file says
what that leaves to its users and what holds each line.

## Names

The core reads no file, environment variable or path. A language implementation loads the core's binary from the path
its caller gives, loads a plugin's code by a name its caller gives, and never assumes a language's module convention
as a default. A path to a binary inside the language's own package, as the TypeScript package carries the core's and
the standard's, is allowed, since it forces nothing on a caller who passes another. The names that weivel's defaults
do use, such as the name of a plugin's manifest file and how plugins lie on disk, are the standard's
([layout](https://github.com/weivel/standard/tree/HEAD/docs/layout.md)). Nor does a weivel repository name a project that
uses weivel or a vendor it would tie itself to: the kit's `names` check refuses such a word in every one
([the drift checks](https://github.com/weivel/conformance/tree/HEAD/docs/checks.md)).

## Formats

The core takes a manifest as a C structure and reads no format. The JSON manifest and its schema are the standard's,
and a host that wants another format, another layout or no files at all builds the core's structures itself and never
links the standard; it owes the core nothing else. What a replacement still owes is the published ids and layouts of
the services, which only ever grow ([replacing the standard](https://github.com/weivel/standard/tree/HEAD/docs/replacing.md)).

## Languages

No language is favoured. Every decision is made once, in C, so a plugin in any language meets the same decisions, and
the kit holds every language implementation to one body of vectors. A C library ships C and the descriptions of its
definitions, never a binding for one language, and each language binds a definition from its description. Each
repository holds only its own language, which the kit's `purity` check holds, and each language implementation holds
the same modules, which its `parity` check holds ([languages/structure.md](../languages/structure.md)).

## Providers

No provider is required. The standard ships the ids and structs of its services and no provider of any of them, so
every provider, the host's own included, is one a project chooses. The core's binary is addressed by its content and
fetched from a mirror list that is the user's own file: the standard ships no list, no mirror is required and none is
privileged, and a download is checked against the hash that was asked for
([mirrors](https://github.com/weivel/standard/tree/HEAD/docs/mirrors.md)).

## What holds it

That the core reaches nothing of the world is held by the kit's core gate, which reads the core's built binary and its
preprocessed header for anything that could read the world or be called back. The `names`, `purity` and `parity`
checks hold the lines above where a program can see them; what no program can see, such as whether a default belongs
in the standard rather than in a language, is held by review.
