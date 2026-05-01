# expand

A simple tool to expand LVM filesystems on Ubuntu VMs after increasing disk size in Proxmox/hypervisor.

## Quick Start

```bash
curl -sL solublespork.github.io/expand | sudo bash
```

That's it. The tool will guide you through expanding your filesystems.

## Usage

### Interactive Mode (default)

```bash
curl -sL solublespork.github.io/expand | sudo bash
```

Shows your current disk layout, identifies expansion opportunities, and lets you choose what to expand.

### Auto Mode

```bash
curl -sL solublespork.github.io/expand | sudo bash -s -- --auto
```

Automatically expands all PVs, LVs, and filesystems without prompts.

### Dry Run

```bash
curl -sL solublespork.github.io/expand | sudo bash -s -- --dry-run
```

Shows what would happen without making any changes. Great for testing.

### Combined

```bash
curl -sL solublespork.github.io/expand | sudo bash -s -- --auto --dry-run
```

## What It Does

After you expand a virtual disk in Proxmox (or any hypervisor), the guest OS doesn't automatically use the new space. This tool handles the three steps needed:

1. **`pvresize`** - Expands the LVM Physical Volume to use the larger disk/partition
2. **`lvextend`** - Extends the Logical Volume to use the free space in the Volume Group
3. **`resize2fs`/`xfs_growfs`/`btrfs resize`** - Grows the filesystem to fill the Logical Volume

## Supported Filesystems

- ext4 (and ext3/ext2)
- XFS
- Btrfs

## Requirements

- Ubuntu (or Debian-based) with LVM
- Root access (sudo)
- LVM tools installed (usually pre-installed: `apt install lvm2`)

## Example Output

```
╔═══════════════════════════════════════════════════════════╗
║         expand - LVM Filesystem Expansion Tool           ║
║                      v1.0.0                              ║
╚═══════════════════════════════════════════════════════════╝

=== Disk Layout (lsblk) ===

NAME                      SIZE  TYPE FSTYPE      MOUNTPOINT
sda                        50G  disk
├─sda1                      1M  part
├─sda2                      2G  part ext4        /boot
└─sda3                     48G  part LVM2_member
  └─ubuntu--vg-ubuntu--lv  24G  lvm  ext4        /

=== Filesystem Usage (df -h) ===

Filesystem                         Size  Used Avail Use% Mounted on
/dev/mapper/ubuntu--vg-ubuntu--lv   24G   12G   11G  53% /

=== Expansion Analysis ===

Checking Volume Groups for free space...
  VG 'ubuntu-vg' has 24G free space available!

=== Available Actions ===

  1) Resize PV: /dev/sda3 (48G) → VG: ubuntu-vg
  2) Extend LV: /dev/mapper/ubuntu--vg-ubuntu--lv (24G, ext4) mounted at /

  a) Expand ALL
  q) Quit

? Select an option:
```

## Typical Workflow

1. Expand VM disk in Proxmox (VM → Hardware → Hard Disk → Resize)
2. SSH into the VM
3. Run: `curl -sL solublespork.github.io/expand | sudo bash`
4. Select option `a` to expand all, or pick specific items
5. Done!

## Local Installation

If you prefer to download the script:

```bash
wget https://raw.githubusercontent.com/SolubleSpork/expand/main/index.html -O expand
chmod +x expand
sudo ./expand
```

## License

MIT License - see [LICENSE](LICENSE)

## Contributing

Issues and PRs welcome at [github.com/SolubleSpork/expand](https://github.com/SolubleSpork/expand)
