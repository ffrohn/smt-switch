# SMT-Switch
A generic C++ API for SMT solving. It provides abstract classes which can be implemented by different SMT solvers.

# Architecture Overview

There are four abstract classes:
* `AbsSmtSolver`
* `AbsSort`
* `AbsFun`
* `AbsTerm`

Each of them has a `using` statement that names a smart pointer of that type, e.g. `using Term = shared_ptr<AbsTerm>;`. They all use `shared_ptr`s except `SmtSolver` which is a `unique_ptr` to an `AbsSmtSolver`. The key thing to remember when using this library is that all solver-specific objects are pointers to the abstract base class. `SmtSolver`, `Sort`, `Fun`, and `Term` are all smart pointers. Note: there are many convenience functions which operate on these pointers, so they may not *look* like pointers. Additionally, the library also includes `using` statements for commonly used data structures, for example, `TermVec` is a vector of shared pointers to `AbsTerm`s.

The function names are based on SMT-LIB. The general rule is that functions/methods in this library can be obtained syntactically from SMT-LIB commands by replacing dashes with underscores. There are a few exceptions, for example `assert` is `assert_formula` in this library to avoid clashing with the `assert` macro. Operator names are also based on SMT-LIB operators, and can be obtained syntactically from an SMT-LIB operator by capitalizing the first letter and any letter after an underscore. The only exception is `bv` which is always capitalized to `BV` and does not count towards the capitalization of the first letter. Some examples include:

* `And`
* `BVAnd`
* `Zero_Extend`
* `BVAshr`

Please see the `tests` directory for some example usage.

# Solvers
To setup and install different solvers, first run the `./contrib/setup-<solver>.sh` script which builds position-independent static libraries in the `<solver>` directory. Then you can run `sudo make install-<solver>` to install that solver. This creates a shared object file named `libsmt-switch-<solver>.so` which is put in `/usr/local/lib` by default. Note that you will also need to build and install the regular `smt-switch` library with `sudo make install` to use the API.

## Custom Solver Location
If you'd like to try your own version of a solver, you can use the `configure.sh` script to point to your custom location with `--<solver>-home`. You will need to build static libraries (.a) and have them be accessible in the standard location for that solver. For example, you would point to a custom location of CVC4 like so:
`./configure.sh --prefix=<your desired install location> --cvc4-home ./custom-cvc4`

where `./custom-cvc4/build/src/libcvc4.a` and `./custom-cvc4/build/src/parser/libcvc4parser.a` already exist. `build` is the default build directory for `CVC4`, and thus that's where the `smt-switch` compiler looks.

# Building Tests
To get started, please try building the tests included in `tests/<solver>`. This does not require a global installation of `smt-switch`. Here is a short walkthrough for building the tests for solver `<solver>`:
* Run `./contrib/setup-<solver>.sh`.
* [optional] Run `./configure.sh --prefix=<non-standard-location>`. This supports relative paths. The default is `/usr/local`.
* Run `make install install-<solver>`. This installs `smt-switch` and the `smt-switch` implementation for `<solver>`.
* Run `make tests-<solver>`.
  * This builds the tests in `tests/<solver>` with `*.out` extensions.
