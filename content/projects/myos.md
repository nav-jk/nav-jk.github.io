---
title: "myos"
weight: 3
repo: "https://github.com/nav-jk/myos"
summary: "A tiny x86 OS built from a 512-byte boot sector up — real mode to protected mode, GDT setup, and a path toward a real kernel."
tags: ["Assembly", "C", "OS Dev"]
highlights:
  - "Hand-written 512-byte boot sector loaded by real BIOS INT 13h calls"
  - "Second-stage loader that enables A20 and builds a GDT by hand"
  - "Far jump into 32-bit protected mode with a minimal C kernel entry"
---

## Overview

A from-scratch x86 operating system, starting at the very first instruction the CPU executes after power-on: a 512-byte boot sector that fits in a single disk sector and ends with the mandatory `0xAA55` signature.

## Boot Sector to Second Stage

The boot sector itself only has room to load a larger second-stage loader from disk via BIOS `INT 13h`, since 512 bytes isn't enough to do much else. The second stage is where the real setup work happens.

## Entering Protected Mode

The second stage enables the A20 line, builds a Global Descriptor Table by hand, sets the protection-enable bit in `CR0`, and performs a far jump to flush the pipeline into 32-bit protected mode — the classic real-mode-to-protected-mode dance, done without any library support.

## Toward a Kernel

Once in protected mode, control is handed to a minimal C entry point, laying the groundwork for a real kernel — a starting point for paging, a proper memory allocator, and eventually a scheduler.
