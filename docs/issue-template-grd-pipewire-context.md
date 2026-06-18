# Title

PipeWire activation context mismatch in RDP audio fallback path (`pw_stream_set_active called from wrong context`)

# Environment

- Fedora 44 / GNOME Remote Desktop 50.1
- PipeWire 1.6.6
- WirePlumber 0.5.14
- RDP client path where AUDIO_PLAYBACK_DVC may fail and fallback is used

# Observed Behavior

Intermittently, audio setup reports:

`pw_stream_set_active called from wrong context, check thread and locking`

When this happens, audio may not reach the client despite successful initial protocol steps.

# Expected Behavior

PipeWire stream activation/deactivation should occur from a valid context/thread for PipeWire API usage, and fallback audio should remain stable.

# Suspected Cause

`pw_stream_set_active()` can be invoked from a context that is not safe for direct PipeWire stream state transitions in the current control flow.

# Proposed Direction

Defer stream activation changes into a safe main-loop context (for example via idle callback / event dispatch) to avoid wrong-context calls.

# Impact

This affects interoperability and reliability in mixed-client environments where fallback paths are important.

# Additional Context

A local workaround confirms the hypothesis by eliminating the wrong-context log and restoring stable behavior in test runs.
