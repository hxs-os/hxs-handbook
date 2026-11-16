# Installation Guide for HxS 26a3

This guide details how to install HxS 26a3 on an x86_64 UEFI machine.

The Live Installer Image is based on HxS 26a3 GNU, extended with the `efi` subset.

## 1. Configure the Live System

### 1.1 Network Configuration

**Programs & Packages**:

- `dhcpcd(8)` from `dhcpcd`
- `ip(8)` from `iproute2`
- `ping(1)` from `inetutils`

The HxS Live System assumes an active internet connection; currently Wi-Fi is not supported.

> [!TIP]
> If your network uses it, execute the DHCP client with `dhcpcd(8)`:
>
> ```sh
> dhcpcd eth0 # look up the appropriate network device with `ip link`
> ```

The `/etc/resolv.conf` file lists DNS servers used to resolve domain names. The following command sets `1.1.1.1` as the default nameserver, with `8.8.8.8` as a fallback:

```sh
echo "nameserver 1.1.1.1" >/etc/resolv.conf
echo "nameserver 8.8.8.8" >>/etc/resolv.conf
```

Finally, use `ping(1)` to test the network connection.

```sh
ping -c 3 main.hxs.juanvel400.com
```

### 1.2 XPT

**Programs & Packages**:

- `xpt(8)` from `xpt`

The Live System does not provide any `xpt(8)` repositories in `/etc/xpt.d/index`. Write the `26a3` main repository (`https://main.hxs.juanvel400.com/archive/26a3/x86_64`) to it:

```sh
echo "https://main.hxs.juanvel400.com/archive/26a3/x86_64" >/etc/xpt.d/index
```

Synchronize the `xpt(8)` package database:

```sh
xpt -s
```

## 2. Partitioning

**Programs & Packages**:

- `lsblk(8)`, `mount(8)` and `cfdisk(8)` from `util-linux`
- `mkfs.ext4(8)` from `e2fsprogs`
- `mkfs.fat(8)` from `dosfstools`
- `mkfhs(1)` from `xpt`

Use `lsblk(8)` to list block devices (disks and partitions):

```sh
lsblk
```

> The guide assumes `/dev/sda` as the target disk

Use `cfdisk(8)` to create a partition scheme for the system.

> [!TIP]
> The following example serves as a reference partition scheme for most HxS systems
>
> | Partition   | Size                                          | Type                   | Filesystem | Mountpoint  |
> | ----------- | --------------------------------------------- | ---------------------- | ---------- | ----------- |
> | `/dev/sda1` | `512MiB` to `1GiB`                            | `EFI System Partition` | `FAT32`    | `/boot/efi` |
> | `/dev/sda2` | At least `1GiB`, preferably more than `10GiB` | `Linux root (x86_64)`  | `ext4`     | `/`         |
> | `/dev/sda3` | Usually `2GiB`                                | `Linux swap`           | `swap`     | `<none>`    |
>
> UEFI systems usually use `GPT` (GUID Partition Table). Ensure the disk's partition table is set to `GPT`

> [!WARNING]
> Utilities such as `mkfs.ext4(8)` or `mkfs.fat(8)` wipe entire partitions. Make sure the partition is safe to erase.

Format the root partition using `mkfs.ext4(8)`:

```sh
mkfs.ext4 /dev/sda2
```

> [!NOTE]
> On a fresh disk, format the `ESP`. If the disk has other Operating Systems, skip this.
>
> To format the `ESP`, use `mkfs.fat(8)`:
>
> ```sh
> mkfs.fat -F 32 /dev/sda1
> ```

Mount root on `/mnt` using `mount(8)`:

```sh
mount /dev/sda2 /mnt
```

Create the base system structure using `mkfhs(1)`:

```sh
mkfhs /mnt
```

Then, mount the `ESP` using `mount(8)`:

```sh
mount --mkdir /dev/sda1 /mnt/boot/efi
```

