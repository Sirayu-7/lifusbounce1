# lifusbounce

`lifusbounce` is a Windows C++20 external overlay and research project for Rainbow Six Siege. It provides a Direct3D 11 overlay, game-session reading, ESP-style rendering, aim assistance, operator/weapon metadata, skeleton drawing, diagnostics, and a companion kernel driver.

This repository is a fork of [lifusdev/lifusbounce](https://github.com/lifusdev/lifusbounce).

> **Warning:** This project contains kernel-mode code and DSE-bypass-related behavior. It can modify system security state, load unsigned drivers, and trigger anti-cheat or Windows protections. Use it only in an isolated lab or on systems you own for authorized research. Do not use it to cheat in multiplayer games or in violation of a game's terms of service.

## Features

- Direct3D 11 / DirectComposition layered overlay
- ImGui configuration panel
- Player username, operator, health, weapon, and defuser indicators
- 2D boxes and head/skeleton overlays
- Configurable aim-assist settings and FOV display
- Team checks and keybind controls
- Operator badge and localized font resources
- Kernel memory, process, region, and thread enumeration support
- Zydis-based instruction decoding and emulation
- Diagnostic archives and retention management
- Custom kernel driver with a user-mode loader

## Requirements

- Windows 10 or later, x64
- Visual Studio with the C++ desktop workload
- MSVC toolset (the project currently targets `v145`)
- Windows SDK 10
- Windows Driver Kit (WDK), for building the kernel driver
- Administrator privileges, where applicable
- Hypervisor-protected Code Integrity (HVCI) must be disabled for unsigned kernel-driver loading

## Repository layout

```text
src/
├── core/       Driver loading, memory primitives, diagnostics, disassembly, and emulation
├── sdk/        Process/session reading and game feature logic
└── ui/         Direct3D, ImGui, overlay, menu, resources, and rendering code

driver/
├── src/        Kernel driver implementation
├── include/    Shared driver interfaces
├── loader/     Standalone vulnerable-driver loader utility
└── resources/  INF and resource files

resources/      Application resources, fonts, and operator images
third_party/    ImGui, miniz, Zydis, and Zycore
```

## Building

Open `lifus.slnx` in Visual Studio and build the `lifus` project in **x64 / Release** configuration.

The main application is emitted under:

```text
out\release\lifus.exe
```

To build the kernel driver and loader, run `driver\build.cmd` from an elevated Command Prompt with Visual Studio and WDK environment discovery available.

```bat
cd /d driver
build.cmd
```

Build output is written to:

```text
bin\driver\lifus.sys
bin\loader\loader.exe
```

## Controls

| Key | Action |
|---|---|
| Insert | Toggle the overlay menu |
| Right mouse button | Activate aim-assist when enabled |
| End | Exit the application |

Controls and feature settings can be adjusted in the in-app menu.

## Diagnostics

The application initializes a diagnostic subsystem at startup. Diagnostic logs and crash artifacts can be opened from the application's Debug tab.

## Third-party components

- [Dear ImGui](https://github.com/ocornut/imgui)
- [Zydis](https://github.com/zyantific/zydis)
- [Zycore](https://github.com/zyantific/zycore-c)
- [miniz](https://github.com/richgel999/miniz)

License notices for bundled third-party code are available in `third_party/notices.txt` and in each dependency directory.

## License

No license file is currently included in this repository. All rights remain with the respective contributors unless a license is explicitly added.
