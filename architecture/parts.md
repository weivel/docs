# The parts

weivel is four kinds of part, each in its own repository or repositories. A decision has exactly one owner, the core,
and every other part either supplies it with values, carries out its answers or checks that the others do.

## The core

**Owns** every decision of the platform: whether a plugin's manifest is compatible with the host, the order a set of
plugins activates in, which service or capability a plugin may reach and which provider answers it, whether a want for
a service is served, served degraded or refused, what is revoked when a plugin is released, quarantined or forgotten,
and the ledger of every plugin. It also owns the contract every language implementation follows, written once so a new
language translates it rather than copying another implementation.

**Does not own** anything that touches the world: loading plugins, reading manifests, calling services and logging
are its caller's. How the core and its caller talk without the core ever calling out is
[communication.md](communication.md), under language to core.

Its docs: [what the core is and is not](https://github.com/weivel/core/tree/HEAD/docs/README.md), and
[the contract a language implementation follows](https://github.com/weivel/core/tree/HEAD/docs/contract/README.md).

## The standard

**Owns** the defaults a host would otherwise write itself, as one C library a host may link or leave out: the JSON
manifest and its published schema ([manifests](https://github.com/weivel/standard/tree/HEAD/docs/manifests.md)), repository identities, how plugins lie on disk and are discovered, the library
declaration and the description of a definition,
[the config, secrets, paths, log, scope and host service definitions](https://github.com/weivel/standard/tree/HEAD/docs/services.md),
and the format of a mirror list a host can fetch the core's binary from, with the check of a download (see
[scope.md](scope.md), under providers).

**Does not own** any decision, and the core does not know it exists. It ships the ids and structs of its services and
no provider of them, and everything in it can be replaced.

Its docs: [what the standard is](https://github.com/weivel/standard/tree/HEAD/docs/README.md), and
[what replacing it means](https://github.com/weivel/standard/tree/HEAD/docs/replacing.md).

## The language implementations

**Own**, each for one language, what that language needs to host plugins written in it, as the modules every
implementation holds ([languages/structure.md](../languages/structure.md)). There is one for
[C](https://github.com/weivel/c/tree/HEAD/docs/README.md),
[Java](https://github.com/weivel/java/tree/HEAD/docs/README.md),
[Rust](https://github.com/weivel/rust/tree/HEAD/docs/README.md) and
[TypeScript](https://github.com/weivel/typescript/tree/HEAD/docs/README.md).

**Do not own** any decision: compatibility, order, reach, waiting and revocation are the core's answers, which they
carry out. None adds a format, file name or variable of its own; reading a manifest or discovering plugins goes
through the standard, in an optional module a host may leave out.

## The conformance kit

**Owns** the exam every other part sits: weivel's behaviour written as vectors (a given state, one operation and the
outcome any engine must produce), the runner that judges the core and the standard against theirs, the layer vectors
that each language's own tests must name, the drift checks every weivel repository runs, and every timing budget
weivel holds.

**Does not own** any part of weivel. It implements nothing and holds no engine: it builds the core and the standard
from pinned checkouts and asks them questions.

Its docs: [what the kit is](https://github.com/weivel/conformance/tree/HEAD/docs/README.md).

## How each pins the others

Every dependency is pinned by commit, in the repository's `repo.json`, and runs one way. The core depends on nothing.
The standard links the core. The kit builds the core and the standard at the commits it pins. Each language
implementation pins the core, the standard and the kit, and is judged by the kit at its pinned commit, so a language
pinned to an older core is held to the contract it adopted until it moves.
