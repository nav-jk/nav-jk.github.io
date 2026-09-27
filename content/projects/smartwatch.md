---
title: "Smartwatch Firmware"
weight: 4
repo: "https://github.com/nav-jk/smartwatch_firmware"
summary: "ESP-IDF firmware for a DIY smartwatch driving a GC9A01 240x240 round SPI display, with a from-scratch display driver."
tags: ["C", "ESP32", "Firmware"]
highlights:
  - "From-scratch GC9A01 SPI display driver — no vendor graphics library"
  - "Fast framebuffer flush routine tuned for a 240x240 round panel"
  - "Built on ESP-IDF directly against the ESP32 SPI and GPIO drivers"
---

## Overview

Firmware for a DIY smartwatch built around the GC9A01, a 240×240 round SPI display commonly used in wearables — driven with a display driver written directly against the datasheet rather than a pre-built graphics library.

## Display Driver

The GC9A01 datasheet is short on worked examples, so the init sequence, memory-access-control register configuration, and window-addressing commands were worked out largely through trial and error against a logic analyzer trace. The driver exposes a simple framebuffer that the rest of the firmware draws into.

## Performance

Because the panel is refreshed over SPI, flush speed dominates perceived responsiveness. The flush routine batches pixel writes into large SPI transactions and only redraws changed regions where possible, keeping full-screen updates well within a comfortable frame budget on the ESP32.

## Built on ESP-IDF

The firmware sits directly on ESP-IDF's SPI master and GPIO drivers rather than Arduino-style abstractions, which keeps timing predictable and makes it straightforward to reason about exactly what's happening on the wire.
