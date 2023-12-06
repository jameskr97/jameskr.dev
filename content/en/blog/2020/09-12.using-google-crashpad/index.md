---
title: Using Google Crashpad
date: '2020-09-12'
slug: using-google-crashpad
tags:
    - Devlog
---

One of the projects I've been working on over the past year is called [Etterna]. It's a fork of a gamed called [StepMania], which is originally based on the two decade old [Dance Dance Revolution] game.

In my time working on this game, I primarily focus on rewriting components of the game. I started with the CMake build system, then the logging system, and I'm currently working on the crash handler. Currently, Etterna had a custom crash handler, which has remained untouched since the most recent functionality commit in 2010. I prioritize what I believe to be the most critical components when choosing what to rewrite, and considering how difficult it is get get proper crash reports (and that getting them doesn't need to be *this* hard), I began rewriting it.

I decided using a library would be the best route, as the idea of having to maintain three versions
of platform-specific crash handling code has shown itself to require too much maintenance. I also
thought using a hosted solution would not be ideal. The benefits of using a service like [Sentry.io]
or [BugSplat] would be outweighed by adding reliance on a third party, of which the game currently
has none.

Here are the requirements I came up with when looking for a crash handler:

1. It must be cross-platform.
2. It should have a low maintenance requirement.
3. Easy to implement into the current CMake build system.

In my search for a cross-platform library, I was not able to find a large number of options. The most popular options I found was Google's [Breakpad], and it's successor, [Crashpad]. While they seemed to satisfy my first two requirements, Crashpad is uses Google's own `gn` build system. I did find some Github repos which implement crashpad in CMake, but the maintenance burden seemed too high incase the Crashpad build script received changes. So I got started in interfacing CMake with the `gn` build system, specifically to build Crashpad.

## How Crashpad Works

Crashpad primarily functions as an out-of-process crash reporting client. When your application launches, it will setup configuration settings, followed by launching a watchdog daemon with those settings. For the lifetime of your application, the daemon will wait in the background for a fatal exception to be thrown, a null pointer to be dereferenced, or for any program halting event to occur. Once crashed, crashpad will collect information about the state of the program when it crashed, and place it in a minidump. This has enough information for a developer to be able to debug what was happening at the time of the crash, provided the developer stored symbols for their application before releasing.

Symbols are the names of variables, functions, and their address locations in memory. Usually the minidump file will only present the function addresses in the minidump, and they have to converted into their function/variable names. This process is called Symbolization, and once a minidump file has been symbolized, there is sufficient information to be able to debug what happened. 

## Compiling Crashpad

Crashpad uses [depot_tools] to manage the dependencies for crashpad, chromium, and various other
google projects. After adding `depot_tools` to my system path, then running `fetch crashpad`, it
will fetch everything needed to compile crashpad *for your current platform*, and assuming your
compiler is available in your path (`MSBuild`, `clang`, `g++`), it should compile without issues.

```bash
$ fetch crashpad            # Get the source code
$ cd crashpad/              # Go into the source directory     
$ gn gen out/BuildDir       # Generate build files
$ ninja -C out/BuildDir     # Build the program!
```

## Integrating Crashpad

I want the addition of crashpad to be transparent to the other developers. Some developer primarily work on higher level engine systems, and I don't want them to have to do anything special in order to compile with crashpad. When I rewrote the build system, I did my best to make sure anybody with a CPU and a compiler could build the game, without knowing anything about specific libraries. Keeping the build system seamless is important for simple usage for all developers.

I started by adding the source code into the build tree. We don't rely on any remote dependencies, except for ones we host ourselves. Every third-party dependency is added into the source tree inside `extern/`. Crashpad is treated the same, check in at `extern/crashpad/`. Here is how I added the source code. 

```bash
$ mkdir extern/crashpad 
$ cd extern/crashpad && fetch crashpad    # Get the source code
$ rm -rf crashpad/.git/ buildtools/.git/  # Remove .git folders
$ git add -A  # Assuming extern/crashpad is the only change
$ git commit -m "Add crashpad to extern/"
```