### 2.1 The Swap Partition

**Programs & Packages**:

- `fallocate(1)`, `mkswap(8)` and `swapon(8)` from `util-linux`
- `chmod(1)` from the `system:coreutils` provider (`sbase`, `coreutils`, `pxbase`)

Most systems may benefit from a swap partition, used to extend physical RAM by moving inactive memory pages to it.

To format a partition as `swap` using `mkswap(8)`:

```sh
mkswap /dev/sda3
```

Alternatively, a file can be created and used as a swap partition without repartitioning the disk.

Allocate `2GiB` to `/usr/swap0` with `fallocate(1)`, set the permissions to `rw-------` with `chmod(1)` and format it as `swap` with `mkswap(8)`:

```sh
fallocate -l 2GiB /mnt/usr/swap0
chmod 600 /mnt/usr/swap0
mkswap /mnt/usr/swap0
```

To enable swap using `swapon(8)`:

```sh
swapon /dev/sda3 # or /mnt/usr/swap0
```

## 3. Bootstrapping HxS

### 3.1 Choosing a set

HxS provides several sets, predefined collections of packages that compose a base system, each suited to different use cases.

#### 3.1.1 The Standard Set (`standard`)

This set provides first-class HxS packages, including `pxbase`, a core user utilities implementation, and `adam`, a sysvinit-compatible init system.

This set is experimental, as the projects it depends on are still experimental and prone to breaking.

#### 3.1.2 The GNU Set (`gnu`)

This set is built on battle-tested GNU packages, including `coreutils`, the GNU file, text, and shell utilities, and `bash`, the GNU Bourne-Again Shell.

This set is preferred for setups that require stability, compatibility, or prefer a complete, assembled system.

#### 3.1.3 The Suckless Set (`suckless`)

