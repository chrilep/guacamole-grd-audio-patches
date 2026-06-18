# Title

RDP/rdpsnd: add compatibility for modern GRD audio flow (`SNDC_WAVE2`) and protocol negotiation

# Summary

This PR improves Guacamole interoperability with modern GNOME Remote Desktop (GRD) / FreeRDP 3 server-side audio behavior.

In affected environments, audio negotiation succeeds, but audio payload delivery may fail because the server sends `SNDC_WAVE2` PDUs when the negotiated RDPSND client version is >= 8, while Guacamole currently handles only classic `SNDC_WAVE` + `SNDWAV` flow.

# Changes

- Align RDPSND protocol negotiation with current GRD expectations (version compatibility)
- Add handling for `SNDC_WAVE2` payloads
- Preserve existing `SNDC_WAVE` flow
- Keep wave confirmation behavior (`SNDC_WAVECONFIRM`) intact

# Reproduction

1. Connect Guacamole to Fedora GRD (FreeRDP 3 based server stack)
2. Observe that video works but browser audio is absent
3. Check server logs showing successful format negotiation and training confirm
4. Observe lack of usable audio payload handling without `SNDC_WAVE2` support

# Validation

- Verified that browser session receives both video and audio
- No regression observed for standard session establishment

# Notes

A separate small crash guard fix (division by zero in RDP sizing path) can be reviewed independently in a dedicated PR to keep this PR focused.
