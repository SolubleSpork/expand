# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).

## [Unreleased]

## [2.0.0] - 2026-07-08

### Added
- Support for plain (non-LVM) partitions, not just LVM-managed volumes

### Fixed
- Script no longer aborts when `growpart` reports nothing to do (`NOCHANGE`), which was the normal outcome on every run after the first

## [1.1.0] - 2026-07-08

### Added
- Automatic partition/PV growth (`growpart` + `pvresize`) before scanning, so a Proxmox disk resize no longer requires a manual step first

## [1.0.0] - 2026-05-01

### Added
- Initial release: curl-pipe installer that expands LVM filesystems on Ubuntu VMs after a Proxmox disk resize

### Changed
- Confirmation prompts default to Yes
- Command-line options simplified to not require dashes (`auto`, `dry-run`, `help`)
- Read from `/dev/tty` instead of stdin so interactive prompts work when piped
