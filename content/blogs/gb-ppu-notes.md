---
title: "Notes on Emulating the Game Boy PPU"
date: 2026-06-14
---
Writing the pixel pipeline was the part of the Game Boy emulator that took the longest to get cycle-accurate. This post walks through how the background FIFO, sprite fetcher, and mode-3 timing fit together, and the test ROMs that finally caught my off-by-one scanline bug.
