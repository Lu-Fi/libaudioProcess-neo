# Changelog (Lu-Fi fork of libaudioProcess-neo)

## 2026-11-25: AEC skips the WebRTC startup phase (Lu-Fi/openimp #640)

- `audio_process_aec_create` (src/aec.c) clears `startup_phase` after the filter set-up.  The Ingenic audio
  driver delivers the far-end reference time-aligned with the capture, with a lead of either ~0 or ~12 ms that
  is fixed per stream start.  The startup phase rewinds the far-end buffer by its own delay estimate; with the
  0 ms lead the filter then never converged for the whole stream (flat ~17-26 dB echo attenuation, T10 box:
  5 of 7 runs with OpenIMP, 2 of 7 with the vendor libimp - both use this library).  Without the rewind both
  lead states converge (host replay sweep: 32-37 dB for leads 0..40 ms; the first 3 s are slower at 12 ms).
- Improvement over the vendor behaviour (the vendor stack on a thingino image uses the same library and gains
  the same); root cause and sweep: openimp issue #640.
