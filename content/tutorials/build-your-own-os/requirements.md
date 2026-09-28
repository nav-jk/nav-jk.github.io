---

title: "Requirements"
weight: 2


---

# What you should have

Before we start, there are a few things you should have ready.

First, **a Linux system is strongly recommended**. If you're on Windows, WSL should work just fine for most of what we're going to do. A lot of the compilers, assemblers, build tools and other tooling we'll be using work best on Linux, so using Linux will save you from a considerable amount of unnecessary suffering.

![alt text](/images/tutorials/build-your-own-os/local.png)

You should also have:

* **QEMU** — we'll use this to run the OS without needing to actually install it on a computer every time something inevitably breaks.

* **CMake** — for managing the build process as things start getting more complicated.

* **A code editor/IDE** — VS Code, Neovim, Vim, Emacs, or whatever you prefer. I'm not going to start an editor war here.

We'll install the other tools as we need them, so there's no point in trying to install every OS development tool known to mankind right now.

The exact setup may also vary a bit depending on whether you're using Linux or WSL. I'll be using Linux, so if something behaves differently on your system, we'll figure it out when we get there.

# Familiarity

You **don't need to be an OS expert** for this.

Having some familiarity with the following will definitely help, though:

* Computer architecture
* Operating systems
* C / C++
* Assembly
* Basic programming and data structures

If you already know these, great.

If you don't, that's also fine. You'll probably just have to pause occasionally, open another tab, read about something for 20 minutes, and then come back wondering why you now have 17 tabs open.

This isn't really meant to be a course on C, assembly or computer architecture. I'm learning a lot of this along the way myself, so I'll explain things when I understand them well enough to explain them, but there will definitely be parts where you'll have to do some additional reading.

And that's kind of the point.

There will be things that initially make absolutely no sense. Then, after staring at registers, memory addresses and hexadecimal numbers for long enough, they will *sort of* start making sense.

Or at least that's the plan.

So, if you have the tools ready and are willing to occasionally get confused along with me, that should be enough.

Let's get started.


![Engineering project meme](/images/tutorials/build-your-own-os/project.png)