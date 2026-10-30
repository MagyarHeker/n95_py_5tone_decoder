# alpha 1.4 test report

## Validation split
- PC: done (below).
- Physical Nokia N95 8GB: reported by the author as tested for alpha 1.0 through 1.4 (not performed by the test suite or by the assistant that wrote it). No other device tested.
- Python 2.2.2: no interpreter available. Checked by AST scan (no list/generator comprehensions, conditional expressions, with, f-strings, try/except/finally, decorators, sum/enumerate/set/sorted/any/all, str.format/partition, key=, multi-arg print) plus manual review. The scan was self-tested on a deliberately bad file.

## Results: `python tests/test_zvei.py` -> 66 checks, 66 passed
- Reference WAV @ 40 ms: seven `186` at 2.08, 2.68, 3.22, 3.75, 4.28, 4.82, 5.35 s; output and diagnostics identical to alpha 1.3; first `1` accepted; leading `9` rejected; reference file hash unchanged.
- 14 generated fixtures (clean, 5-tone calls, E marker, repeat gaps, 30/50/70/100 ms, amplitude jitter, +1 % and -1.5 % offset, 32-burst log-cap file, quiet, clipped): decoded as expected and identical to alpha 1.3.
- E marker: raw `12E45`, expanded `12245`, both shown.
- Caps: 50 history entries (newest first) and 100 diagnostics enforced; 60 rapid identical bursts -> 1 entry.
- No write-mode file opens during decode/format/history.
- Phone UI functions run against stub `appuifw/e32/audio`: result text, history details, decoder info, stage labels (READING WAV, DECODING, DECODING 100%, STOPPING RECORDING, RECORDING STOPPED, CLOSING WAV, WAV READY).
- Level warnings: reference no warning (peak 100 %, 0.003 % at full scale); quiet -> LOW LEVEL; clipped -> CLIPPING.
- Frequency offset: 18 synthetic cases (-1.5..+1.0 %), mean abs error 0.06 %, max 0.50 %; reference +0.1 %.

## Noise sweep (`python tests/noise_sweep.py`, informational) - see noise_sweep_results.md
Reference + noise: 100 % exact down to ~15 dB SNR, 93-98 % at 6-12 dB, ~66-70 % at 3 dB, ~30 % at 0 dB. Every failure seen at 3-12 dB white noise was the first tone dropped (`186` -> `86`). Synthetic clean calls: 100 % down to 0 dB. Thresholds not changed.

## Open items for the N95
- Added CPU on the phone: level peak scan (built-in max/min), a per-sample clip count only when the peak is >= 32000 (up to ~80k iterations for 10 s; the reference WAV triggers it), and 5 short Goertzels per accepted tone. Expected to be well under a second; not timed on the device.
