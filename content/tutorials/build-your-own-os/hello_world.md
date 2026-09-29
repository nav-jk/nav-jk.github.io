---
title: "hello world"
weight: 3
---

# Starting

Okay, now we can actually start building something.

For this project, I'll be dealing with the old-school **BIOS + bootloader** path rather than EFI/UEFI. This makes things considerably simpler to understand at the beginning, and we're going to be working very close to the hardware anyway.

When a BIOS-based x86 machine boots from a disk, the BIOS loads the first sector of the boot device into memory at **physical address `0x7C00`** and then starts executing it.

One small but important correction here: the CPU doesn't start in protected mode. It starts in **16-bit real mode**. Protected mode comes later, when we decide we're ready to make things more complicated.

Which, unfortunately, we will.

The first thing I want to do is simply get the machine to print:

**Hello World!**

It's not exactly a groundbreaking operating system yet, but it is a good way to make sure the whole chain from BIOS → bootloader → CPU → our code is actually working.

## The Makefile

We will also need a `Makefile`. At this point there are only a couple of files, so manually running NASM isn't particularly painful. But as the project grows, having to remember a bunch of commands every time we change something gets annoying very quickly.

So I'll let `make` handle the repetitive stuff.

```makefile
ASM = nasm

SRC_DIR = src
BUILD_DIR = build

$(BUILD_DIR)/main_floppy.img: $(BUILD_DIR)/main.bin
	cp $(BUILD_DIR)/main.bin $(BUILD_DIR)/main_floppy.img
	truncate -s 1440k $(BUILD_DIR)/main_floppy.img

$(BUILD_DIR)/main.bin: $(SRC_DIR)/main.asm
	$(ASM) $(SRC_DIR)/main.asm -f bin -o $(BUILD_DIR)/main.bin
```

The idea here is fairly simple.

NASM takes our assembly file and produces a raw binary:

```text
main.asm → main.bin
```

We then copy that binary into a floppy disk image and resize the image to **1440 KiB**, which is the size of a standard 1.44 MB floppy disk.

Why a floppy disk?

Because we're pretending it's 1987.

More importantly, the BIOS boot process we're using expects a bootable disk image, and the floppy format keeps things nice and simple while we're figuring out what's actually happening.

## The bootloader

Now for the part that actually gets executed.

```asm
org 0x7C00
bits 16

%define ENDL 0x0D, 0x0A

start:
    jmp main

puts:
    push si
    push ax

.loop:
    lodsb
    or al, al
    jz .done

    mov ah, 0x0e
    int 0x10

    jmp .loop

.done:
    pop ax
    pop si
    ret

main:
    mov ax, 0
    mov ds, ax
    mov es, ax
    mov ss, ax
    mov sp, 0x7C00

    mov si, msg_hello
    call puts

    hlt

.halt:
    jmp .halt

msg_hello: db 'Hello World!', ENDL, 0

times 510 - ($ - $$) db 0

dw 0xAA55
```

There is quite a lot happening here for something that ultimately just prints a string.

The first two lines tell NASM how we're assembling the file:

```asm
org 0x7C00
bits 16
```

`org 0x7C00` tells NASM that the code is going to be loaded at address `0x7C00`. `bits 16` tells NASM that we're generating 16-bit code, which is what we need because the CPU starts in real mode.

We then jump to `main`:

```asm
start:
    jmp main
```

Nothing particularly exciting yet.

## Printing something

The `puts` function is where we actually print our string.

```asm
puts:
    push si
    push ax
```

We're going to modify `SI` and `AX`, so we save them first. This is useful because we don't want our little printing function randomly destroying registers that the rest of the program might care about.

Then:

```asm
.loop:
    lodsb
```

`lodsb` loads one byte from the address stored in `SI` into `AL` and advances `SI`.

The string is terminated with a zero byte, so we check for that:

```asm
or al, al
jz .done
```

If `AL` is zero, we've reached the end of the string.

Otherwise, we use a BIOS interrupt to print the character:

```asm
mov ah, 0x0e
int 0x10
```

This is one of the nice things about using BIOS at this stage. We don't have to write a display driver just to print a character. We can ask the BIOS to do it for us.

`AH = 0x0E` selects the BIOS teletype output function, and the character to print is stored in `AL`.

Then we go back around the loop and print the next character.

Eventually we hit the terminating zero and return:

```asm
.done:
    pop ax
    pop si
    ret
```

## Setting things up

Now we get to `main`:

```asm
main:
    mov ax, 0
    mov ds, ax
    mov es, ax
    mov ss, ax
    mov sp, 0x7C00
```

We're setting up the segment registers and stack.

At this point we're still in 16-bit real mode, so segmentation is part of how memory addresses are handled. I'm not going to go too deep into segmentation here because that rabbit hole gets surprisingly deep, surprisingly quickly.

The important thing for now is that we're setting the data, extra and stack segments to zero and putting the stack pointer at `0x7C00`.

Then:

```asm
mov si, msg_hello
call puts
```

We put the address of our message into `SI` and call the function we just wrote.

Finally:

```asm
hlt

.halt:
    jmp .halt
```

Once the message is printed, there's nothing else for us to do yet.

So we halt the CPU and then sit in an infinite loop.

Very sophisticated operating system architecture.

## The boot signature

There is one last part that is particularly important:

```asm
times 510 - ($ - $$) db 0
dw 0xAA55
```

A BIOS boot sector is **512 bytes** long.

The last two bytes must contain the boot signature:

```text
0xAA55
```

So we fill the space between our code and the final two bytes with zeroes:

```asm
times 510 - ($ - $$) db 0
```

and then put the signature at the end:

```asm
dw 0xAA55
```

This gives us:

```text
┌───────────────────────────────┐
│                               │
│       Our bootloader          │
│                               │
│                               │
├───────────────────────────────┤
│       Padding                 │
├───────────────────────────────┤
│       0xAA55                  │
└───────────────────────────────┘
             512 bytes
```

The BIOS sees that signature and knows that this sector is intended to be bootable.

And that's basically our first tiny piece of an OS.

At this point, all we're doing is getting the CPU to execute some assembly and convince the BIOS to print a string. But this is the first point where the code we're writing is running directly on the machine rather than inside another operating system.

You can run the image you created in your vm by using this
```bash
qemu-system-i386 -fda build/main_floppy.img
```

![First Boot](/images/tutorials/build-your-own-os/first_boot.png)