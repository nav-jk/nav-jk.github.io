---
title: "Game Boy Emulator"
weight: 1
repo: "https://github.com/nav-jk/gameboy"
summary: "A Game Boy (DMG) emulator written in C++, built from scratch to understand the CPU, memory system, graphics, and interrupts."
tags: ["C++", "Emulation"]
highlights:
  - "Full DMG CPU instruction set, including several undocumented opcodes"
  - "Cycle-stepped PPU with background, window, and sprite (OAM) rendering"
  - "Interrupt controller, timers, and MBC1/MBC3 memory bank controllers"
---

## Overview

A cycle-accurate Game Boy (DMG) emulator written in C++ from the ground up, mainly as an exercise in understanding how a real 8-bit console fits together — CPU, PPU, memory map, and interrupts — rather than relying on a high-level framework.

## CPU & Memory

The Sharp LR35902 core is implemented instruction-by-instruction against opcode tables, with correct flag behavior and cycle counts checked against known test ROMs (Blargg's `cpu_instrs`). Memory is mapped through swappable bank controllers so commercial ROMs larger than 32 KB load correctly.

## Graphics Pipeline

The PPU is stepped in lockstep with the CPU rather than rendered per-frame, so mid-scanline effects (raster tricks used by several commercial titles) render correctly. Background, window, and sprite layers are composited with proper priority and palette handling.

## What I Learned

Getting timing right — not just "does the game run" but "does it run at the exact cycle the hardware would" — turned out to be the hard 20%. Most of the debugging time went into matching test-ROM output byte-for-byte rather than writing new features.
