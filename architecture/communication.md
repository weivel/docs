# How the parts talk

Every path by which one part of a running weivel reaches another, what crosses it, and why it has that shape. The
rules each path keeps in full are in the docs it links to.

## Plugin to host

A plugin reaches the host, the core and every other plugin through its context and nothing else. The host builds the
context no later than it opens the plugin, hands it to the plugin's entry when it calls the entry's activate, and calls the
entry's optional deactivate when it releases the plugin. What the core reports about the plugin (a service revoked, a
refusal of reach, a want settled degraded) arrives as events the host dispatches to the plugin's listeners. A host
offers services of its own under `WEIVEL_HOST`, which a plugin resolves like any other service; the ones a host
commonly offers (config, secrets, paths and log, and a per-plugin scope of them) are defined by
[the standard's services](https://github.com/weivel/standard/tree/HEAD/docs/services.md). The context's rules
are [the contract's `context`](https://github.com/weivel/core/tree/HEAD/docs/contract/context.md), and the host's
[the contract's `host`](https://github.com/weivel/core/tree/HEAD/docs/contract/host.md).

## Plugin to plugin

Plugins never call each other except through a service or a capability. A plugin's manifest names the services and
capabilities it provides and consumes; the core decides what it may reach and which provider answers, and the plugin gets, wants or
watches a service, or lists a capability's implementations, through its context. What it receives is the provider's
own object, with no proxy, copy or wrapper, so calling it is a native call in the language that crosses nothing and
takes no lock. That is how weivel adds no cost to a call between plugins once it is resolved, and the benchmarks
hold it ([the cross-cutting rules](https://github.com/weivel/core/tree/HEAD/docs/contract/cross-cutting.md)). When the
provider stops, the core revokes the service and the consumer hears it as an event, so it stops calling; a want that
has not settled keeps waiting for another provider ([the contract's `context`](https://github.com/weivel/core/tree/HEAD/docs/contract/context.md),
under services and events).

## Language to core

A language implementation reaches the core through the core's one C header, and only through one wrapper of its own.
Structures go in and come back out; every structure starts with its size and only grows, so a layer built against an
older header keeps working against a newer core. The core never calls back: every change it makes lands in an event
queue, which the wrapper drains after each call and dispatches in the core's order, outside the core. Time is handed in
as a number, since the core owns no clock, opens no file, reads no environment and parses no format, and every plugin
object is an integer handle the layer maps to its own. Why the core never calls out is the core's
[docs](https://github.com/weivel/core/tree/HEAD/docs/README.md), and
[the conventions of the ABI](https://github.com/weivel/core/tree/HEAD/docs/abi.md) state what every call keeps. How
each language crosses into C is [languages/reaching-c.md](../languages/reaching-c.md).

## Language to a C library

A C library offers plugins its functions as services, and a host loads it as host services. The library declares
itself in a library declaration, which names its services and, for each function, one of two kinds: a bytes function,
which takes and answers opaque bytes, or a definition, a static struct of function pointers whose layout is the
library's own ([the library declaration](https://github.com/weivel/standard/tree/HEAD/docs/library.md)). A
definition is registered as the very pointer the library answered, so a plugin's call through it is a plain native
call into the library. A language that cannot read the library's C header binds the definition from its description,
data the library answers that lists every struct, member offset and operation, after the standard's check has
accepted it ([descriptions](https://github.com/weivel/standard/tree/HEAD/docs/descriptions.md)). No JSON crosses a C
boundary: data crosses as structures, bytes or the standard's value tree, and JSON enters C only through the
standard's readers. How each language binds a definition is [languages/reaching-c.md](../languages/reaching-c.md).

## A program to its provider

A host program asks a C library what to run through the standard's `weivel.host` service: the library's `start`
answers a plan (which libraries to load, which plugins to activate in which order, and whether to serve or run one
action), the program carries it out and tells the library what happened, and the library's `stop` answers the exit
code. The program decides nothing of its own and passes the clock in through `tick`
([the host service](https://github.com/weivel/standard/tree/HEAD/docs/services.md)). TypeScript is the language that
ships a host program ([its `program` module](https://github.com/weivel/typescript/tree/HEAD/docs/modules/program.md)).

## Process to process, as it is today

Everything above happens inside one process. The core is a library loaded into the host's process, every plugin and
C library the host loads runs there, and two cores share nothing. What crosses a process boundary is only how a host
program is told to end and how it answers: a stop request from its platform's terminate signals, and an exit code its
provider's `stop` decides ([the host service](https://github.com/weivel/standard/tree/HEAD/docs/services.md),
[TypeScript's program](https://github.com/weivel/typescript/tree/HEAD/docs/modules/program.md)).

The standard's service definitions are shaped so that a provider may live in another process: every operation
completes before it returns, and a provider in another process answers once the write has reached it
([the standard's conventions](https://github.com/weivel/standard/tree/HEAD/docs/conventions.md), under threads and
time).

## What is not built

- No channel between processes: a plugin reaches only services and capabilities registered in its own host's core,
  never one in another process, and no provider of a standard service that lives in another process exists.
- No host for a plugin written in another language than the host's: each language implementation hosts plugins of its
  own language, so plugins written in two languages share nothing but the C libraries each of their hosts loads.
