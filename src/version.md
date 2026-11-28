# HxS Version

This Handbook describes version `26a3` of HxS.

> [!NOTE]
> `26a3` is still a living release, as described in [Introduction # 4. Release Model](./introduction.md#4-release-model).

## What's new since `26a2`

- Linux: `7.2.9` -> `7.3.4`
- Added a Kernel Package
- `xpt`: `0.13.2` -> `0.14.0`
- `hxsbootstrap`: `0.1.0` -> `0.2.0`
- Wayland Packages (`wayland`, `wlroots`, `sway`, `dwl`, etc.)
- Tons of new packages added
- `openrc` service management
- Sets: `gnu`
- Subsets: `efi`

## hxs-base

This release uses `hxs-base` 0.3.0.

`hxs-base` contains core system files, including `os-release`, `lsb-release`, a set of default licenses, and other files deemed important for the system.

## Versioning Model

Each HxS release contains an identifier, unlike [Semantic Versioning](https://semver.org), this identifier encodes the year, life cycle, and iteration.

Each component of, e.g. `26a3`, means:

- `26`: The last two digits of the release year, that is, 2026
- `a`: Short for Alpha
- `3`: Third iteration of the Alpha cycle of the year

Where the life cycle can be:

- `a` for Alpha: An early, experimental iteration of the release, expected and prone to breaking
- `b` for Beta: A feature-complete iteration of the release, less prone to breaking
- `rc` for Release Candidate: A Beta release that has the potential of becoming a stable release

Stable releases are published once per year with no life cycle or iteration suffix.

A Long-Term-Support (LTS) release is expected every four years, with security backports for up to two more years, this release also doubles as the year's stable release.

### Example Versions

- `26a3`: 2026 Third Alpha
- `26b1`: 2026 First Beta
- `26rc2`: 2026 Second Release Candidate
- `26`: 2026 Stable
- `30lts`: 2030 LTS
