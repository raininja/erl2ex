# Erl2ex

[![Build Status](https://travis-ci.org/dazuma/erl2ex.svg?branch=master)](https://travis-ci.org/dazuma/erl2ex)

Erl2ex is an Erlang to Elixir transpiler, converting well-formed Erlang source to Elixir source with equivalent functionality.

The goal is to produce correct, functioning, but not necessarily perfectly idiomatic, Elixir code. This tool may be used as a starting point to port code from Erlang to Elixir, but manual cleanup will likely be desired.

This software is currently highly experimental and should be considered "pre-alpha". Some capabilities are not yet complete, and there are significant known issues, particularly in the Erlang preprocessor support. See the Caveats section for more information.

## Installing

Erl2ex may be run as a mix task or as a standalone escript.

### Requirements

Erl2ex recognizes Erlang source that is compatible with Erlang 18.x. The generated Elixir source requires Elixir 1.2 or later. The Erl2ex tool itself also requires Elixir 1.2 or later.

### Installing the mix task

To run the mix task, first add Erl2ex as a dependency to your existing Elixir project:

```elixir
def deps do
  [ {:erl2ex, ">= 0.0.7", only: :dev} ]
end
```

After adding Erl2ex as a dependency, run `mix deps.get` followed by `mix deps.compile` to install it. An `erl2ex` task will now be available. Run `mix help erl2ex` for help.

### Building the escript

To build a standalone command line application (escript), clone this repository using `git clone https://github.com/dazuma/erl2ex.git`, and then run `mix escript.build` within the cloned project.

## Usage

Erl2ex may be run in three modes:

*   It can read an Erlang source file on stdin, and write the generated Elixir source to stdout.
*   It can read a specified Erlang source file (.erl) from the file system, and write the generated Elixir source file (.ex) to the same or a different specified directory.
*   It can search a directory for all Erlang source files (.erl), and write corresponding Elixir source files (.ex) to the same or a different specified directory.

Various switches may also be provided on the command line, including the include path for searching for Erlang include files (.hrl), and other conversion settings.

For detailed help, use `mix help erl2ex` for the mix task, or `erl2ex --help` for the standalone tool.

## Caveats

This software is still under heavy development, and many capabilities are not yet complete. The following is a partial list of known issues and planned features.

### Known issues

*   Defining a macro that attempts to assign to a substitution results in an exception. e.g. `-define(A(X), X = 1).` should be legal. This may also run into problems because Elixir's macros are hygenic.
*   Returning a remote function reference from a macro is not supported: e.g. `-define(A, m:f).` generates illegal Elixir syntax.
*   Function macros cannot return function names; Erlang's parser rejects the syntax `?A()()`. In Erlang, the preprocessor fixes this, but we're not running the Erlang preprocessor directly.
*   Record names cannot be macro results; Erlang's parser rejects the syntax `-record(?MODULE, {...}).` and `#?MODULE{...}`. (Examples in https://github.com/soranoba/bbmustache/blob/master/src/bbmustache.erl)
*   Binary expressions with complex or combination size/type specs are not supported, and cause the converter to crash. An example is `<<1:16/integer-signed-native>>`. This seems to be a limitation of Elixir itself.
*   Elixir reserves the function name `__info__` and won't allow its definition. (Failure example in https://github.com/elixir-lang/elixir/blob/master/lib/elixir/src/elixir_bootstrap.erl).
*   Erlang allows variables for function name/arity in captures, whereas Elixir apparently doesn't. (Example in the `expand_macro_named/6` function in https://github.com/elixir-lang/elixir/blob/master/lib/elixir/src/elixir_dispatch.erl)
*   The Elixir compiler doesn't seem to like functions with too many clauses. (Example: https://github.com/benoitc/erlang-idna/blob/master/src/idna_unicode_data2.erl). Not sure if this is just an Elixir limitation.

### Incomplete features

*   Generate exdoc comments, probably based on heuristics on the funtion comments.
*   Do something reasonable with inline comments.
*   Provide an option to elixirize module names (e.g. instead of generating `defmodule :my_erlang_module`, generate `defmodule MyErlangModule`)
*   Provide an option to convert variable names from camelCase to snake_case.
*   Provide (possibly optional) translation of include files (.hrl) to separate modules rather than copying into the including module, so the declarations can be shared after translation to Elixir.
*   Correct the usage of leading underscores in variable names.
*   Closer integration with EUnit. For example: perform auto-exporting of tests, or strip test code depending on settings. Needed to get JSX tests working (https://github.com/talentdeficit/jsx/blob/develop/src/jsx.erl)

## Contributing

While we appreciate contributions, please note that this software is currently highly experimental, and the code is evolving very rapidly. It is recommended to contact the author before embarking on a major pull request. More detailed contribution guidelines will be provided when the software stabilizes further.

The source can be found on Github at [https://github.com/dazuma/erl2ex](https://github.com/dazuma/erl2ex)

## License

Copyright 2015 Daniel Azuma

This software is licensed under the 3-clause BSD license.

See the LICENSE.md file for more information.
