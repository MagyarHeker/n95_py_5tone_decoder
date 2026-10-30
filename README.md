# N95 ZVEI-1 decoder

A ZVEI-1 five-tone selective-call decoder for the Nokia N95, written in pure Python for PyS60 (Python for S60) 1.4.5 / Python 2.2.2. It records audio from the phone's microphone (for example from a radio speaker), saves it as a WAV and decodes the tone sequences. It also runs on a PC with Python 3 for testing.

Author: **atakusi** - Release 1 (version string in the app: alpha 1.4) - MIT License

## Important: device compatibility

**This has been tested only on a Nokia N95 8GB** (alpha 1.0 through 1.4). It is **not guaranteed to work on your device**, including other N95 variants or other Symbian S60 3rd edition phones. Microphone recording, file paths, memory and speed differ between devices and firmware. Use it at your own risk, and do not rely on it for anything safety-critical.

## Features

- Record 1-20 s (default 10 s) and decode, or re-decode the last saved WAV.
- ZVEI-1 tone table, default tone length **40 ms** (adjustable 30-200 ms).
- Distorted first tones are kept, short false tones are rejected (run-based acceptance with purity and match scores).
- Repeated identical bursts are collapsed: `2.08-5.35s  186 x7`.
- ZVEI `E` repeat marker: raw tones and expanded sequence are both shown.
- Call history (newest 50) and a signal-quality log (latest 100 candidate tones), kept in RAM only and cleared when you exit.
- Input level check (peak, CLIPPING / LOW LEVEL warning) and per-call frequency offset.
- Processing stages shown in the title bar (STOPPING RECORDING, CLOSING WAV, READING WAV, DECODING xx%, ...).
- Nothing is saved except the WAV (`E:\zvei.wav`, or `C:\Data\zvei.wav` if there is no E: drive).

## Install on the phone

1. Install Python for S60 (PyS60) 1.4.5 on the phone.
2. Copy `zvei_decoder.py` to the folder your PyS60 installation runs scripts from (commonly `E:\Python` or `C:\Python`).
3. Start the Python application on the phone, choose `zvei_decoder.py` and run it.

## Use

Menu entries:

- **Record + decode** - records for the set number of seconds, then decodes.
- **Decode last WAV** - decodes the saved WAV again (for example after changing the tone length).
- **Call history** - decoded calls, newest first; select one for details (raw tones, position, repeats, frequency offset, level, signal quality).
- **Signal quality log** - every candidate tone from the last decode, accepted or rejected, with purity and match percentages.
- **Clear call history**
- **Tone ms** - tone length, 30-200. Keep 40 unless your system uses another length.
- **Record secs** - 1-20.
- **Decoder information**

Tips: start recording a moment before the transmission (leading silence is fine), keep the volume high enough to be clean but not clipped, and check the Level line in the result.

## Use on a PC

    python zvei_decoder.py recording.wav [tone_ms] [flat]

`flat` prints one line per burst instead of collapsing repeats. Input: standard PCM WAV, 8 or 16 bit (first channel used).


PC tests (need Python 3 and NumPy):

    python tests/test_zvei.py        # 66 regression checks
    python tests/noise_sweep.py      # noise-margin sweep (informational)

`baseline/` holds alpha 1.3, which the tests compare against.

## Known limitations

- ZVEI-1 only.
- One recording is decoded at a time; a call cut off by the start or end of the recording is not recovered.
- The first tone of a transmission is sometimes missed (for example `186` read as `86`). It is often distorted by the radio chain and sits near the acceptance threshold.
- The `D` tone is 885 Hz, as in the original script.
- Purity, match, level and frequency-offset figures are heuristics, not calibrated measurements.

See `CHANGELOG.md` for details and test results.

## License

MIT - see `LICENSE`. Copyright (c) 2026 atakusi.
