# Changelog

## Release 1 (version string in the app: alpha 1.4)

First public release by atakusi. Single-file ZVEI-1 five-tone decoder for the Nokia N95 (PyS60 1.4.5 / Python 2.2.2). Also runs on Python 3 for PC testing.

### What it does
- Records microphone audio to a WAV (1-20 s, default 10 s) and decodes it with a pure-Python Goertzel detector. `Decode last WAV` re-decodes the saved file.
- ZVEI-1 tone table, default tone length 40 ms (adjustable 30-200 ms in the menu).
- Call history (newest 50) and a signal-quality log (latest 100 candidate tones) are kept in RAM only and cleared on exit. Decoded results are never written to a file. Only the WAV is saved (`E:\zvei.wav`, or `C:\Data\zvei.wav` if there is no E:).
- ZVEI `E` repeat marker: the raw received tones and the expanded sequence are both kept.
- Processing stages are shown in the title bar: STOPPING RECORDING, RECORDING STOPPED, CLOSING WAV, WAV READY, READING WAV, DECODING, DECODING xx%.
- PC use: `python zvei_decoder.py file.wav [tone_ms] [flat]`

### New in this release (compared with alpha 1.3)
- Identical consecutive bursts are collapsed into one entry with a repeat count, e.g. `2.08-5.35s  186 x7`. `flat` on the PC command line prints one line per burst.
- Input level check: shows the recording's peak level and warns about CLIPPING or LOW LEVEL. Informational only.
- Frequency offset: each accepted tone is measured against its nominal frequency and the call shows the average offset, e.g. `+0.1%`.
- Result, history details and Decoder information show the level and offset; Decoder information shows the version.

### Unchanged from alpha 1.3
Detector, thresholds, ZVEI-1 frequency table, 40 ms default, `E` handling, RAM limits, WAV storage, menu layout and status labels. On the test recordings the decoded output and diagnostics are identical to alpha 1.3.

### Known limitations
- ZVEI-1 only. No other tone systems.
- One recording is decoded at a time. A call cut by the start or end of the recording is not recovered.
- The first tone of a transmission can occasionally be missed (for example `186` read as `86`). That tone is often distorted by the radio chain and sits close to the acceptance threshold (75% average match). Under added noise this was the only failure seen in testing.
- The `D` tone is 885 Hz, as in the original script.
- WAV input: standard PCM, 8 or 16 bit, first channel only.
- Level and frequency-offset figures are heuristics, not calibrated measurements.

### Testing
- Physical device: alpha 1.0 through 1.4 were tested by the author on a **Nokia N95 8GB** running PyS60 1.4.5; a slightly noisy recording decoded with no mistakes. Not tested on any other device. It is not guaranteed to work on other N95 variants or other Symbian S60 3rd edition phones.
- PC: 66 automated checks in `tests/test_zvei.py` pass: decode output identical to alpha 1.3 on a real recording and 14 generated ones, `E` handling, RAM caps, no disk writes, phone UI code with stub modules, level and offset behaviour, and a scan for Python 2.2-incompatible syntax. Python 2.2.2 itself was not available for testing.
- Noise sweep (`tests/noise_sweep.py`, results in `tests/noise_sweep_results.md`): on a real recording with added noise, decoding was about 100% exact down to roughly 15 dB signal-to-noise, 93-98% at 6-12 dB, and 66-70% at 3 dB. Clean synthetic calls decoded fully down to 0 dB.

## alpha 1.3
Run-based tone acceptance (keeps a distorted first tone, rejects short false tones) plus the processing-stage title labels. Kept in `baseline/` for comparison.
