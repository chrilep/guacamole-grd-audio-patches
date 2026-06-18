# Upstream Plan (English)

## Scope

This work spans two upstream projects:

- Apache Guacamole (`apache/guacamole-server`)
- GNOME Remote Desktop (`GNOME/gnome-remote-desktop`)

## Guacamole PR Plan

### PR 1: RDP settings crash guard (small bugfix)

- File area: RDP settings sizing logic
- Fix: protect division by zero when `optimal_resolution == 0`
- Goal: eliminate SIGFPE class crash in specific negotiation paths

### PR 2: RDPSND compatibility with modern GRD / FreeRDP 3 server behavior

- File area: `src/protocols/rdp/channels/rdpsnd/*`
- Add support for `SNDC_WAVE2` PDU decode path
- Keep wave confirmation behavior correct (`SNDC_WAVECONFIRM`)
- Maintain backward compatibility with classic `SNDC_WAVE`/`SNDWAV`

## GNOME Remote Desktop Issue / MR Plan

### Issue: PipeWire activation context/threading during audio fallback

Observed log signature:

- `pw_stream_set_active called from wrong context, check thread and locking`

Impact:

- Audio source setup may fail intermittently in SVC fallback path.

Proposed direction:

- Ensure stream active/inactive transitions execute in the proper PipeWire/main-loop context.

## Evidence to Attach

- Before/after logs showing:
  - `Client Formats` negotiation
  - Training confirm
  - PipeWire stream transitions to `streaming`
  - final successful audio behavior in browser session

## PR Hygiene

- Keep commits focused and independently reviewable
- One behavioral change per commit
- Include minimal regression notes in each PR body
- Use English-only commit messages and discussion text
