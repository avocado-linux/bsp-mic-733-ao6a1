# Changelog

All notable changes to avocado-bsp-mic-733-ao6a1 are documented in this file.
The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

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
