# The HxS PackageTracker

**Programs & Packages**:

- `ebs(5)` and `ebs(1)` from `xpt`

The HxS PackageTracker is a growing tree of `ebs(5)` formulae, describing how to build binary packages using the `ebs(1)` build system.

PackageTracker is inspired by [`void-packages`](https://github.com/void-linux/void-packages), [the FreeBSD ports tree](https://github.com/freebsd/freebsd-ports), and, loosely, the [Arch User Repository](https://aur.archlinux.org).

> [!IMPORTANT]
> Most formulae assume the presence of a native compilation toolchain, which is not included in the binary repositories, the user is expected to bootstrap their own, using the `x86_64-hxs-linux-musl` triple.

> The PackageTracker Git Repository is available in GitHub as [hxs-os/hxs-packagetracker](https://github.com/hxs-os/hxs-packagetracker)

## 1. Getting the PackageTracker Tree

**Programs & Packages**:

- `git(1)` from `git`

> [!NOTE]
> Most HxS systems are expected to have a copy of PackageTracker available in `/usr/tracker`

As `root`, use `git(1)` to clone the PackageTracker tree into `/usr/tracker`:

```sh
git clone https://github.com/hxs-os/HxS-PackageTracker.git /usr/tracker
```

## 2. Working with PackageTracker

### 2.1 Search for a Formula

**Programs & Packages**:

- `find(1)` from `findutils`
- `grep(1)` from `grep`

Use `find(1)` and `grep(1)` to look up formulae in the tree:

```sh
find /usr/tracker -type f -name "*.ebs" | grep -F -e "<name>"
```

> [!TIP]
> The following shell script creates a wrapper around the command above, under a command named `qtracker`:
>
> ```sh
> cat << 'EOF' > /usr/local/bin/qtracker
> #!/bin/sh
> set -eu
> if test "$#" -ne 1; then
> 	echo "usage: qtracker query"
> 	exit 1
> fi
>
> results=$(find /usr/tracker -type f -name "*.ebs" | grep -F -e "$1" || :)
>
> if test -z "$results"; then
> 	echo "! nothing found"
> 	exit 1
> fi
>
> printf '%s\n' "$results"
>
> EOF
>
> chmod 755 /usr/local/bin/qtracker
> ```

### 2.2 Building a formula

**Programs & Packages**:

- `ebs(1)` from `xpt`

> [!TIP]
> Prefer not to build packages as the `root` user

Use the `ebs(1)` build system to build a formula:

```sh
ebs build /usr/tracker/hello/hello-0.1.3.ebs
```

If a second argument is not provided, `ebs(1)` produces an `.xpt` package under the filename `<name>-<version>.xpt`.

Install the built package using `xpt(8)`:

```sh
xpt -if hello-0.1.3.xpt
```

## 3. Writing `ebs(5)` formulae

**Programs & Packages**:

- `ebs(5)` from `xpt`

View the `ebs(5)` manual page and use `/usr/share/xpt/examples/ebs.formula` as a reference for building `ebs(5)` formula.
