# HxS Compared to Other Operating Systems

This chapter attempts to draw a comparison between HxS and other popular operating systems.

The descriptions and summaries are designed to be objective and merely based on facts.

## 1. Linux Distributions

### 1.1 Arch Linux

- [Arch Linux](https://archlinux.org), as defined by its homepage and recurring slogan, is a GNU/Linux distribution that tries to Keep It Simple by providing the user with a minimal base system for them to build upon
- Similarly, HxS provides a minimal base system that can be adjusted to the user's needs with the flexible Set System
- Arch Linux uses a rolling-release system, whereas HxS uses a channel-based release model designed to suit each user's needs
- Arch Linux uses the `pacman` package manager, a mature, battle-tested package-management utility, whereas HxS uses `xpt`, an experimental package-management suite
- Arch Linux uses `systemd` as the init system, whereas HxS uses the much smaller `adam`

### 1.2 Debian GNU/Linux

- [Debian GNU/Linux](https://debian.org) is a GNU/Linux built around free software, community governance, and universality. HxS, in contrast, is a Linux distribution built around minimalism, correctness, and simplicity
- Debian uses `dpkg`, a well-established package manager, and `apt`, a high-level interface over `dpkg` providing complete dependency resolution and repository access, whereas HxS uses the aforementioned `xpt` package-management suite
- Debian is available for various machine architectures, such as `i386`, `x86_64` (`amd64`), `aarch64` (`arm64`), `riscv64`, and various others, whereas HxS only supports `x86_64`
- The Debian binary repositories contain tens of thousands of packages, whereas the HxS binary repositories contain a much more modest amount, with approximately 80 packages
- Debian does not offer a source-based repository, whereas HxS offers the first-party [HxS PackageTracker](.packagetracker.md)
- Debian packages are generally fragmented in various packages, such as `libfoo`, `libfoo-common`, `libfoo-dev`, `libfoo-doc`, `lib32-libfoo`, `libfoo-tools`, `libfoo-dbgsym`, etc. Whereas HxS bundles all of the package's contents in a single `libfoo` package

### 1.3 Linux From Scratch

- [Linux From Scratch](https://linuxfromscratch.org) (LFS) is a series of books guiding users through building a complete GNU/Linux system from source
- LFS is built entirely from source, which allows the user to configure the system to their will, but also becomes a tedious task after a couple hundred packages, unlike HxS, which provides binary packages configured to various systems and configurations
