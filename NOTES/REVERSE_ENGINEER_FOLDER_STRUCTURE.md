Little Kernel (LK) Folder Structure

This document summarizes the reverse-engineered folder structure of the Little Kernel (LK). It explains the purpose of each directory and how components interact, especially in the context of architecture, platform, and build systems.


---

High-Level Structure Overview

LK/
│
├── .github/         # CI/CD pipeline scripts (e.g., GitHub Actions YAML files)
├── app/             # Applications that run after system initialization
├── arch/            # CPU architecture-specific code (e.g., ARM64, ARM-M, x86)
├── build/           # Output directory for compiled files (e.g., .elf, .map)
├── dev/             # Device drivers
├── docs/            # Documentation
├── external/        # External dependencies (e.g., CMSIS for ARM-M)
├── include/         # Header files exposing interfaces (architecture-agnostic)
├── kernel/          # Core OS primitives: mutexes, schedulers, memory management
├── lib/             # Generic, standalone libraries (e.g., circular buffer)
├── make/            # Build system scripts and rules
├── platform/        # SoC-level code for different vendors (e.g., STM, RPi)
├── project/         # Board/project-specific makefiles
├── scripts/         # Utility scripts (e.g., QEMU, debug setup)
├── target/          # Board-specific configurations
├── tools/           # Host utilities used during the build or flashing process
└── top-level files  # main.c, init functions, top-level makefiles


---

Directory Descriptions

.github/

Contains CI/CD-related scripts (usually GitHub Actions YAMLs).


app/

Contains the actual applications that run after system initialization.

app_init() is used to initialize all apps one-by-one.

Operates with unprivileged access.


arch/

Architecture-specific code (e.g., arm, arm64, x86, mips, riscv).

Each folder under arch/ implements common interfaces with architecture-specific details.

Example: spinlock.h implemented differently for arm-m (NVIC) vs arm64 (GIC).

Function names remain consistent, but implementations differ.

Includes assembly and low-level code required for boot and interrupt handling.

These are tightly coupled with the CPU architecture.


build/

Stores build artifacts: object files, .elf, .map, etc.

Typically auto-generated.


dev/

Device drivers such as UART, GPIO, timers, etc.


docs/

Contains design notes, usage guides, and other documentation.


external/

External libraries or components used by LK.

Example: CMSIS for ARM-M-based microcontrollers.


include/

Contains header files and interface declarations.

Implementation is resolved based on architecture or platform at compile time.


kernel/

Core operating system components:

Threading

Mutexes, semaphores

Scheduling

Memory management (MMU/SMU)


Low-level primitives and system management.


lib/

Minimal, generic libraries like:

Circular buffer

Page tables

Dynamic memory allocators


Standalone and reusable across apps and drivers.


make/

Contains build logic and scripts.

Controls the hierarchy and rules for which files get compiled.

Handles conditional inclusion based on selected arch, platform, target, and project.


platform/

SoC-specific implementations.

Example folders: stm, bcm, nxp, altera.

Each platform may contain init code, CPU config, board support packages.

SoCs are built around a specific CPU architecture, and this folder maps architecture-specific code to specific hardware implementations.


project/

Contains makefiles for specific boards/projects.

Defines which target, platform, and apps to include.

Example: rpi2, stm32f4_eval.


scripts/

Debugging scripts (e.g., Lauterbach .cmm, QEMU launchers).

Useful during simulation or hardware flashing.


tools/

Host-side utilities and tools used during build or deployment.


target/

Board-specific configurations.

Defines how a particular board maps to platform and architecture.



---

Version Info

Version metadata tracks the following:

arch

platform

target

project

build


Note: These can be from different versions, and it's not necessary that all components use the same version.


---

Hierarchical Flow

Conceptual View

arch ─▶ platform ─▶ target ─▶ project

Makefile Build Dependency View

project
 ├──▶ target
      ├──▶ platform
           └──▶ arch

Additionally:

project ─▶ app, lib, dev, kernel, etc.


---

Top-Level Files

Files like main.c, init.c, and global makefiles reside at the top level.

Handle early initialization before control is handed over to app/.



---

Summary

The build system is Makefile-based, which handles conditional inclusion of files based on the selected hierarchy.

arch, platform, target, and project together define the configuration and composition of the LK build.

Generic functionality is placed in lib/, kernel/, and include/, while specific implementations are placed in arch/, platform/, and target/.


