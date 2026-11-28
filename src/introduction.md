# Introduction

This is the HxS Handbook, documenting the HxS Operating System's functionality, configuration, and usage.

## 1. What is HxS?

HxS is a `musl`-based Linux distribution for x86_64 UEFI machines, built around its own collection of first-class packages, while additionally providing GNU and [suckless](https://suckless.org) packages for compatibility and reliability.

HxS is designed as a composable system via its [Set System](./installation.md#31-choosing-a-set), which lets you compose the system from sets and subsets.

The [HxS Compared to Other Operating Systems](./comparison.md) chapter compares HxS with other popular operating systems.

## 2. Philosophy

HxS emphasizes a philosophy built around correctness, minimalism, and simplicity.

The HxS philosophy is loosely inspired by suckless and traditional UNIX design.

## 3. First Steps

For most users, install HxS using the [Installation Guide](./installation.md), which outlines how to install HxS in most x86_64 UEFI machines.

The [Getting HxS](./getting-hxs.md) chapter describes various methods of obtaining a built HxS system, such as container images.

## 4. Release Model

HxS uses a rather unconventional release model similar to [Debian GNU/Linux](https://debian.org) and [Arch Linux](https://archlinux.org).

The `living` (`latest`) channel provides the latest versions of the HxS packages, following upstream closely. It is used to test and experiment with package releases, and is prone to breaking. Packages are not backported with security fixes; you get what upstream ships.

`living` is analogous to the Arch Linux Release Model, since both are bleeding-edge.

The `stable` channel is designed to follow the latest frozen release of HxS. This channel is far less prone to breaking, but not immune.

`stable` is loosely analogous to the Debian `stable` branch.

Previous versions of HxS are pinned. They may receive security updates and do not receive updated packages.

> View [Post-Install Recommendations / 2. The HxS Binary Repositories](./post-install.md#2-the-hxs-binary-repositories) for the respective repositories.

## 5. License

Copyright (c) 2026 juanvel400 [<me@juanvel400.com>](mailto:me@juanvel400.com).

Licensed under [CC-BY-4.0](https://spdx.org/licenses/CC-BY-4.0.html).

> Code snippets authored for this handbook are also available under [0BSD](https://spdx.org/licenses/0BSD.html).

```
welcome to HxS 26a3 'Roses'
mounting /dev
mounting /proc
mounting /sys
mounting /run
mounting /tmp
mounting /dev/pts
mounting /dev/shm
runlevel: 4

HxS 26a3 (x86_64)
hxs login: root
Password:
 _   _       ____    ____   __         _____
| | | |_  __/ ___|  |___ \ / /_   __ _|___ /
| |_| \ \/ /\___ \    __) | '_ \ / _` | |_ \
|  _  |>  <  ___) |  / __/| (_) | (_| |___) |
|_| |_/_/\_\|____/  |_____|\___/ \__,_|____/
No mail.
root@hxs:~#
```
