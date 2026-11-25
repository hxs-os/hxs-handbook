# System Initialization & Service Management

## 1. Init

**Programs & Packages**:

- `adam(8)` from `adam`

The `adam` program is a `sysvinit`-style init system designed solely to initialize the system, execute processes and supervise them.

The `adam` package is included in most sets as `adam` or `adam.static`, except in the `suckless` sets, where it is replaced by `sinit`.

### 1.1 The Init Table

`adam` uses a `sysvinit`-style `/etc/inittab` file.

The [Installation Guide # 4.5 The `/etc/inittab` File](./installation.md#45-the-etcinittab-file) copies the example inittab file provided by `adam` to `/etc/inittab`. The following `/etc/inittab` presents a more correct base for most HxS systems:

```inittab
# id:runlevels:action:command

# default runlevel
id:3:initdefault:

# rescue shell
su:S:respawn:/bin/sh

# run fsck(8)
fs:123456S:sysinit:/usr/sbin/fsck -a

# mount fstab(5)
ma:123456S:sysinit:/usr/sbin/mount -a

# run ttys
tty1:235:respawn:/usr/sbin/agetty -J tty1 linux
tty2:235:respawn:/usr/sbin/agetty -J tty2 linux
tty3:235:respawn:/usr/sbin/agetty -J tty3 linux
tty4:235:respawn:/usr/sbin/agetty -J tty4 linux
tty5:235:respawn:/usr/sbin/agetty -J tty5 linux
tty6:235:respawn:/usr/sbin/agetty -J tty6 linux
```

Each `/etc/inittab` entry represents a process, and when and how to run it.

The aforementioned example sets a default runlevel, runs filesystem checks, mounts filesystems described in `/etc/fstab`, and executes TTYs.

### 1.2 The `poweroff(8)` utility

**Programs & Packages**:

- `poweroff(8)` from `adam`

The `poweroff(8)` utility functions as a multicall-style dispatcher.

When called under the `reboot` filename, `poweroff(8)` will reboot the machine by sending a `SIGINT` signal to `adam(8)`.

When called under the `poweroff` filename, `poweroff(8)` will shut down the machine by sending a `SIGTERM` signal to `adam(8)`.

When called under the `halt` filename, `poweroff(8)` will halt the machine by sending a `SIGUSR1` signal to `adam(8)`.

The installed `halt(8)` and `reboot(8)` programs are symbolic links to the `poweroff(8)` program.

## 2. Service Management

**Programs & Packages**:

- `openrc(8)` from `openrc`

`adam` does not handle service management but merely initialization and basic supervision, the `openrc` package provides a dependency-based service management system.

### 2.1 Integrating with `adam`

The following `/etc/inittab` entries execute `openrc(8)` at separate runlevels for system initialization, boot, and shutdown:

```inittab
si::sysinit:/usr/sbin/openrc sysinit
rc::wait:/usr/sbin/openrc boot
de:2345:wait:/usr/sbin/openrc default
sn:S:wait:/usr/sbin/openrc single
rb::reboot:/usr/sbin/openrc reboot
st::shutdown:/usr/sbin/openrc shutdown
```

### 2.2 Services

`openrc` uses shell-scripts specified in `/etc/init.d` or `/usr/local/etc/init.d`.

#### 2.2.1 Enabling / Disabling Services

**Programs & Packages**:

- `rc-update(8)` from `openrc`
- `svc:openrc:dhcpcd` from `dhcpcd`

The `rc-update(8)` utility adds a service to a specified runlevel, or `default` if none.

e.g. To add the `dhcpcd` service from `dhcpcd`:

```sh
rc-update add dhcpcd
```

e.g. To remove the `dhcpcd` service:

```sh
rc-update delete dhcpcd
```

#### 2.2.2 Starting Services

**Programs & Packages**:

- `rc-service(8)` from `openrc`
- `svc:openrc:dhcpcd` from `dhcpcd`

The `rc-service(8)` utility executes a service at runtime, regardless of whether it's enabled or not:

e.g. To start the `dhcpcd` service:

```sh
rc-service dhcpcd start
```

e.g. To stop the `dhcpcd` service:

```sh
rc-service dhcpcd stop
```

e.g. To view the status of the `dhcpcd` service:

```sh
rc-service dhcpcd status
```

e.g. To restart the `dhcpcd` service:

```sh
rc-service dhcpcd restart
```
