# Getting HxS

## 1. OCI Container Images

The following HxS containers are available through the `ghcr.io` registry

- `juanvel4000/hxs.standard:26a3`: HxS 26a3 with the `standard` flavor
- `juanvel4000/hxs.gnu:26a3`: HxS 26a3 with the `gnu` flavor
- `juanvel4000/hxs.suckless:26a3`: HxS 26a3 with the `suckless` flavor
- `juanvel4000/hxs.minimal:26a3`: HxS 26a3 with the `minimal` flavor
- `juanvel4000/hxs.suckless-minimal:26a3`: HxS 26a3 with the `suckless-minimal` flavor

e.g. with `podman(1)`:

```sh
podman pull ghcr.io/juanvel4000/hxs.standard:26a3
podman run --rm -it ghcr.io/juanvel4000/hxs.standard:26a3
```

## 2. Using `hxsbootstrap(1)`

**Programs & Packages**:

- `hxsbootstrap(1)` from `hxsbootstrap`

The `hxsbootstrap(1)` utility allows bootstrapping a base HxS system by retrieving set lists and installing packages into a directory.

e.g. to bootstrap a HxS `gnu` 26a3 System:

```sh
hxsbootstrap \
    -x \
    -d "$(mktemp -d)" \
    -s gnu \
    -v 26a3
```
