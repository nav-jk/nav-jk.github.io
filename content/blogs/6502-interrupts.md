---
title: "Getting NMI/IRQ Timing Right on the 6502"
date: 2026-03-02
---
The Klaus Dormann test suite doesn't check interrupt timing, so my 6502 core passed the functional tests while still being subtly wrong about when interrupts could fire relative to instruction boundaries. Here's how I found and fixed it.
