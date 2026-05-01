# expand

Expand LVM filesystems on Ubuntu VMs after increasing disk size in Proxmox.

## Quick Start

```bash
curl -sL solublespork.github.io/expand | sudo bash
```

> **Note:** The `-L` flag is required (follows redirects). The `-s` is optional (hides progress bar).

The tool scans your system, shows what can be expanded, and lets you choose.

## Options

For fully automatic expansion (no prompts):
```bash
curl -sL solublespork.github.io/expand | sudo bash -s -- --auto
```

## What It Does

After expanding a VM disk in Proxmox, the guest OS doesn't automatically use the new space. This tool runs the three commands needed:

1. `pvresize` - Expand the LVM Physical Volume
2. `lvextend` - Extend the Logical Volume
3. `resize2fs` / `xfs_growfs` / `btrfs resize` - Grow the filesystem

## Example

```
expand v1.0.0

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
  1. pvresize /dev/sda3
  2. lvextend -l +100%FREE /dev/mapper/ubuntu--vg-ubuntu--lv
  3. resize2fs /dev/mapper/ubuntu--vg-ubuntu--lv

? Proceed? [y/N] y
    Running: pvresize /dev/sda3
    Running: lvextend -l +100%FREE /dev/mapper/ubuntu--vg-ubuntu--lv
    Running: resize2fs /dev/mapper/ubuntu--vg-ubuntu--lv

✓ Expansion complete!
```

## Typical Workflow

1. Expand VM disk in Proxmox (VM → Hardware → Hard Disk → Resize)
2. SSH into the VM
3. Run: `curl -sL solublespork.github.io/expand | sudo bash`
4. Select the volume to expand (or use `--auto`)
5. Done!

## Supported

- Filesystems: ext4, xfs, btrfs
- Requires: Ubuntu/Debian with LVM, root access

## License

MIT
