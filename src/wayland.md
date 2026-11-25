# Using Wayland

**Programs & Packages**:

- `xpt(8)` from `xpt`

[Wayland](https://wayland.freedesktop.org) is a modern display server protocol, effectively replacing X11 in most modern UNIX systems.

## 1. Graphic Renderers

**Programs & Packages**:

- `pixman`

> [!NOTE]
> HxS deliberately excludes Mesa from its base repositories. While Mesa provides extensive graphics compatibility and hardware acceleration, its massive size and dependency footprint conflicts with HxS's unbeefy computers.

The `pixman` package provides a CPU-based pixel manipulation and compositing library. It can be used as a software rendering backend by compatible Wayland compositors, install with `xpt(8)`:

```sh
xpt -in pixman
```

> [!TIP]
> Use the `WLR_RENDERER=pixman` environment variable to specify `pixman` as the default Wayland renderer in most `wlroots`-based compositors.

## 2. Installing Wayland

**Programs & Packages**:

- `wayland`
- `wayland-protocols`

The `wayland` package provides the base Wayland protocol, install with `xpt(8)`:

```sh
xpt -in wayland
```

The `wayland-protocols` extends the base `wayland` package by including extra protocols, install `wayland-protocols` with `xpt(8)`:

```sh
xpt -in wayland-protocols
```

## 3. Compositors

**Programs & Packages**:

- `wlroots`

Several compositors use `wlroots` as their compositor library, install with `xpt(8)`:

```sh
xpt -in wlroots
```

### 3.1 `dwl`

**Programs & Packages**:

- `dwl`

A Wayland compositor inspired by [`dwm`](https://dwm.suckless.org), install with `xpt(8)`:

```sh
xpt -in dwl
```

> `dwl` is recommended to be built as a Persistent Build as outlined in [Using PackageTracker # 4. Persistent Builds](./packagetracker.md#4-persistent-builds) as most of its configuration is performed at build time.
> The `dwl` package provided by the binary repositories use all default build options.

### 3.2 `sway`

**Programs & Packages**:

- `sway`

An i3-compatible Wayland compositor, install with `xpt(8)`:

```sh
xpt -in sway
```

## 4. XDG Runtime Directories

HxS does not provide login managers, which handle the creation of XDG Runtime Directories.

The following snippet creates an XDG runtime directory, place it in a shell runtime file, such as `/etc/profile`:

```sh
if [ -z "$XDG_RUNTIME_DIR" ]; then
    XDG_RUNTIME_DIR="/run/user/$(id -u)"
    export XDG_RUNTIME_DIR

    if [ ! -d "$XDG_RUNTIME_DIR" ]; then
        mkdir -p -m 0700 "$XDG_RUNTIME_DIR"
    fi

    chmod 0700 "$XDG_RUNTIME_DIR"
fi
```