Removing the `.git` repositories is important, otherwise Git will interpret those folders as submodules. Crashpad will also have a `crashpad/crashpad/third_party` folder which contains *platform-dependant* submodules. Sadly, dealing with these platform-dependant crashpad dependencies is not as easy. While I wanted to have all platform able to be compiled with the one `crashpad` folder, there was no easy way to download all dependencies with a single `fetch crashpad` command (or at least none that I could find). So I added everything within `crashpad/crashpad/third_party`, being sure to remove any sub-folder with a `.git` folder. I completed this `third_party` process on macOS, Windows, and Linux, pushing to a private branch, and amending my commits each time to keep the library addition consolidated to one commit. As I go from platform to platform, I also modify my `CMake` to `gn` interface build script to make sure adding compatibility with the next platform, doesn't break compatibility on the previous platform. I called the file [SetupCrashpad.cmake].

While CMake allows using `target_link_libraries` directly with `ExternalProject` commands, I prefer to have CMake run the commands with `ExternalProject`, but then create the library used for linking with an `add_library(crashpad INTERFACE)` statement. The `add_library` command has more options for what commands can be used with them, and I have more ways to modify the library I end up linking with. Here is what my add_library interface looks like. 

```cmake
# Create library which will be linked to main application
add_library(crashpad INTERFACE)
add_dependencies(crashpad crashpad_init)
target_link_libraries(crashpad INTERFACE ${OUTPUT_LIBS})
target_include_directories(crashpad INTERFACE ${SOURCE_DIR})
target_include_directories(crashpad INTERFACE ${SOURCE_DIR}/third_party/mini_chromium/mini_chromium)
```

The `add_dependencies` line lets CMake know that `crashpad_init` must be built before the `crashpad` target is "built." It doesn't really get built, but I believe it sets a priority order for when building starts.

At this point, crashpad successfully compiles on all platforms through the CMake script, but it's
not integrated with the game yet. With all the prior setup, all that is necessary to include it is
to write `target_link_libraries(Etterna PRIVATE crashpad)`, and I then have access to all required
crashpad headers, and I'm able to much more easily follow the [BugSplat build guide]. After
following that guide, I've completed only half what is necessary to start using crashpad for crash
reports...

## Symbol Storage

A necessary step for generating the postmortem stack traces, is having a copy of the debug symbols
generated from the same binary which was distributed to your users. From this point on, I'm not
longer building `Release`, but `RelWithDebInfo`. We get the same optimizations for a release build,
but we still generate the debug information so we can extract debug symbols.

We have a few different formats for to store symbols. The [Sentry symbolicator blog] and the [Sentry
debug info file docs], were good resources into learning about the formats in a single place. There
were 5 standardized ways to organize symbols, including Microsoft Symbol Server and File Mapped UUID
Directories. I won't go into detail on all the formats, but with my focus on crashpad, and the
cross-platform application I am integrating with, the Breakpad Directory Layout is how I intended on
storing my symbols.

Breakpad Directory Layout is as follows
```txt
# <DebugName>/<BreakpadID>/<SymName>
SymbolsDirectory/
    MyApp/
        34FE609E917E4FFDACD24728366A7A172/MyApp.sym
        CFCF5D1231D9431FB3C4BD8583935D204/MyApp.sym
    MyOtherApp/
        B3057E8C1CDE4482A5BF4A794F624AEF2/MyApp.sym
```

I changed the continuous integration setup to automatically generate symbols and upload them to an
Amazon S3 for future use. It allows me to have automatic uploads from the Github Actions CI, and for
public access to be available for the symbols, should a released version of the game need to be
debugged.

[Etterna]: https://github.com/etternagame/etterna
[StepMania]: https://www.stepmania.com/
[Dance Dance Revolution]: https://en.wikipedia.org/wiki/Dance_Dance_Revolution
[Sentry.io]: https://sentry.io/
[BugSplat]: https://www.bugsplat.com/
[Breakpad]: https://chromium.googlesource.com/breakpad/breakpad/
[Crashpad]: https://chromium.googlesource.com/crashpad/crashpad/
[depot_tools]: https://dev.chromium.org/developers/how-tos/depottools
[SetupCrashpad.cmake]: https://gist.github.com/jameskr97/8c40d927db05fe253235e05333fed4f3
[BugSplat build guide]: https://www.bugsplat.com/docs/sdk/crashpad/building/
[Sentry symbolicator blog]: https://blog.sentry.io/2019/06/13/building-a-sentry-symbolicator
[Sentry debug info file docs]: https://docs.sentry.io/data-management/debug-files/
