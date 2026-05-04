# C++17 SFML Network Ping Pong

A C++17 network Ping Pong game developed with SFML.

## Project Overview

**Type:** SFML Game Development
**Build System:** CMake
**Language:** C++17
**Target Platform:** UCRT64 on Windows, GCC on Unix/macOS
**Dependencies:** sfml, gtest

---

## Project Structure

```
sfml-pingpong.git/
├── src/
│   ├── game/               # sources for main game logic (executable)
│   ├── graphic/            # sources for graphic module (dynamic library)
│   └── network/            # sources for network module (dynamic library)
├── include/
│   ├── game/               # headers for game logic
│   ├── graphic/            # headers for graphic module public API
│   └── network/            # headers for network module public API
├── test/
│   ├── game/               # gtest unit tests for game module
│   ├── graphic/            # gtest unit tests for graphic module
│   └── network/            # gtest unit tests for network module
├── build/
│   ├── ucrt64/             # Windows build output (generated — do not commit)
│   └── gcc/                # Unix/macOS build output (generated — do not commit)
├── CMakeLists.txt          # root build definition, adds all modules via add_subdirectory()
├── CMakePresets.json       # build presets for ucrt64 (Windows) and gcc (Unix/macOS)
├── .clang-format           # C++ code formatting rules applied via clang-format
├── .claudeignore           # files and folders hidden from Claude
├── CLAUDE.md               # project rules, architecture, and style — read by Claude every session
├── MEMORY.md               # current context, decisions, and where work stopped
├── TASKS.md                # task backlog — in progress, up next, done
├── PLAN.md                 # current feature plan — replaced per feature
├── CHANGELOG.md            # permanent record of what shipped — append only
└── .claude/
    ├── settings.json       # Claude permissions (allow/deny rules)
    ├── commands/           # slash command definitions (/start, /stop, /coder, etc.)
    └── archives/           # completed task snapshots (memory, tasks, plan)
        └── YYYYMMDD/       # one folder per archived date
```

---

## Architecture

### Modules

| Module  | Type            | Output             | Links against    |
|---------|-----------------|--------------------|------------------|
| game    | Executable      | netPingPong[.exe]  | network, graphic |
| network | Dynamic library | network.dll / .so  | —                |
| graphic | Dynamic library | graphic.dll / .so  | —                |

### Rules

- Each module is self-contained — no cross-includes between network and graphic
- game is the only target allowed to include from network/ and graphic/
- Public API for each library lives in include/<module>/ only
- Private implementation details stay in src/<module>/ and are never exposed
- No circular dependencies between modules

### CMake Target Names

- game    → target: Game
- network → target: NetworkLib
- graphic → target: GraphicLib

---

## Build & Run

Each module has its own CMakeLists.txt.
Root CMakeLists.txt adds all three via add_subdirectory().

### Target Structure

- src/network/CMakeLists.txt → add_library(NetworkLib SHARED ...)
- src/graphic/CMakeLists.txt → add_library(GraphicLib SHARED ...)
- src/game/CMakeLists.txt    → add_executable(Game ...) + target_link_libraries(Game PRIVATE NetworkLib GraphicLib)

### Linking Rule

game links network and graphic as PRIVATE — they are implementation details, not part of game's public API.

### Windows (ucrt64)

Requires MSYS2_UCRT64 environment variable set to your ucrt64 root (e.g. C:/msys64/ucrt64).

```bash
# Configure — generates build files into build/ucrt64
cmake --preset ucrt64 -DBUILD_TESTS=ON

# Build — compiles app and tests in parallel
cmake --build build/ucrt64 --parallel

# Run app
./build/ucrt64/netPingPong.exe

# Run tests — direct executable
./build/ucrt64/tests.exe

# Run tests — via ctest (shows pass/fail summary)
ctest --test-dir build/ucrt64
```

### Unix / macOS (gcc)

```bash
# Configure — generates build files into build/gcc
cmake --preset gcc -DBUILD_TESTS=ON

# Build — compiles app and tests in parallel
cmake --build build/gcc --parallel

# Run app
./build/gcc/netPingPong

# Run tests — direct executable
./build/gcc/tests

# Run tests — via ctest (shows pass/fail summary)
ctest --test-dir build/gcc
```