This set uses packages from the [`suckless`](https://suckless.org/) Project, which strives for quality and minimalism. The packages include: `sbase`, a collection of portable unix tools, `sinit`, a suckless init, and `ubase`, a collection of unportable tools (alternative to `util-linux`).

> HxS does not provide `/etc/rc.init`, `/etc/rc.poweroff`, or `/etc/rc.reboot`. The user is expected to provide those.

#### 3.1.4 The Minimal Set (`minimal`)

The Minimal Set is a trimmed-down version of [Standard](installation.md#311-the-standard-set-standard), using statically-linked builds of `pxbase`, `adam`, and `xpt`, and featuring a reduced base package set.

This set is designed for minimal containers, embedded systems, or rescue environments where size is a major priority.

#### 3.1.5 The Suckless-Minimal Set (`suckless-minimal`)

This set is a trimmed-down version of the [Suckless](installation.md#313-the-suckless-set-suckless) Set. Similarly to the [Minimal](installation.md#314-the-minimal-set-minimal) Set, the Suckless-Minimal set uses statically-linked builds of `sbase`, `ubase`, `sinit`, and `xpt`.

### 3.2 Subsets

Unlike sets, subsets are designed to expand a base installation by providing packages that complement a specific feature or necessity.

#### 3.2.1 `efi`

The `efi` subset includes packages to boot or administer UEFI-based systems.

Packages included by this subset are: `popt`, `efivar`, `efibootmgr`, and `grub-efi`.

### 3.3 Using `hxsbootstrap(1)` to Bootstrap a Base System

**Programs & Packages**:

- `hxsbootstrap(1)` from `hxsbootstrap`

Use `hxsbootstrap(1)` to bootstrap a base system:

```sh
hxsbootstrap \
    -x \
    -d /mnt \
    -s standard \
    -S efi \
    -v 26a3
```

**Flags**

- `-x`: Use the system `xpt(8)` instead of bootstrapping `xpt.static`
- `-d /mnt`: Bootstrap HxS on `/mnt`
- `-s standard`: Use the `standard` set
- `-S efi`: Install the `efi` subset
- `-v 26a3`: Bootstrap HxS 26a3

## 4. Configuring the Base System

### 4.1 Entering the Base System

**Programs & Packages**:

- `ecw(8)` from `xpt`
- `cp(1)`, `chroot(1)` from the `system:coreutils` provider (`coreutils`, `sbase`, `pxbase`)

First, copy the `/etc/resolv.conf` file from the live system using `cp(1)`:

```sh
cp /etc/resolv.conf /mnt/etc/resolv.conf
```

After bootstrapping, all the system configuration must be made inside a chroot.

The `ecw(8)` program provides a wrapper around `chroot(1)`:

```sh
ecw /mnt
```

### 4.2 Configuring the Shell

**Programs & Packages**:

- `bash(1)` from `bash`
- `dash(1)` from `dash`
- `chsh(1)` from `util-linux`
- `ln(1)` from the `system:coreutils` provider (`coreutils`, `sbase`, `pxbase`)

> [!NOTE]
> All sets use the `dash(1)` shell, except for the `gnu` set, which uses `bash(1)`

For compatibility with most scripts, symlink the installed shell to `/bin/sh` using `ln(1)`:

```sh
ln -sf dash /bin/sh # or `ln -sf bash /bin/sh` for the gnu set
```

Set the `root` user's shell using `chsh(1)`:

```sh
chsh -s /bin/dash root # or /bin/bash
```

### 4.3 The Package Manager

**Programs & Packages**

- `xpt(8)` from `xpt`

> [!IMPORTANT]
> Minimal sets ship with a static version of the `xpt` package, this is a trimmed-down version of `xpt` that lacks the networking subsystem entirely. Minimal sets can skip this section.
>
> The complete version of `xpt` depends on: `libarchive`, `openssl`, `zstd`, `pkgconf`, `zlib`, `xz`, `bzip2`, and `musl`.
>
> To install `xpt` in almost any HxS system, write the main repository to `/etc/xpt.d/index`, then exit the chroot, as outlined in [6. Reboot](installation.md#6-reboot). Then, use the following commands to refresh the Package Database, uninstall `xpt.static`, and install `xpt`:
>
> ```sh
> xpt -d /mnt -s
> xpt -d /mnt -rp xpt.static
> xpt -d /mnt -in xpt
> ```
>
> Then, return to the chroot using `ecw(8)` as outlined in [4.1 Entering the Base System](installation.md#41-entering-the-base-system)

The newly installed system does not provide any repositories, nor does it have a configured `xpt(8)`.

Similarly to [1.2 XPT](installation.md#12-xpt), write the `26a3` main repository in `/etc/xpt.d/index`:

```sh
echo "https://main.hxs.juanvel400.com/archive/26a3/x86_64" >/etc/xpt.d/index
```

Synchronize the `xpt(8)` Package Database:

```sh
xpt -s
```

### 4.4 Users

**Programs & Packages**

- `passwd(8)` and `useradd(8)` from `shadow`

First, set the `root` user password using `passwd(8)`:

```sh
passwd root
```

Using the `root` user by default is insecure. Create a regular user for most use cases using `useradd(8)`:

```sh
useradd -m johndoe
```

Assign it a password using `passwd(8)`:

```sh
passwd johndoe
```

### 4.5 The `/etc/inittab` File

**Programs & Packages**:

- `adam(8)` from `adam`
- `cp(1)` from the `system:coreutils` provider (`coreutils`, `sbase`, `pxbase`)
- `vim(1)` from `vim`
- `nano(1)` from `nano`

> [!IMPORTANT]
> The `suckless` and `suckless-minimal` sets do not use `/etc/inittab`. Instead, they use the `/etc/rc.init`, `/etc/rc.poweroff`, and `/etc/rc.reboot` scripts.
>
> Use the [Morpheus Init Scripts](https://git.2f30.org/fs/files.html) as a reference for writing your own.

The `adam(8)` init system uses a file called `inittab` to initialize processes that the system requires to function normally.

Copy `/usr/share/adam/examples/inittab` to `/etc/inittab` using `cp(1)`:

```sh
cp /usr/share/adam/examples/inittab /etc/inittab
```

Use the editor provided by the system to modify `/etc/inittab`:

```sh
vim /etc/inittab
```

> [!NOTE]
> The `gnu` set provides `nano(1)` instead of `vim(1)`, whereas the `suckless`, `minimal`, and `suckless-minimal` sets do not provide an editor at all.

> [!TIP]
> On HxS systems where `xpt(8)` supports networking, install `vim(1)` using:
>
> ```sh
> xpt -in vim
> ```

### 4.6 System Networking

**Programs & Packages**

- `adam(8)` from `adam`
- `dhcpcd(8)` from `dhcpcd`

To identify the system in the network, `adam(8)` reads the `/etc/hostname` file and sets its contents as the system's hostname.

Set the system's hostname in `/etc/hostname` using:

```sh
echo "hxs" >/etc/hostname
```

> [!TIP]
> To execute the DHCP client (`dhcpcd(8)`) on boot, append the following line to `inittab`
>
> ```inittab
> dhcp:2345:respawn:/sbin/dhcpcd -B
> ```

### 4.7 The Filesystem Table

**Programs & Packages**:

- `blkid(8)` from `util-linux`

The `/etc/fstab` file is used to specify which filesystems to mount at boot time.

Each partition is assigned a UUID. To look up all partitions and their UUIDs, use `blkid(8)`:

```sh
blkid
```

Example `/etc/fstab` file:

```fstab
# view fstab(5) from util-linux for further information on the /etc/fstab file
# fs mp fstype mntops dump pass
UUID=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx / ext4 rw,relatime 0 2
UUID=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx /boot/efi vfat rw,relatime,utf8 0 2
UUID=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx none swap sw 0 0
# /usr/swap0 none swap sw 0 0
```

## 5. Making the System Bootable

### 5.1 Installing a Kernel

> [!IMPORTANT]
> There is no Kernel Package available in any of the main repositories, thus the user is expected to provide their own kernel and configure it.
>
> This section is considered TODO

### 5.2 Installing a Bootloader

**Programs & Packages**:

- `grub-mkconfig(8)`, `grub-install(8)` from `grub-efi`

So far, the system is not bootable. The `grub-efi` package provides the `GNU Grand Unified Bootloader`. Install it to the `EFI System Partition` using `grub-install(8)`:

```sh
grub-install \
    --target=x86_64-efi \
    --bootloader-id=HxS \
    --efi-directory=/boot/efi
```

If you attempt to boot to `GRUB`, you will find a bare `grub>` prompt. This is because there are no boot entries configured. Use `grub-mkconfig(8)` to generate a `/boot/grub/grub.cfg` file:

```sh
grub-mkconfig -o /boot/grub/grub.cfg
```

## 6. Reboot

**Programs & Packages**:

- `ecw(8)` from `xpt`
- `reboot(8)` from `adam`
- `umount(8)` from `util-linux`

Exit `ecw(8)` by closing the interactive shell:

```sh
# exit
exit
```

> Alternatively, use the `Ctrl-D` shortcut to send an `EOF` signal to the shell, thus closing it.

If swap was configured and enabled in [2.1 The Swap Partition](installation.md#21-the-swap-partition.md), disable it using `swapoff(8)`:

```sh
swapoff /dev/sda3 # or /mnt/usr/swap0
```

Unmount the filesystem recursively using `umount(8)`:

```sh
umount -R /mnt
```

Use `reboot(8)` from adam to restart the system:

```sh
reboot
```

The first boot is expected to show something similar to the following:

```stdout
HxS 26a3 (x86_64)
hxs login:
```

Type the username, and then the password, of the user created in [4.4 Users](installation.md#44-users). Alternatively, log in as `root`.
