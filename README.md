![alt text](https://b2r2.org//images/b2r2-2d.png)

[![Build Status](https://travis-ci.com/B2R2-org/B2R2.svg?branch=master)](https://travis-ci.com/B2R2-org/B2R2)
![](https://img.shields.io/github/license/B2R2-org/B2R2.svg?style=flat)
[![](https://img.shields.io/nuget/vpre/B2R2.FrontEnd.svg?style=flat)](https://www.nuget.org/packages/B2R2.FrontEnd)

B2R2
====

B2R2 is a collection of useful algorithms, functions, and tools for **binary
analysis**, written purely in F# (in .NET lingo, it is purely managed code).
B2R2 has been named after [R2-D2](https://en.wikipedia.org/wiki/R2-D2), a famous
fictional robot appeared in the Star Wars. In fact, B2R2's original name was
*B2-R2*, but we decided to use the name *B2R2* instead, because .NET does not
allow dash (-) characters in identifiers (or namespaces). The name essentially
represents "binary" or "two": "binary" itself means "two" states anyways. "B"
and "2" mean "binary", and "R" indicates *reversing*.

B2R2?
-----

1. B2R2 is *analysis-friendly*: it is written in F#, which provides all the
   syntactic goodies for writing program analyzers, such as pattern matching,
   algebraic data types, and etc.

1. B2R2 is *fast*: it has a fast and efficient front-end engine for binary
   analysis, which is written purely in a functional way. Therefore, it
   naturally supports *pure parallelism* for binary disassembling, lifting and
   IR optimization.

1. B2R2 is *easy* to play with: there is absolutely no dependency hell for B2R2:
   All you need to do is to install [.NET Core
   SDK](https://dotnet.microsoft.com/download), and you are ready to go! Native
   [IntelliSense](https://docs.microsoft.com/en-us/visualstudio/ide/using-intellisense?view=vs-2017)
   support is another plus!

1. B2R2 is *OS-Independent*: it works on Linux, Mac, Windows, and etc. as long
   as .NET core supports it.

1. B2R2 is *interoperable*: it is not bound to a specific
   language. Theoretically, you can use B2R2 APIs with any [CLI supported
   languages](https://en.wikipedia.org/wiki/List_of_CLI_languages).

Features?
---------

Currently, our focus is on the front-end of binary analysis, which includes
binary parser, lifter, and optimizer. B2R2 natively supports parallel lifting,
which is a new technique we introduced in 2019 NDSS Bar. Please refer to our
[paper](#citation) for more details about the technique as well as our design
decisions. We also have our own back-end tools such as symbolic executor, but we
are *not* planning to open-source them yet. Nevertheless, B2R2 includes several
useful middle-end or back-end features such as ROP chain compilation, CFG
building, and automatic graph drawing, and etc. B2R2 also comes with a simple
command-line utility that we call [`BinExplorer`](src/Utilities/BinExplorer),
which can help explore such features using a simple command line interface.

Dependencies?
-------------

B2R2 itself does *not* rely on any external libraries. So, you really don't need
to install any other libraries in order to build B2R2! Just install .NET core,
and that's all.

API Documentation
-----------------

We currently use docfx to generate our documentation: https://b2r2.org/APIDoc/

Example
-------

Let's try to use B2R2 APIs.

1. First we create an empty directory `DIRNAME`:

    ```
    mkdir DIRNAME
    ```

1. We then create an empty console project with `dotnet` command line:

    ```
    $ dotnet new console -lang "F#"
    ```

1. Add our nuget package *B2R2.FrontEnd* to the project:

    ```
    $ dotnet add package B2R2.FrontEnd --version 0.1.0
    ```

1. Modify the `Program.fs` file with your favorite editor as follows:

    ```fsharp
    open B2R2
    open B2R2.FrontEnd

    [<EntryPoint>]
    let main argv =
      let isa = ISA.OfString "amd64"
      let bytes = [| 0x65uy; 0xffuy; 0x15uy; 0x10uy; 0x00uy; 0x00uy; 0x00uy |]
      let handler = BinHandler.Init (isa, bytes)
      let ins = BinHandler.ParseInstr handler 0UL
      ins.Translate handler.TranslationContext |> printfn "%A"
      0
    ```

1. We then just run it by typing: `dotnet run`. You will be able see lifted IR
   statements from your console. That's it! You just lifted an Intel instruction
   with only few lines of F# code!

Build
-----

Building B2R2 is fun and easy. All you need to do is to install [.NET Core SDK
2.0 or above](https://dotnet.microsoft.com/download/dotnet-core/2.2). Yea, that's it!

- To build B2R2 in release mode, type ```make release``` or ```dotnet build -c
  Release``` in the source root.

- To build B2R2 in debug mode, type ```make```, or ```dotnet build``` in the
  source root.

For your information, please visit the official web site of F# to get more tips
about installing the development environment for F#: http://fsharp.org/.

Why Reinventing the Wheel?
--------------------------

There are many other great tools available, but we wanted to build a
*functional-first* binary analysis platform that is painless to install and runs
on any platform without any hassle. B2R2 is in its *infancy* stage, but we
believe it provides a rich set of library functions for binary analysis. It also
has a strong front-end that is easily adaptable and extendible! Currently it
reliably supports x86 and x86-64, meaning that we have heavily tested them; and
it partially supports ARMv7 (and Thumb), ARMv8, MIPS32, and MIPS64, meaning that
they work, but we haven't tested them thorougly yet.


Features to be Added?
---------------------

Below is a list of features that we plan to add in the future: the list is
totally incomplete. Some of them are work in progress, but we look forward your
contributions! Feel free to write a PR (Pull Requst) while making sure that you
have read our [contribution guideline](CONTRIBUTING.md).

- Implement CFG recovery algorithms.
- Implement function boundary identification algorithms.
- Implement assembler for currently supported ISAs using a parser combinator.
- Support for floating point operations.
- Support for more architectures such as PPC.

Credits
-------

Members in [SoftSec Lab](https://softsec.kaist.ac.kr/). @ KAIST developed B2R2
in collaboration with [Cyber Security Research Center](http://csrc.kaist.ac.kr/)
(CSRC) at KAIST. See [Authors](AUTHORS.md) for the full list.

Citation
--------

If you plan to use B2R2 in your own research. Please consider citing our
[paper](https://FIXME):

```bibtex
@INPROCEEDINGS{jung:bar:2019,
  author = {Minkyu Jung and Soomin Kim and HyungSeok Han and Jaeseung Choi and Sang Kil Cha},
  title = {{B2R2}: Building an Efficient Front-End for Binary Analysis},
  booktitle = {Proceedings of the NDSS Workshop on Binary Analysis Research},
  year = 2019
}
```
