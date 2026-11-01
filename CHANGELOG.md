# Changelog

All notable changes to avocado-bsp-mic-733-ao6a1 are documented in this file.
The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [0.2.1]

### Fixed
- **Publishes to the 2026 feed under the target name that feed actually has.**
  Both release legs named `jetson-agx-orin-devkit`. That target exists in the
  2024 feed but not in 2026, so the 2026 leg landed in an orphan
  `jetson-agx-orin-devkit-ext` repo that no 2026 device resolves, and
  `2026/next/target/jetson-agx-orin-ext` carried no carrier support at all.
  The 2026 leg now names `jetson-agx-orin`.

### Added
- `test.yml` gains the 2026 leg, so the combination that was broken is the one
  CI checks.
- Ethernet: `igb` for the I350 quad 1GbE that some units carry on C8, next to
  `ixgbe` for the X550. On 2026 this needs avocado-linux/meta-avocado#441
  (`CONFIG_IXGBE`/`CONFIG_IGB` on linux-yocto 6.18).
- CAN: `mttcan`, `can-dev` and `can-raw` for the two MTTCAN controllers.
- I2C on the L4T 6.8 kernel (`i2c-tegra`, `i2c-tegra-bpmp`, `i2c-dev`), where
  I2C is modular, and `modprobe: i2c-dev` so `/dev/i2c-*` exists on every
  kernel.
- nvidia-drm KMS on the 2026 kernels: `nvidia-drm-loadconf` (modeset=1,
  fbdev=1), with `nvidia-drm` in `modprobe` because the package's
  modules-load.d entry arrives after systemd-modules-load has run. Needs
  avocado-linux/meta-avocado#445. 2024 devices do not get the options file and
  keep their display behavior.

### Changed
- The 2024 feed now uses the target name `jetson-agx-orin`, as the 2026 feed
  does: meta-avocado scarthgap renamed `jetson-agx-orin-devkit` to
  `jetson-agx-orin`. `supported_targets` lists the one name, and CI builds and
  releases it for both feeds. A 2024 project must set
  `default_target: jetson-agx-orin`.
- The kernel DTB is now the stock r39 `tegra234-p3737-0000+p3701-0005-nv.dtb`
  plus a carrier overlay (`tegra234-mic-733-carrier.dtbo`) that enables the
  carrier's PCIe NIC controllers. Advantech's vendor DTB predates r39 and makes
  the r39 UEFI assert in PcieControllerDxe and reset in a loop. This applies to
  both feeds.
- The 2026 package set resolves on both 2026 kernels (6.18.35 and
  6.8.12-l4t-r39.2.0): the `nv-kernel-module-snd-soc-*` packages are now
  `kernel-module-snd-soc-*`, and `tegra-nvphs`, `tegra-nvstartup` and
  `crct10dif-ce` move into the per-kernel blocks of the kernels that have them.

## [0.1.1]

### Fixed
- Restore ethernet ports 3 & 4 by enabling the carrier's second Intel X550
  dual-port NIC on PCIe controller C8 (`pcie@141e0000`). Set
  `CARRIER_ENV_ODMDATA` to the factory GBE-UPHY mapping
  (`gbe-uphy-config-0` / `hsio-uphy-config-16`) so MB1 powers C8's UPHY lanes,
  and re-enable `pcie@141e0000` in the kernel DTB (now byte-identical to the
  vendor DTB). A prior revision had disabled C8 to work around a RAS UE, which
  dropped two of the four 10GbE ports. Requires a full reflash.

### Changed
- Correct BSP docs: the four front 10GbE ports are two Intel X550 dual-port
  NICs on PCIe C7/C8 driven by `ixgbe`, not on-SoC MGBE.

## [0.1.0]

### Added
- Initial release: Board support for the Advantech MIC-733-AO6A1 (carrier extension on jetson-agx-orin).
- CI via the shared `avocado-linux/actions` reusable workflows: PR build check
  (`test.yml`) and tag-driven package + publish to the Avocado feed (`release.yml`).
