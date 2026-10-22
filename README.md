# PiShrink

**PiShrink** is a bash script that automatically shrinks a Raspberry Pi `.img` file to its minimum size. Upon booting the shrunken image on a Raspberry Pi, it automatically resizes to fill the maximum capacity of the target SD card.

---

## Features

- **Automatic File System Shrinking**: Reduces the root filesystem to fit only the used data.
- **Auto-Expansion on Boot**: Modifies `/etc/rc.local` inside the image so that the system automatically re-expands the filesystem to fit the whole SD card when booted for the first time.
- **Compression Support**: Option to compress images using `gzip` or `xz` after shrinking.
- **Multi-Core Parallel Compression**: Leverage multiple CPU cores using `pigz` or parallel `xz` settings for faster compression.
- **Image Repair & Recovery**: Built-in filesystem check (`e2fsck`) with optional advanced recovery parameters.
- **Safe Operations**: Option to output to a new file instead of overwriting the original.

---

## Requirements & Dependencies

PiShrink requires a Linux environment with `sudo` / `root` privileges and the following dependencies installed:

* `parted`
* `losetup`
* `tune2fs`
* `md5sum`
* `e2fsck`
* `resize2fs`

### Optional Dependencies (for compression)
* `gzip` / `pigz` (for multi-core `gzip` compression)
* `xz`

### Installation of Dependencies (Debian/Ubuntu/Raspberry Pi OS)

```bash
sudo apt update
sudo apt install parted e2fsck-static tune2fs resize2fs pigz xz-utils
```

---

## Quick Start

1. **Download and make the script executable:**

   ```bash
   wget https://raw.githubusercontent.com/Michdo93/pishrink/refs/heads/main/pishrink.sh
   chmod +x pishrink.sh
   ```

2. **Run PiShrink on an image file (requires root):**

   ```bash
   sudo ./pishrink.sh pi_image.img
   ```

---

## Usage Syntax

```bash
sudo ./pishrink.sh [-adhnrsvzZ] imagefile.img [newimagefile.img]
```

### Arguments

* `imagefile.img`: Path to the input image file.
* `[newimagefile.img]` *(Optional)*: Path for the new, shrunk image file. If omitted, PiShrink will shrink `imagefile.img` in place.

---

## Options / Flags

| Flag | Description |
| :--- | :--- |
| `-s` | **Skip auto-expansion**: Prevents the filesystem from expanding automatically when booted on a Raspberry Pi. |
| `-z` | **Compress with gzip**: Compresses the image using `gzip` after shrinking (`.img.gz`). |
| `-Z` | **Compress with xz**: Compresses the image using `xz` after shrinking (`.img.xz`). |
| `-a` | **Parallel compression**: Uses multiple CPU cores for faster compression (uses `pigz` for gzip or `xz -T0`). |
| `-r` | **Advanced repair**: Uses advanced `e2fsck` recovery options if standard filesystem checks fail. |
| `-v` | **Verbose**: Displays detailed process information. |
| `-d` | **Debug**: Writes debug log output to `pishrink.log` in the current working directory. |
| `-n` | **Disable update check**: Skips checking GitHub for newer versions of PiShrink. |
| `-h` | **Help**: Displays the options menu. |

---

## Example Commands

### 1. Basic In-Place Shrink
Shrinks `raspberry.img` directly and enables auto-expansion on boot:
```bash
sudo ./pishrink.sh raspberry.img
```

### 2. Shrink and Save to a New File
Keeps `original.img` untouched and saves the reduced image to `shrunken.img`:
```bash
sudo ./pishrink.sh original.img shrunken.img
```

### 3. Fast Parallel Compression (`xz`)
Shrinks `pi.img` and compresses it using `xz` across all available CPU cores:
```bash
sudo ./pishrink.sh -Za pi.img
```

### 4. Shrink and Compress (`gzip`) Without Auto-Expansion
Shrinks `pi.img`, compresses it with `gzip`, and prevents auto-expansion on first boot:
```bash
sudo ./pishrink.sh -sz pi.img
```

---

## Environment Variables

Custom default flags for compression tools can be overridden using environment variables:

```bash
# Example: Custom compression flags for Gzip or XZ
export PISHRINK_GZIP="-f9"
export PISHRINK_XZ="-e -9"

sudo -E ./pishrink.sh -z pi.img
```
*(Note: Use `sudo -E` to preserve environment variables across the `sudo` boundary).*

---

## License & Attribution

PiShrink is maintained by Drewsif and the open-source community.  
Source Code: [https://github.com/Drewsif/PiShrink](https://github.com/Drewsif/PiShrink)
