# Guacamole + GNOME Remote Desktop Audio Interoperability Patches

This repository contains reproducible patches, build instructions, and upstream contribution notes for making browser audio work reliably with:

- Apache Guacamole (guacd based on FreeRDP 3)
- GNOME Remote Desktop (GRD 50.x on Fedora)
- RDP audio via `rdpsnd` static virtual channel fallback

## Problem Summary

Observed behavior during testing:

- `mstsc` (Windows RDP client): video + audio worked
- Guacamole: video worked, audio missing

Root causes identified:

1. Guacamole advertised an RDPSND client protocol version that was too old for current GRD behavior.
2. After version negotiation was fixed, GRD sent audio as `SNDC_WAVE2` for version >= 8, while Guacamole handled only classic `SNDC_WAVE` / `SNDWAV` flow.
3. On GRD side, a PipeWire activation race (`pw_stream_set_active called from wrong context`) affected SVC fallback reliability in some runs.

## What Is Included Here

- `Dockerfile.guacd`: reproducible guacd build with applied compatibility patches
- `patches/guacamole-server/`: focused patch files for upstreaming
- `patches/gnome-remote-desktop/`: GRD workaround/fix patch material
- `docs/upstream-plan.md`: PR/issue strategy and split

## Upstream Contribution Strategy

### Apache Guacamole (`apache/guacamole-server`)

Planned upstream PR split:

1. **RDP settings SIGFPE guard** (`optimal_resolution == 0`)
2. **RDPSND interoperability with GRD / FreeRDP 3**
   - protocol/version negotiation compatibility
   - support for `SNDC_WAVE2` decode + wave confirm handling

### GNOME Remote Desktop (`GNOME/gnome-remote-desktop`)

- Open issue (or MR) for PipeWire context/threading correctness around stream activation.
- Include logs and minimal reproducer from this repo.

## Status

- Local validation: **success** (`guac: video + audio`)
- Public artifacts: this repository
- Next step: publish PRs against upstream projects

## License

Patch files and documentation in this repository are provided under the MIT License unless noted otherwise.
