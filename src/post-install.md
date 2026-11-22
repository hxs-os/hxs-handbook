# Post-Install Recommendations

## 1. Using `doas(1)`

**Programs & Packages**:

- `doas(1)` from `opendoas`
- `groupadd(8)`, and `usermod(8)` from `shadow`
- `chmod(1)` from the `system:coreutils` provider (`coreutils`, `sbase`, `pxbase`)

The `doas(1)` utility allows regular users to obtain `root` privileges securely.

Create a `wheel` group using `groupadd(8)`:

```sh
groupadd wheel
```

Append a regular user to the `wheel` group using `usermod(8)`:

```sh
usermod -aG wheel johndoe
```

Append the following line to `/etc/doas.conf` to allow users of the group `wheel` to use `doas(1)`:

```
permit :wheel
```

Optionally, set the `/etc/doas.conf` permissions to `r--------` using `chmod(1)`:

```sh
chmod 0400 /etc/doas.conf
```

> `doas.conf(5)` provides a more detailed explanation of the `/etc/doas.conf` file

## 2. The HxS Binary Repositories

HxS provides several official binary package repositories:

- `https://main.hxs.juanvel400.com/x86_64`: Bleeding-edge packages, prone to breaking
- `https://main.hxs.juanvel400.com/archive/VERSION/x86_64`: A specific version repository
- `https://main.hxs.juanvel400.com/archive/latest/x86_64`: A link to the current living release
- `https://main.hxs.juanvel400.com/archive/stable/x86_64`: A link to the last frozen release

The last frozen HxS release is `26a2`, whereas the current ("living") HxS release is `26a3`.

> [!NOTE]
> The [Installation Guide](./installation.md) sets `https://main.hxs.juanvel400.com/archive/26a3/x86_64` as the main `xpt` repository, however, certain setups may benefit from being in the bleeding-edge with `https://main.hxs.juanvel400.com/x86_64`.

## 3. The HxS Build System

The HxS Build System allows users to compile packages from source without the overhead of manually unpacking source, setting configuration options and clashing with the package manager.

The HxS Build System uses build recipes named `ebs(5)` formulae to declare build procedures and package metadata, this recipe is then executed by the `ebs(1)` utility, which builds a `.xpt` package installable by `xpt(8)`.

View [PackageTracker](./packagetracker.md) for further information about the HxS PackageTracker tree.
