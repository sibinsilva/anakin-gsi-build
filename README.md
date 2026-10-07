# Lunaris-AOSP 3.12 (Android 16 QPR2 / Baklava) GSI for ROG Phone 5 / 5s

Generic System Image (GSI) built specifically for the **ASUS ROG Phone 5 / 5s (`ASUS_I005D` / `ASUS_I005_1`, codename `anakin`)**, matching Doze-off's build architecture with complete fixes for SELinux boot issues, UDFPS optical sensor coordinates, touch latency, and super partition sizing.

---

## 📦 Download

| File | Size | SHA-256 Checksum |
| :--- | :--- | :--- |
| [anakin_gsi_omni-GAPPS-20261007.img.xz](https://github.com/sibinsilva/anakin-gsi-build/releases/download/v2026.10.07/anakin_gsi_omni-GAPPS-20261007.img.xz) | ~1.7 GB (compressed) | `4645a9fe869487d81d30cde071254cd744cca08dc4a9594b2642adf96fcb96f5` |

---

## 🔧 Build Highlights & Fixes Applied

### 1. SELinux Permissive Domains Whitelisted in User Build
- **The Issue**: In previous local `user` builds, Soong stripped permissive domains, leaving `ueventd` and `phhsu_daemon` in enforcing mode. This broke `/dev` hardware node creation (GPU, touchscreen) and blocked `rw-system.sh` from executing, causing a bootloop into recovery.
- **The Fix**: Added `phhsu_daemon`, `ueventd`, and `tkcore` to `permissive_domains_on_user_builds` in `system/sepolicy/Android.bp` and preserved unconditional permissive rules in `device/phh/treble/sepolicy/`.

### 2. Under-Display Fingerprint Sensor (Goodix UDFPS)
- **Optical Sensor Position**: Corrected physical sensor center coordinates in `vendor/hardware_overlay` (PR #110):
  - X: `540 px`
  - Y: `1874 px` (fixed from the obsolete inverted `610 px`)
  - Radius: `110 px`
- **FOD Triggers**: Extended `rw-system.sh` to trigger ASUS FOD dimming/HBM on `ASUS_I005`.

### 3. Hardware & Gaming Fixups
- **Touch DAC & SELinux**: Installed `rog5-fixups.rc` and relabeled `fts_game_mode` to `vendor_sysfs_touch` with `0664 system:system` DAC permissions for X Mode touch response.
- **Multi-STA Dual Wi-Fi Concurrency**: Merged `Rog5_5s_Wifi` overlay (PR #111) for FastConnect 6900 Wi-Fi concurrency.
- **Display Refresh Rates**: Configured for `60, 90, 120, 144 Hz`.
- **Bypass Charging**: Enabled `BYPASS_CHARGE_SUPPORTED := true`.

---

## 🚀 Flashing Guide (Fastbootd)

### Prerequisites
- ROG Phone 5 / 5s with unlocked bootloader.
- Stock Android 13 firmware (WW-33.0210.0210.229 or later recommended as vendor baseline).
- Working `fastboot` platform-tools on your host PC.

### Step-by-Step Installation

```bash
# 1. Extract the compressed image
unxz -v anakin_gsi_omni-GAPPS-20261007.img.xz

# 2. Reboot phone into fastbootd (userspace fastboot)
adb reboot fastboot
# OR from stock bootloader:
fastboot reboot fastboot

# 3. [CRITICAL] Prevent Super Partition budget overflow on ROG 5:
# Delete the logical product partition on your active slot (e.g. slot A):
fastboot delete-logical-partition product_a
# (If your active slot is B, run: fastboot delete-logical-partition product_b)

# 4. Flash the GSI system image:
fastboot flash system anakin_gsi_omni-GAPPS-20261007.img

# 5. Format userdata (factory reset) and reboot:
fastboot -w
fastboot reboot
```

---

## 📋 Build Details
- **Base**: Lunaris-AOSP 3.12 (Android 16 QPR2 / Baklava)
- **Target Release**: `bp4a` (`BP4A.251205.006`)
- **Variant**: `user`
- **GApps**: Pixel GMS Suite
- **Maintainer**: Sibieeeee
