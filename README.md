# expand

Expand filesystems on Ubuntu VMs after increasing disk size in Proxmox.
Works with both LVM and plain-partition (non-LVM) layouts.

## Features

- Single curl-pipe install, no dependencies to manage
- Auto-detects LVM vs. plain-partition layouts
- Supports ext4, xfs, and btrfs
- Interactive mode (choose what to expand) or `auto` mode (expand everything)
- `dry-run` mode to preview changes before making them

## Quick Start

```bash
curl -sL solublespork.github.io/expand | sudo bash
```

> **Note:** The `-L` flag is required (follows redirects). The `-s` is optional (hides progress bar).

The tool scans your system, shows what can be expanded, and lets you choose.

## Auto Mode

To skip prompts and expand everything automatically:
```bash
curl -sL solublespork.github.io/expand | sudo bash -s auto
```

## What It Does

After expanding a VM disk in Proxmox, the guest OS doesn't automatically use the new space. This tool runs the steps needed, automatically detecting whether you're using LVM or plain partitions:

1. `growpart` - Grow the partition to match the resized disk (+ `pvresize` if using LVM)
2. `lvextend` - Extend the Logical Volume (LVM only)
3. `resize2fs` / `xfs_growfs` / `btrfs resize` - Grow the filesystem

## Example

```
expand v2.0.0

Syncing partition/LVM layers... done.
Scanning... done.

Found 1 volume that can be expanded:

  1) / (ext4)
     /dev/mapper/ubuntu--vg-ubuntu--lv
     Filesystem: 48G → could be 100G (+52G)
     Currently using: 16G (33%)

  q) Quit

Select [1/q]: 1

Expanding: /
/dev/mapper/ubuntu--vg-ubuntu--lv

Steps:
  1. lvextend -l +100%FREE /dev/mapper/ubuntu--vg-ubuntu--lv
  2. resize2fs /dev/mapper/ubuntu--vg-ubuntu--lv

? Proceed? [y/N] y
    Running: lvextend -l +100%FREE /dev/mapper/ubuntu--vg-ubuntu--lv
    Running: resize2fs /dev/mapper/ubuntu--vg-ubuntu--lv

✓ Expansion complete!
```

Partition/PV growth (`growpart`/`pvresize`) now happens automatically during the "Syncing" step, before the scan, so it no longer shows up as a per-volume step you have to confirm.

## Typical Workflow

1. Expand VM disk in Proxmox (VM → Hardware → Hard Disk → Resize)
2. SSH into the VM
3. Run: `curl -sL solublespork.github.io/expand | sudo bash`
4. Select the volume to expand (or use `auto`)
5. Done!

## Requirements

- Filesystems: ext4, xfs, btrfs
- Layouts: LVM or plain partitions
- Requires: Ubuntu/Debian, `cloud-guest-utils` (for `growpart`, preinstalled on Ubuntu cloud images), root access. `lvm2` only needed if you're actually using LVM.

## Usage

```
$ curl -sL solublespork.github.io/expand | sudo bash -s help

expand v2.0.0 - Filesystem Expansion Tool (LVM and plain partitions)

Usage:
  curl -sL solublespork.github.io/expand | sudo bash
  curl -sL solublespork.github.io/expand | sudo bash -s auto
  curl -sL solublespork.github.io/expand | sudo bash -s dry-run

Options:
  auto       Expand everything automatically (no prompts)
  dry-run    Show what would happen without making changes
  help       Show this help

What it does:
  1. growpart (+ pvresize if using LVM) - Grow the partition to match the disk
  2. lvextend  - Extend LV to use free VG space (LVM only)
  3. resize2fs/xfs_growfs/btrfs resize - Grow the filesystem
```

```
$ curl -sL solublespork.github.io/expand | sudo bash -s --version
expand v2.0.0
```

## Development & Releases

`main` is both the working branch and what the installer fetches directly — there's no separate release branch. Changes are developed and tested locally; nothing reaches GitHub until it's pushed, and that push is itself what ships it, so:

```bash
curl -sL solublespork.github.io/expand | sudo bash
```

always installs whatever was last pushed to `main`.

## License

MIT
