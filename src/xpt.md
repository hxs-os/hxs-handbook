# Package Management

**Programs & Packages**:

- `xpt(8)` from `xpt`

HxS provides a package-management suite named `xpt(8)`. It is designed as a relatively simple package manager built around `.xpt` packages, which are essentially zstd-compressed tarballs.

## 1. Basic Package Management

### 1.1 Install a Package File

Install a `.xpt` package using the following flags:

- `-i` (install)
- `-f <file>` (package file)

```sh
xpt -if hello-0.1.3.xpt
```

### 1.2 Uninstall a Package

Uninstall an installed package using the following flags:

- `-r` (remove)
- `-p <pkg>` (installed package)

```sh
xpt -rp hello
```

### 1.3 Query a Package

Find information about an installed package using the following flags:

- `-Q` (query)
- `-p <pkg>` (installed package)

```sh
xpt -Qp hello
```

### 1.4 Get a Package Version

Get the version string of an installed package using the following flags:

- `-g` (get version)
- `-p <pkg>` (installed package)

```sh
xpt -gp hello
```

### 1.5 Get Package Tree

Get a list of files installed by a package:

- `-t` (tree)
- `-p <pkg>` (installed package)

```sh
xpt -tp hello
```

## 2. Networking

> [!NOTE]
> `xpt.static` builds do not provide networking, view [Installation Guide / 4.3 The Package Manager](./installation.md#43-the-package-manager) on replacing `xpt.static` with `xpt`

### 2.1 The `/etc/xpt.d/index` File

This file is used to specify which remote repositories `xpt` must fetch to retrieve package databases.

**Example `/etc/xpt.d/index`**:

```
https://main.hxs.juanvel400.com/archive/26a3/x86_64
```

### 2.2 Synchronizing Repositories

Use `xpt -s` to refresh the package databases with the repository indexes:

```sh
xpt -s
```

### 2.3 Installing a Network Package

Install a network package using the following flags:

- `-i` (install)
- `-n <pkg>` (network package)

```sh
xpt -in hello
```

### 2.4 Querying a Network Package

Unlike `-Qp`, `-Qn` only shows the package name, version, and architecture.

```sh
xpt -Qn hello
```

> `-gn` is not supported for network packages

## 3. Extras

### 3.1 Custom Destinations

The `-d dir` flag allows packages to be installed in a different root.

It is used by `hxsbootstrap(1)` to bootstrap packages.

### 3.2 Quiet Output

Use the `-q` flag to suppress most informative output, errors are still shown.

### 3.3 `NO_COLOR`

`xpt(8)` is compliant with [`NO_COLOR`](https://no-color.org/).

Setting the `NO_COLOR` environment variable will suppress colorized output.
