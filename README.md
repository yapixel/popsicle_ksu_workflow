# Popsicle GKI 6.12 Kernel for Xiaomi (SM8850)

Automated GitHub Actions CI/CD pipeline for building and packaging **Popsicle GKI 6.12 Kernel** with **KernelSU (xxKSU)** and **SuSFS** for **Xiaomi Popsicle (Snapdragon 8 Elite / SM8850)** on Android 16.

---

## 📱 Device & Platform

| Property | Value |
| :--- | :--- |
| **Target Device** | Xiaomi Popsicle |
| **SoC / Platform** | Qualcomm Snapdragon 8 Elite (`SM8850`) |
| **Android Version** | Android 16 |
| **Kernel Base** | Android Generic Kernel Image (`GKI 6.12.x`) |
| **Toolchain** | AOSP LLVM Clang 19 (`r536225`) + Rust 1.82.0 + LLD |

---

## 📦 Release Artifact Naming Convention

Artifact packages follow the standardized Scheme A naming format:

```text
KSU_Popsicle_6.12.<sub_level>+<KSU_VER>[-staging]-<Hook>[-SUSFS_v<SuSFS_VER>]-<YYMMDD>.zip
```

### Examples:
- **`KSU_Popsicle_6.12.23+12000-manual-SUSFS_v2.2.0-260826.zip`**
  *(Manual Security Hooks + SuSFS, GKI 6.12.23, Stable KSU `master` release)*
- **`KSU_Popsicle_6.12.23+12000-manual-260826.zip`**
  *(Manual Security Hooks, GKI 6.12.23, Clean KSU without SuSFS)*
- **`KSU_Popsicle_6.12.23+12055-staging-lsm-SUSFS_v2.2.0-260826.zip`**
  *(LSM Security Hooks + SuSFS, GKI 6.12.23, KSU `staging` pre-release)*

---

## 🪝 Hook Modes & Variant Breakdown

| Variant | Hook Mode | Description | Stealth / Recommendation |
| :--- | :--- | :--- | :--- |
| **`manual-SUSFS`** | Direct Patch (`manual`) | Inlines KSU hooks directly into kernel security functions (`security.c` + `scope-min-v2.3`). | 🛡️ **Highest stealth** (Recommended) |
| **`manual`** | Direct Patch (`manual`) | Clean KernelSU integration via manual patches without SuSFS. | Standard root |
| **`lsm-SUSFS`** | ARM64 BL Hookless (`lsm`) | Uses LSM security hooks with Branch-Link trampoline hooking + SuSFS. | Alternative hookless approach |
| **`lsm`** | ARM64 BL Hookless (`lsm`) | Clean LSM security hooks without SuSFS. | Standard hookless root |

---

## ⚡ Build Features & Enhancements

- **AOSP Clang & Rust Toolchain Caching**: Instant zero-delay toolchain restoration via GitHub Actions cache.
- **Memory Tmpfs (`/dev/shm`)**: Intermediate compiler objects stored in RAM disk to eliminate virtual disk I/O latency.
- **Ccache 90%+ Hit Rate**: Relocatable cache configuration for 3-minute rapid rebuilds.
- **Networking & Routing Optimizations**:
  - TCP Congestion: BBR v1 + FQ default
  - Policy Routing (Table 1066) & 64k IPSet support
  - Google VPN (XFRM) & TTL 64 share compatibility

---

## 📥 Installation

1. Download the flashable AnyKernel3 `.zip` matching your preference from [Releases](../../releases).
2. Flash using any root/kernel manager app:
   - [Kernel Flasher](https://github.com/capntrips/KernelFlasher)
   - [Horizon Kernel Flasher](https://github.com/libxzr/HorizonKernelFlasher)
   - Or flash via custom recovery (TWRP).
3. Reboot your device.

---

## 📜 Disclaimer

Flashing custom kernels and modifying system partitions involves inherent risks. Please make sure you have backups before proceeding. **Proceed at your own risk.**

---

## 🙏 Credits & Acknowledgements

- **KernelSU**: [tiann](https://github.com/tiann/KernelSU) & [backslashxx (xxKSU)](https://github.com/backslashxx/KernelSU)
- **SuSFS**: [simonpunk](https://gitlab.com/simonpunk/susfs4ksu)
- **AnyKernel3**: [osm0sis](https://github.com/osm0sis/AnyKernel3)