---

## Code Style

### Naming

| Construct        | Convention  | Example          | Note                                         |
|------------------|-------------|------------------|----------------------------------------------|
| Functions        | camelCase   | parseInput()     |                                              |
| Classes          | PascalCase  | InputParser      |                                              |
| Member variables | camelCase   | playerSpeed      | declared inside class body                   |
| Constants        | UPPER_SNAKE | MAX_SUGGESTIONS  |                                              |
| Local variables  | snake_case  | token_list       | declared inside function body only           |
| Files            | PascalCase  | InputParser.cpp  |                                              |

### Header Template

```cpp
#ifndef aiteam_netPingPong_MYCLASS_H
#define aiteam_netPingPong_MYCLASS_H

#include <string>
#include <string_view>

namespace aiteam {

class MyClass {
public:
    [[nodiscard]] std::string getValue() const;
    void printValue(std::string_view sv);

private:
    std::string playerSpeed;   // member variable — camelCase
};

} // namespace aiteam

#endif // aiteam_netPingPong_MYCLASS_H
```

### Source Template

```cpp
#include "MyClass.h"

#include <iostream>

namespace aiteam {


std::string MyClass::getValue() const
{
    std::string cached_value = playerSpeed;   // local variable — snake_case
    return cached_value;
}


void MyClass::printValue(std::string_view sv)
{
    std::cout << sv << std::endl;
}


} // namespace aiteam
```

### Rules

- Use `#ifndef` header guards — never `#pragma once`
- Guard format: `aiteam_netPingPong_CLASSNAME_H`
- `[[nodiscard]]` on every non-void return
- `std::string_view` for read-only string parameters
- No `using namespace` in any header file
- Use only `namespace aiteam` — no other namespaces
- Always two blank lines between function implementations in source files
- One class per header/source pair

---

## Testing

**Framework:** Google Test (gtest)
**Location:** test/, one file per class mirroring the src/ structure
**Naming:** `TEST(ClassName, whatItDoes)`
**Coverage:** every public method needs at least one passing and one failing/edge case

```cpp
#include <gtest/gtest.h>
#include "InputParser.h"

TEST(InputParser, emptyInputReturnsEmptyVector)
{
    aiteam::InputParser parser;
    EXPECT_TRUE(parser.parseInput("").empty());
}
```

---

## Code Formatting

**Tool:** clang-format (`.clang-format` in project root)

```bash
# Format all C++ files
find src include test -name "*.cpp" -o -name "*.h" | xargs clang-format -i
```

---

## CMake Style

- Commands in lowercase: `add_executable()` not `ADD_EXECUTABLE()`
- Keywords in uppercase: `PRIVATE`, `PUBLIC`, `INTERFACE`, `TARGET`
- 4-space indentation
- One argument per line when a command has more than 3 arguments
- Closing parenthesis aligned with the command name
- Always two blank lines between top-level blocks

```cmake
add_executable(
    netPingPong
    src/game/main.cpp
    src/game/Game.cpp
)


target_include_directories(netPingPong
    PRIVATE
        include
)


target_compile_options(netPingPong
    PRIVATE
        -Wall
        -Wextra
        -Wpedantic
)
```

---

## Git Rules

- Every task gets its own branch before any code is written
- Branch naming: `task/[id]-[short-description]`
  - Example: `task/001-add-player-movement`
  - Example: `task/002-fix-network-disconnect`
- Never work directly on main or dev
- Commit message format: `type: short description`
  - Types: feature / fix / bugfix / refactor / test / docs / chore
  - Example: `feature: add player movement`
  - Example: `bugfix: fix network disconnect on timeout`
- Never commit automatically — only when explicitly told or via /commiter
- Never push, merge, rebase, or switch branches — human only

---

## Do Not

- Do not place network or graphic headers inside src/game/
- Do not link network against graphic or vice versa
- Do not use GLOB to collect sources — list every .cpp file explicitly
- Do not make network or graphic STATIC — they must be SHARED (dynamic)
- Do not use `#pragma once` — use `#ifndef` header guards only
- Do not use any namespace other than `aiteam`
- Do not use `using namespace` in any header file
- Do not add a duplicate row for local variables in the naming table
- Do not place test files outside test/
