+++
title = "Etterna"
summary = "A fork of Stepmania which I contribute towards."
tags = ["Open Source", "Contributor"]
date = "2019-03-11"

showComments = false
showAuthor = false
showDate = false
showPagination = false
showReadingTime = false
showWordCount = false
layoutBackgroundHeaderSpace = false
heroStyle = 'big'
+++
{{< lead >}}
phalangeography platform for moonlight vigil
{{</ lead >}}

{{< github repo="etternagame/etterna" >}}

I started contributing to this project around the start of 2019 when I was short on personal programming projects, so began searching for a project I could contribute to. Growing up, I used to play a lot of Dance Dance Revolution at my arcade before they closed. I don't get the chance to play as much today, so instead, I try to make time to work on this game so it can still be enjoyed. It's not so easy with life going on, but I try to come back.


## Major Contributions
- [Pull Request #445](https://github.com/etternagame/etterna/pull/455) - Organized source files, Removed unused files, Moved libraries
    - Before this PR, all source files were in the same `src/` directory.
- [Pull Request #481](https://github.com/etternagame/etterna/pull/481) - Completely rewrite of CMake build system
    - The original CMake build system was a mess - Difficult to modify, and difficult to understand.
- [Pull Request #780](https://github.com/etternagame/etterna/pull/780) - Improved CI with GitHub Actions
    - This one is semi-major, because it wasn't a huge change, but it made out developer lives a lot easier, and I'm proud of it.
- [Pull Request #882](https://github.com/etternagame/etterna/pull/882) - 💥 Improve Crash Handling
    - This one is major because finding out why our program crashed was a pain point (we only had log files). With this, we have stacktraces on every thread, and we can see the exact line of code that caused the crash.


## All Contributions
- [Pull Request #458](https://github.com/etternagame/etterna/pull/458) - Fix segfault on linux caused by image writing
- [Pull Request #468](https://github.com/etternagame/etterna/pull/468) - Remove unused CMake Variables
- [Pull Request #478](https://github.com/etternagame/etterna/pull/478) - Fix macOS Build issues caused by [PR#445](https://github.com/etternagame/etterna/pull/455)
- [Pull Request #513](https://github.com/etternagame/etterna/pull/513) - Update root README.md
- [Pull Request #514](https://github.com/etternagame/etterna/pull/514) - CMake Spring Cleaning
- [Pull Request #520](https://github.com/etternagame/etterna/pull/520) - Remove redudant header text from README.md.
- [Pull Request #529](https://github.com/etternagame/etterna/pull/529) - Prevent windows-only MinaCalc (internal lib).
- [Pull Request #532](https://github.com/etternagame/etterna/pull/532) - Change install directory
- [Pull Request #553](https://github.com/etternagame/etterna/pull/553) - Upgrade to C++17
- [Pull Request #557](https://github.com/etternagame/etterna/pull/557) - Update CPack (installer generator)
- [Pull Request #559](https://github.com/etternagame/etterna/pull/559) - Fix Linux build issues caused by CMake updates [PR#557](https://github.com/etternagame/etterna/pull/557)
- [Pull Request #561](https://github.com/etternagame/etterna/pull/561) - Change CMake order of operations to fix build issues on Linux
- [Pull Request #565](https://github.com/etternagame/etterna/pull/565) - Update Building.md to include images for CMake GUI
- [Pull Request #680](https://github.com/etternagame/etterna/pull/680) - Update macOS dependencies and build instructions
- [Pull Request #686](https://github.com/etternagame/etterna/pull/686) - Fix macOS catalina crashing
- [Pull Request #728](https://github.com/etternagame/etterna/pull/686) - Update curl to 7.69.1
- [Pull Request #767](https://github.com/etternagame/etterna/pull/767) - 📝 Improve Logging System
- [Pull Request #768](https://github.com/etternagame/etterna/pull/768) - Improve CMake System (delayed from [PR#481](https://github.com/etternagame/etterna/pull/481))
- [Pull Request #783](https://github.com/etternagame/etterna/pull/783) - Completely move to Github Actions
- [Pull Request #786](https://github.com/etternagame/etterna/pull/786) - Fix Github Action for Coverity Scan
- [Pull Request #824](https://github.com/etternagame/etterna/pull/824) - Remove libssh2 requirement
- [Pull Request #911](https://github.com/etternagame/etterna/pull/911) - 🧾 Better AppInfo - Embed git hash, version number, etc
- [Pull Request #917](https://github.com/etternagame/etterna/pull/917) - 🛠 Improving Arch (Platform) Specific Code
- [Pull Request #1001](https://github.com/etternagame/etterna/pull/1001) - 🔑 macOS Auto-Untranslocation
- [Pull Request #1010](https://github.com/etternagame/etterna/pull/1010) - 📚 Update LuaJIT
- [Pull Request #1016](https://github.com/etternagame/etterna/pull/1016) - 🧾 Better Issue Reports 
- [Pull Request #1026](https://github.com/etternagame/etterna/pull/1026) - 🔨 Update macOS Build and CI
- [Pull Request #1053](https://github.com/etternagame/etterna/pull/1053) - 💥 Updating Crashpad and Symbol Upload
- [Pull Request #1061](https://github.com/etternagame/etterna/pull/1061) - 💡 Update Crashpad and Linux Update
- [Pull Request #1071](https://github.com/etternagame/etterna/pull/1071) - ⌛ C++20 Upgrade 



## Closed/Rejected Contributions

Here are some PR's where the standard was not met, or the scope was too large.

- [Pull Request #466](https://github.com/etternagame/etterna/pull/466)
    - First attempt at rewriting CMake build system.
    - Rejected for scope being too large.
- [Pull Request #530](https://github.com/etternagame/etterna/pull/530) - Remove `foreach.h`.
    - Rejected as not all replacements were correct.
- [Pull Request #543](https://github.com/etternagame/etterna/pull/543)
    - Remove instances of `using namespace <lib>;`.
    - Rejected for requiring too many additional changes.
- [Pull Request #567](https://github.com/etternagame/etterna/pull/567)
    - Remove instances of `using <anything>;`.
    - Rejected for changing game functionality.
- [Pull Request #758](https://github.com/etternagame/etterna/pull/758)
    - Update files in Visual Studio, NetworkSyncManager to use `/bigobj`.
    - Rejected for improve solution in different PR.
- [Pull Request #884](https://github.com/etternagame/etterna/pull/884)
    - 📖 Better Documentation.
    - Rejcted because, I thought I could do better.
- [Pull Request #921](https://github.com/etternagame/etterna/pull/921) 
    - ⏲️ Updated Timer Implementation.
    - Reason for closure not documented. I believe it was too large in scope.
- [Pull Request #921](https://github.com/etternagame/etterna/pull/921)
    - 🖥️ Improved Window System & 🧹 Minor Organization.
    - Rejected because I knew I could do better.
- [Pull Request #1013](https://github.com/etternagame/etterna/pull/1013)
    - 🖥️ Improved Window System & Input.
    - Rejected for affecting input timing.
