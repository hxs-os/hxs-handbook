# System Recovery

The HxS system, like any other system, is prone to breaking, this chapter describes how to create a HxS System Recovery Disk.

## 1. Creating a Complete System Recovery Disk

### 1.1 Bootstrapping HxS

**Programs & Packages**:

- `hxsbootstrap(1)` from `hxsbootstrap`
- `rm(1)`, and `ln(1)` from the `system:coreutils` provider (`coreutils`, `sbase`, `pxbase`)

An HxS System Recovery disk is highly recommended to use the `gnu` set.

Use `hxsbootstrap(1)` to build an empty base system:

```sh
hxsbootstrap \
    -x \
    -d ./rootfs.recovery \
    -s gnu \
    -S efi \
    -v 26a3
```

The packages provided by the `gnu` set should be able to satisfy most necessities of the recovery environment.

Clean up the hxsbootstrap cache using `rm(1)`:

```sh
rm -rf ./rootfs.recovery/var/cache/hxsbootstrap
```

Link `bash(1)` to `sh(1)` using `ln(1)`:

```sh
ln -sf bash ./rootfs.recovery/bin/sh
```

### 1.2 Configurate `/etc/inittab` and `adam(8)`

**Programs & Packages**:

- `adam(8)` from `adam`

Set up a minimal `/etc/inittab` that sets the runlevel to `S` (single-user) and executes a respawning `/bin/sh` instance:

```inittab
id:S:initdefault:
sh:S:respawn:/bin/sh
```

Link `/init` to `/usr/sbin/adam` using `ln(1)`. So the kernel can recognize it without extra cmdline arguments:

```sh
ln -sf usr/sbin/adam ./rootfs.recovery/init
```

### 1.3 Packaging the Root Filesystem into an `initramfs` Image

**Programs & Packages**:

- `cpio(1)` from `libarchive`
- `gzip(1)` from `gzip`
- `find(1)` from `findutils`

Since there is no root disk to mount, the kernel will load an archive named `initramfs`, this archive contains the root filesystem previously built in [1.1 Bootstrapping HxS](recovery.md.md#11-bootstrapping-hxs), which the kernel will unpack into memory and use as root.

Use `find(1)` to list all files in the root filesystem, piped to `cpio(1)`, which will create a `newc` archive, piped to `gzip(1)`, which will compress the final archive:

```sh
(
cd ./rootfs.recovery
find . -print0 | \
    cpio -0 -o -newc |
    gzip > ../initramfs.cpio.gz
)
```

> [!NOTE]
> Ensure the kernel has enabled the following features during build time
>
> `CONFIG_BLK_DEV_INITRD=y`: Support `initramfs` images
> `CONFIG_RD_GZIP=y`: Support for `gzip`-compressed initramfs images (`.gz`)

### 1.4 Creating an ISO using `grub-mkrescue(1)`

**Programs & Packages**:

- `grub-mkrescue(1)` from `grub-efi`
- `cp(1)` and `mkdir(1)` from the `system:coreutils` provider (`coreutils`, `sbase`, `pxbase`)

Create the basic directory tree for the iso using `mkdir(1)`:

```sh
mkdir -p ./iso.recovery/boot/grub
```

Copy the host's kernel to the ISO workspace using `cp(1)`:

```sh
cp /boot/vmlinuz-linux ./iso.recovery/boot/vmlinuz-linux
```

Copy the built `initramfs.cpio.gz` to the ISO workspace:

```sh
cp ./initramfs.cpio.gz ./iso.recovery/boot/initramfs.cpio.gz
```

To add a menu entry on `GRUB`, create a `/boot/grub/grub.cfg` inside the ISO workspace:

```cfg
set timeout=5

menuentry 'HxS 26a3 Recovery' --id=hxs-recovery {
  echo 'Loading HxS 26a3 Recovery...'
  linux /boot/vmlinuz-linux
  initrd /boot/initramfs.cpio.gz
}
```

Finally, use `grub-mkrescue(1)` to build an ISO image:

```sh
grub-mkrescue -o hxs.recovery-26a3-x86_64.iso ./iso.recovery
```

> [!NOTE]
> `grub-mkrescue(1)` depends on `xorriso`
