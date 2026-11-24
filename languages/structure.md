# The structure every implementation shares

Every language implementation of weivel is built the same way, so a reader who knows one knows where everything is in
another, and a rule is written once for all of them rather than once per language.

## The modules

The core's `MODULES` file lists the modules a language implements, and
[the contract](https://github.com/weivel/core/tree/HEAD/docs/contract/README.md) says, one file per module, what each
must do and may not do. They stand on one another in one direction:

- `core` loads the core's binary and owns the one wrapper every call into it goes through, which drains the events
  each call queues and dispatches them;
- `handles` maps the core's integer handles to the language's own objects;
- `timer` arms the core's next deadline on the language's own timer and hands the core the time;
- `context` is what a plugin sees of the core, built on those three;
- `host` registers, activates and releases plugins written in the language and loads C libraries as host services.

Beside them stand `conformance`, the driver that runs the kit's layer vectors through the host, and `benchmarks`,
which times calls through weivel against the same calls made bare. Four modules are optional: `standard`, the
language's access to the standard's defaults, on which none of the five above depends, so a host may leave the
standard out; `program`, a host program; `bundle`, its packer; and `testing`, helpers for the tests of a program that
hosts plugins.

| language | holds | where the modules lie |
|---|---|---|
| C | the seven required and `standard` | `src/<module>`, headers under `include/weivel/c` |
| Java | the seven required and `standard` | one package each under `src/main/java/com/weivel`, and the Java 22 backend of `core` under `src/main/java22/com/weivel` |
| TypeScript | all eleven | `src/<module>`, each a subpath of the package |

Each language's own docs describe its modules:
[C](https://github.com/weivel/c/tree/HEAD/docs/README.md),
[Java](https://github.com/weivel/java/tree/HEAD/docs/modules/README.md) and
[TypeScript](https://github.com/weivel/typescript/tree/HEAD/docs/modules/README.md).

## What keeps them alike

The kit's drift checks and its budgets, each applied to a language repository at the kit commit it pins
([the drift checks](https://github.com/weivel/conformance/tree/HEAD/docs/checks.md)):

- `parity` refuses a missing required module, a directory that is no module, and a module that holds no file, reading
  the module list from the core the repository pins;
- `layers` refuses a language whose tests do not name every layer vector, the behaviour only a language can witness,
  except a directory the kit's language list exempts that language from by design
  ([the layer vectors](https://github.com/weivel/conformance/tree/HEAD/docs/vectors/layer/README.md));
- `purity` refuses a file of any other language in the repository;
- the language's `benchmarks` module holds its overhead to the bounds the kit keeps for it
  ([the budgets](https://github.com/weivel/conformance/tree/HEAD/docs/benchmarks.md)).

## Adding a language

A new language is a new repository, built in this order:

1. List the language in the kit's `checks/languages.txt` (its file extensions, its build files, its source and test
   roots, and any layer vector directory it is exempt from by design): the checks refuse a language the list does not
   name, so this comes before anything is checked.
2. Pin the core, the standard and the kit by commit in the repository's `repo.json`.
3. Write one top-level directory per module under the source root, translating the core's contract module by module
   rather than copying an existing implementation.
4. Write a test naming every layer vector, and add the language's timing bounds to the kit as
   `benchmarks/<language>.txt`, which the language's `benchmarks` module reads from the kit commit it pins.
5. Run `purity`, `names`, `parity` and `layers` in the repository's drift job, against the pinned kit commit.

How the language crosses into C is its own choice, and [reaching-c.md](reaching-c.md) shows the three made so far.
